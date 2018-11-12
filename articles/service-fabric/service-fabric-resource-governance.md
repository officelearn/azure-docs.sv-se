---
title: Azure Service Fabric-resursstyrning för behållare och tjänster | Microsoft Docs
description: Azure Service Fabric kan du ange resursgränser för tjänster som körs inom eller utanför behållare.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: twhitney, subramar
ms.openlocfilehash: f2898de030a70d578eb45e81c9ccbef90bce96c8
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300480"
---
# <a name="resource-governance"></a>Resursstyrning 

När du kör flera tjänster på samma nod eller i klustret, är det möjligt att en tjänst kan förbruka mer resurser, begränsar resursanvändningen för andra tjänster i processen. Det här problemet kallas ”resursfördelningen” problemet. Azure Service Fabric kan utvecklare ange reservationer och gränser för tjänsten för att garantera resurser och begränsar resursanvändningen.

> Innan du fortsätter med den här artikeln, rekommenderar vi att du bekanta dig med den [Service Fabric-programmodellen](service-fabric-application-model.md) och [Service Fabric-värdmodell](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Mätvärden för resurs-styrning 

Resursstyrning stöds i Service Fabric i enlighet med den [tjänstpaket](service-fabric-application-model.md). De resurser som har tilldelats tjänstpaketet kan delas mellan kodpaket ytterligare. Resursbegränsningar som anges även innebära reserverade resurser. Service Fabric har stöd för att ange processor och minne per servicepaket, med två inbyggda [mått](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (Måttnamn `servicefabric:/_CpuCores`): en logisk kärna som är tillgängligt på värddatorn. Alla kärnor i alla noder viktas samma.

* *Minne* (Måttnamn `servicefabric:/_MemoryInMB`): minne uttrycks i megabyte och det mappas till fysiskt minne som är tillgänglig på datorn.

För de här två måtten [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) spårar total klusterkapacitet, belastningen på varje nod i klustret och de återstående resurserna i klustret. De här två måtten är likvärdiga med andra användare eller anpassat mått. Alla befintliga funktioner som kan användas med dem:
* Klustret kan vara [belastningsutjämnade](service-fabric-cluster-resource-manager-balancing.md) enligt de här två måtten (standardinställning).
* Klustret kan vara [defragmenteras](service-fabric-cluster-resource-manager-defragmentation-metrics.md) enligt de här två måtten.
* När [beskriva ett kluster](service-fabric-cluster-resource-manager-cluster-description.md), buffrade kapacitet kan anges för de här två måtten.

[Rapportering om dynamisk inläsning](service-fabric-cluster-resource-manager-metrics.md) stöds inte för de här måtten och läser in för de här måtten definieras vid tidpunkten för skapandet.

## <a name="resource-governance-mechanism"></a>Mekanism för resurs-styrning

Service Fabric-körningen ger för närvarande inte reservation för resurser. När en process eller till en behållare öppnas, anger körningen på resursbegränsningar till belastningar som definierades vid tidpunkten för skapandet. Dessutom avvisar körningen öppnandet av nya tjänstpaket som är tillgängliga när resurserna har överskridits. För att bättre förstå hur processen fungerar, låt oss ta ett exempel på en nod med två CPU-kärnor (mekanism för minne styrning är motsvarande):

1. Först placeras en behållare på noden och begär en processorkärna. Körningen öppnas behållaren och CPU-begränsningen till en kärna. Behållaren kommer inte att kunna använda mer än en kärna.

2. Sedan en replik av en tjänst är placerad på noden och motsvarande tjänstpaketet anger en gräns på en processorkärna. Körningen öppnas kodpaketet och anger dess processorgräns till kärna.

Summan av gränser är nu lika med kapaciteten för noden. En process och en behållare som körs med en kärna och inte stör av varandra. Service Fabric placerar inte några fler behållare eller repliker när de anger CPU-begränsning.

Men finns det två situationer där andra processer kan tävla om CPU. I sådana fall är uppstå en process och en behållare från vårt exempel problem med resursfördelningen:

* *Blanda styrt och ej styrda tjänster och behållare*: om en användare skapar en tjänst utan någon resurs-styrning som angetts, av runtime ser det som förbrukar inga resurser och kan placera den på noden i vårt exempel. I det här fallet använder den här nya processen effektivt vissa CPU på bekostnad av tjänster som redan körs på noden. Det finns två lösning på problemet. Antingen inte blanda styrt och ej styrda tjänster i samma kluster eller använda [placeringsbegränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) så att dessa två typer av tjänster inte hamnar på samma uppsättning noder.

* *När en annan process har startats på noden utanför Service Fabric (till exempel en OS-service)*: I det här fallet process utanför Service Fabric också contends för processor med befintliga tjänster. Lösning på problemet är att ställa in nodkapaciteterna korrekt till kontot för OS-kostnader, som du ser i nästa avsnitt.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguration för att aktivera resursstyrning

När en nod startas och ansluter till klustret, Service Fabric identifierar tillgängliga mängden minne och det tillgängliga antalet kärnor och ställer sedan nodkapaciteterna för dessa två resurser. 

Om du vill lämna buffertutrymme för operativsystemet och för andra processer kan köras på noden, Service Fabric använder endast 80% av de tillgängliga resurserna på noden. Procenttalet kan konfigureras och kan ändras i klustermanifestet. 

Här är ett exempel på hur du kan instruera Service Fabric för att använda 50% av tillgängliga CPU och 70% av det tillgängliga minnet: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Om du behöver fullständig manuell installation av nodkapaciteterna kan kan du använda den vanliga mekanismen för att beskriva noder i klustret. Här är ett exempel på hur du ställer in noden med fyra kärnor och 2 GB minne: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

När automatisk identifiering av tillgängliga resurser är aktiverad och nodkapaciteterna är manuellt definierade i klustermanifestet, kontrollerar Service Fabric att noden har tillräckligt med resurser som stöder den kapacitet som användaren har definierat:
* Om nodkapaciteterna som definieras i manifestet är mindre än eller lika med de tillgängliga resurserna på noden, använder Service Fabric kapaciteter som anges i manifestet.

* Service Fabric använder de tillgängliga resurserna som nodkapaciteterna om nodkapaciteterna som definieras i manifestet är större än tillgängliga resurser.

Automatisk identifiering av tillgängliga resurser kan stängas av om det inte krävs. Om du vill inaktivera det, ändrar du följande inställning:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

För optimala prestanda bör också följande inställning aktiveras i klustermanifestet: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Ange resurs-styrning 

Resursgränser för styrning har angetts i manifestet (ServiceManifestImport avsnittet) som visas i följande exempel:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
I det här exemplet heter tjänstpaketet **ServicePackageA** hämtar en kärna på noderna där det är placerat. Det här service-paketet innehåller två kodpaket (**CodeA1** och **CodeA2**), och båda anger den `CpuShares` parametern. Andelen CpuShares 512:256 dividerar kärnan i de två kodpaket. 

Därför i det här exemplet CodeA1 hämtar två tredjedelar av en kärna, och CodeA2 får en tredjedel av en kärna (och en mjuk garanti med samma reservation). Om CpuShares inte har angetts för kodpaket, delas kärnor balanseras mellan dem i Service Fabric.

Minnesgränser är absoluta, så att båda kodpaket är begränsat till 1024 MB minne (och en mjuk garanti med samma reservation). Kodpaket (behållare eller processer) kan inte allokera mer minne än den här gränsen och försök att göra detta leder undantaget minnet är slut. För att tvingande resursbegränsning ska fungera bör minnesbegränsningar ha angetts för alla kodpaket inom ett tjänstpaket.

### <a name="using-application-parameters"></a>Med hjälp av programparametrar

När du anger resursstyrning det är möjligt att använda [programparametrar](service-fabric-manage-multiple-environment-app-configuration.md) att hantera flera konfigurationer. I följande exempel visas användningen av parametrar för program:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

I det här exemplet ställs Standardparametervärden för produktionsmiljön, där varje tjänstpaket skulle få 4 kärnor och 2 GB minne. Det går att ändra standardvärdena med parameterfiler för programmet. I det här exemplet kan en parameterfil användas för att testa programmet lokalt, där det skulle få färre resurser än i produktion: 

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]  Att ange resurs-styrning med programparametrar är tillgängligt från och med Service Fabric version 6.1.<br> 
>
> När programmet parametrar för att ange resurs-styrning, kan Service Fabric inte nedgraderas till en tidigare version än version 6.1. 


## <a name="other-resources-for-containers"></a>Andra resurser för behållare
Förutom CPU och minne är det möjligt att ange andra resursbegränsningar för behållare. Dessa gränser anges på nivån code-package och tillämpas när behållaren har startats. Till skillnad från med CPU och minne, Cluster Resource Manager är inte medveten om dessa resurser, och inte göra några kapacitet kontroller eller belastningsutjämning för dem. 

* *MemorySwapInMB*: mängden swap-minne som kan använda för en behållare.
* *MemoryReservationInMB*: mjuk gränsen för minne styrning som tillämpas endast när minne konkurrens har upptäckts på noden.
* *CpuPercent*: procentandelen av CPU som kan använda för behållaren. Den här parametern ignoreras effektivt om CPU-gränser anges för tjänstpaketet.
* *MaximumIOps*: den högsta IOPS som kan använda för en behållare (läsning och skrivning).
* *MaximumIOBytesps*: den högsta i/o (byte per sekund) som kan använda för en behållare (läsning och skrivning).
* *BlockIOWeight*: block i/o vikt för i förhållande till andra behållare.

Dessa resurser kan kombineras med CPU och minne. Här är ett exempel på hur du kan ange ytterligare resurser för behållare:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Nästa steg
* Mer information om Cluster Resource Manager [introduktion till Service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md).
* Läs mer om programmodellen, servicepaket och kodpaket – och hur repliker som mappar till dem, kan du läsa [modellera ett program i Service Fabric](service-fabric-application-model.md).
