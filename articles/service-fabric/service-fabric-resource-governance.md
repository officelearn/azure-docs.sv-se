---
title: Azure Service Fabric resurs styrning för behållare och tjänster | Microsoft Docs
description: Azure Service Fabric kan du ange gränserna för tjänster som körs inom eller utanför behållare.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 49c7e2c99cce13880781a67806543b1cde0c12b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208020"
---
# <a name="resource-governance"></a>Resursstyrning 

När du använder flera tjänster på samma nod eller kluster, är det möjligt att en tjänst kan förbruka mer resurser, starving andra tjänster i processen. Det här problemet kallas ”störningar granne” problemet. Azure Service Fabric gör det möjligt för utvecklare att ange reservationer och gränser för tjänsten för att garantera att resurser och begränsa Resursanvändning.

> Innan du fortsätter med den här artikeln ska vi rekommenderar att du bekanta dig med de [Service Fabric programmodell](service-fabric-application-model.md) och [Service Fabric värdmodell](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Resursen styrning mått 

Resurs-styrning stöds i Service Fabric i enlighet med den [tjänstpaket](service-fabric-application-model.md). De resurser som har tilldelats tjänstepaketet kan ytterligare delas mellan kod paket. Resurs-gränser som anges också vara reservation av resurser. Service Fabric stöder ange CPU och minne per servicepaket, med två inbyggda [mått](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (måttnamnet `servicefabric:/_CpuCores`): en logisk kärna som är tillgängligt på värddatorn. Alla kärnor på alla noder viktas samma.

* *Minne* (måttnamnet `servicefabric:/_MemoryInMB`): uttrycks minne i megabyte och mappas till det fysiska minne som är tillgängligt på datorn.

För dessa två mätvärden [klustret Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) spårar totala klustret kapacitet, belastningen på varje nod i klustret och de återstående resurserna i klustret. Dessa två mått är likvärdiga med andra användare eller anpassat mått. Alla befintliga funktioner kan användas med dem:
* Klustret kan vara [belastningsutjämnade](service-fabric-cluster-resource-manager-balancing.md) enligt dessa två mått (standardinställning).
* Klustret kan vara [defragmenteras](service-fabric-cluster-resource-manager-defragmentation-metrics.md) enligt dessa två mått.
* När [som beskriver ett kluster](service-fabric-cluster-resource-manager-cluster-description.md), buffrat kapacitet kan ställas in för dessa två mått.

[Dynamisk reporting](service-fabric-cluster-resource-manager-metrics.md) stöds inte för de här måtten och läser in för dessa mått har definierats vid skapandet.

## <a name="resource-governance-mechanism"></a>Mekanism för resurs-styrning

Service Fabric runtime tillhandahåller för närvarande inte reservation för resurser. När en process eller en behållare öppnas, anger körningsmiljön resurs gränser till belastningar som definierades vid skapandet. Dessutom avvisar körningsmiljön öppnandet av nya tjänstpaket som är tillgängliga när resurser har överskridits. För att bättre förstå hur processen fungerar, låt oss ta ett exempel på en nod med två CPU-kärnor (mekanism för minne styrning är likvärdiga):

1. En behållare placeras först på noden, begär en processorkärna. Körningen öppnar behållaren och CPU-begränsningen till en kärna. Behållaren kan inte använda mer än en kärna.

2. Sedan en replik av en tjänst är placerad på noden och motsvarande tjänstepaketet anger en begränsning på en processorkärna. Körningen öppnar kodpaketet och dess processorgräns till en kärna.

Nu är är summan av gränser lika med kapaciteten för noden. En process och en behållare med en kärna och inte stör ut varandra. Service Fabric Placera inte fler behållare eller repliker när de anger CPU-begränsningen.

Det finns två situationer där andra processer kan tävla om CPU. I dessa fall kan en process och en behållare från våra exempel uppstå störningar granne problemet:

* *Blanda styrt och icke-styrs tjänster och behållare*: om en användare skapar en tjänst utan någon resurs styrning anges, körningsmiljön ser som förbrukar inga resurser och kan placera den på noden i vårt exempel. Den här nya process förbrukar i det här fallet effektivt vissa CPU på bekostnad av de tjänster som redan körs på noden. Det finns två lösning på problemet. Ta inte blanda styrt och icke-regleras tjänster på samma kluster eller Använd [placeringen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) så att dessa två typer av tjänster inte slutar på samma uppsättning noder.

* *När en annan process har startats på noden utanför Service Fabric (till exempel en OS-tjänst)*: I det här fallet process utanför Service Fabric också contends för CPU med befintliga tjänster. Lösning på problemet är att konfigurera noden kapaciteter korrekt till kontot för OS kostnader, som visas i nästa avsnitt.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguration för att aktivera resursen styrning

När en nod startas och ansluter till klustret, Service Fabric identifierar tillgängliga mängden minne och det tillgängliga antalet kärnor och anger sedan noden kapacitet för de två resurserna. 

Om du vill lämna buffertutrymme för operativsystemet och andra processer kanske körs på noden, Service Fabric använder endast 80% av de tillgängliga resurserna på noden. Procenttalet kan konfigureras och kan ändras i klustermanifestet. 

Här är ett exempel på hur du instruera Service Fabric 50% av tillgänglig CPU och 70% av tillgängligt minne: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Om du behöver fullständiga manuell installation av noden kapacitet kan du använda mekanismen regelbundet för att beskriva noder i klustret. Här är ett exempel på hur du ställer in noden med fyra kärnor och 2 GB minne: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

När automatisk identifiering av tillgängliga resurser är aktiverad och noden kapaciteter definieras manuellt i klustermanifestet, kontrollerar Service Fabric att noden har tillräckligt med resurser för att stödja den kapacitet som användaren har definierat:
* Om noden kapaciteter som definieras i manifestet är mindre än eller lika med de tillgängliga resurserna på noden, använder Service Fabric kapaciteter som har angetts i manifestet.

* Om noden kapaciteter som definieras i manifestet är större än tillgängliga resurser, använder Service Fabric de tillgängliga resurserna som nod kapacitet.

Automatisk identifiering av tillgängliga resurser kan stängas av om det inte krävs. Om du vill inaktivera, ändra följande inställning:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

För optimala prestanda bör också följande inställning aktiveras i klustermanifestet: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Ange resurs-styrning 

Gränserna för styrning har angetts i applikationsmanifestet (ServiceManifestImport avsnittet) som visas i följande exempel:

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
  
I det här exemplet tjänstepaketet kallas **ServicePackageA** hämtar en core på noderna där den är placerad. Det här service-paketet innehåller två kod paket (**CodeA1** och **CodeA2**), och båda anges den `CpuShares` parametern. Andelen CpuShares 512:256 delar upp grundläggande mellan de två kod paket. 

Därför i det här exemplet CodeA1 hämtar en kärna väger och CodeA2 hämtar en tredjedel av en kärna (och en mjuk garanti reservation av samma). Om CpuShares inte har angetts för koden paket, delas kärnor balanseras mellan Service Fabric.

Minnesgränserna är absolut, så att båda paketen koden är begränsade till 1 024 MB minne (och en mjuk garanti reservation av samma). Koden paket (behållare eller processer) kan inte allokera mer minne än den här gränsen och försök att göra så resulterar i ett undantag i minnet är slut. För att tvingande resursbegränsning ska fungera bör minnesbegränsningar ha angetts för alla kodpaket inom ett tjänstpaket.

### <a name="using-application-parameters"></a>Med hjälp av parametrar för program

När du anger resursen styrning det är möjligt att använda [applikationsparametrarna](service-fabric-manage-multiple-environment-app-configuration.md) att hantera flera konfigurationer för appen. I följande exempel visas användningen av parametrar för program:

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

I det här exemplet ställs Standardparametervärden för produktionsmiljön, där varje servicepaket skulle få 4 kärnor och 2 GB minne. Det är möjligt att ändra standardvärdena med parametern programfiler. I det här exemplet kan en parameterfil användas för att testa programmet lokalt, där det skulle få färre resurser än i produktion: 

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

> [!IMPORTANT] 
> Ange resurs-styrning med parametrar för program är tillgängliga från och med Service Fabric version 6.1.<br> 
>
> När programmet parametrar för att ange resurs styrning, kan Service Fabric inte nedgraderas till en tidigare version än version 6.1. 


## <a name="other-resources-for-containers"></a>Andra resurser för behållare
Förutom CPU och minne är det möjligt att ange andra gränserna för behållare. Dessa värden anges på koden package-nivå och tillämpas när behållaren har startats. Till skillnad från med processor och minne, klustret Resource Manager är inte medveten om dessa resurser, och inte göra några kapacitet-kontroller eller belastningsutjämning för dem. 

* *MemorySwapInMB*: mängden byte minne som en behållare kan använda.
* *MemoryReservationInMB*: mjuk gränsen för minne styrningen som tillämpas endast när konkurrens om minne har identifierats på noden.
* *CpuPercent*: procentandelen av CPU som kan använda för behållaren. Den här parametern ignoreras effektivt om CPU-gränser har angetts för tjänstepaketet.
* *MaximumIOps*: den högsta IOPS som en behållare kan använda (läsning och skrivning).
* *MaximumIOBytesps*: den maximala i/o (byte per sekund) som en behållare kan använda (läsning och skrivning).
* *BlockIOWeight*: blockera IO vikt för i förhållande till andra behållare.

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
* Mer information om kluster Resource Manager [introduktion till Service Fabric-klustret resurshanteraren](service-fabric-cluster-resource-manager-introduction.md).
* Mer information om programmodell, servicepaket och koden paket - och hur repliker mappa till dem, läsa [modellen är ett program i Service Fabric](service-fabric-application-model.md).
