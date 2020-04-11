---
title: Resursstyrning för container och tjänster
description: Azure Service Fabric kan du ange resursgränser för tjänster som körs i eller utanför behållare.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115146"
---
# <a name="resource-governance"></a>Resursstyrning

När du kör flera tjänster på samma nod eller kluster är det möjligt att en tjänst kan förbruka mer resurser och svälta andra tjänster i processen. Detta problem kallas "bullriga granne" problem. Azure Service Fabric gör det möjligt för utvecklaren att ange reservationer och gränser per tjänst för att garantera resurser och begränsa resursanvändningen.

> Innan du fortsätter med den här artikeln rekommenderar vi att du bekantar dig med [programmodellen Service Fabric](service-fabric-application-model.md) och [service fabric-värdmodellen](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Mått för resursstyrning

Resursstyrning stöds i Service Fabric i enlighet med [servicepaketet](service-fabric-application-model.md). De resurser som har tilldelats servicepaketet kan delas upp ytterligare mellan kodpaket. De resursgränser som anges innebär också reservation av resurserna. Service Fabric stöder att ange CPU och minne per servicepaket, med två inbyggda [mått:](service-fabric-cluster-resource-manager-metrics.md)

* *CPU* (måttnamn): `servicefabric:/_CpuCores`En logisk kärna som är tillgänglig på värddatorn. Alla kärnor över alla noder viktas på samma sätt.

* *Minne* (måttnamn): `servicefabric:/_MemoryInMB`Minnet uttrycks i megabyte och mappas till fysiskt minne som är tillgängligt på datorn.

För dessa två mått spårar [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) total klusterkapacitet, belastningen på varje nod i klustret och de återstående resurserna i klustret. Dessa två mått motsvarar alla andra användare eller anpassade mått. Alla befintliga funktioner kan användas med dem:

* Klustret kan [balanseras](service-fabric-cluster-resource-manager-balancing.md) enligt dessa två mått (standardbeteende).
* Klustret kan [defragmenteras](service-fabric-cluster-resource-manager-defragmentation-metrics.md) enligt dessa två mått.
* När [du beskriver ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)kan buffrad kapacitet ställas in för dessa två mått.

> [!NOTE]
> [Dynamisk inläsningsrapportering](service-fabric-cluster-resource-manager-metrics.md) stöds inte för dessa mått. belastningar för dessa mått definieras vid skapande tid.

## <a name="resource-governance-mechanism"></a>Mekanism för resursstyrning

Service Fabric-körningen tillhandahåller för närvarande inte reservation för resurser. När en process eller en behållare öppnas anger körningen resursgränserna för de belastningar som definierades vid skapande. Dessutom avvisar körningen öppnandet av nya tjänstpaket som är tillgängliga när resurser överskrids. För att bättre förstå hur processen fungerar, låt oss ta ett exempel på en nod med två CPU-kärnor (mekanism för minnesstyrning är likvärdig):

1. Först placeras en behållare på noden och begär en CPU-kärna. Körningen öppnar behållaren och ställer in CPU-gränsen till en kärna. Behållaren kan inte använda mer än en kärna.

2. Sedan placeras en replik av en tjänst på noden och motsvarande tjänstpaket anger en gräns för en CPU-kärna. Körningen öppnar kodpaketet och anger dess CPU-gräns till en kärna.

Nu är summan av gränserna lika med nodens kapacitet. En process och en behållare körs med en kärna vardera och inte störa varandra. Service Fabric placerar inte fler behållare eller repliker när de anger CPU-gränsen.

Det finns dock två situationer där andra processer kan kämpa för CPU. I dessa situationer kan en process och en behållare från vårt exempel uppleva problemet med bullriga grannar:

* *Blandning av styrda och icke-styrda tjänster och behållare*: Om en användare skapar en tjänst utan någon angiven resursstyrning, ser körningen det som att det inte förbrukar några resurser och kan placera den på noden i vårt exempel. I det här fallet förbrukar den här nya processen effektivt en del processor på bekostnad av de tjänster som redan körs på noden. Det finns två lösningar på detta problem. Blanda antingen inte styrda och icke-styrda tjänster i samma kluster, eller använd [placeringsbegränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) så att dessa två typer av tjänster inte hamnar på samma uppsättning noder.

* *När en annan process startas på noden, utanför Service Fabric (till exempel en OS-tjänst):* I det här fallet processen utanför Service Fabric kämpar också för CPU med befintliga tjänster. Lösningen på det här problemet är att ställa in nodkapaciteter korrekt för att ta hänsyn till OS-omkostnader, vilket visas i nästa avsnitt.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Klusterinställningar för att aktivera resursstyrning

När en nod startar och ansluter till klustret identifierar Service Fabric den tillgängliga mängden minne och tillgängligt antal kärnor och anger sedan nodkapaciteterna för dessa två resurser.

Om du vill lämna buffertutrymme för operativsystemet och för andra processer som körs på noden använder Service Fabric endast 80 % av de tillgängliga resurserna på noden. Den här procentsatsen kan konfigureras och kan ändras i klustermanifestet.

Här är ett exempel på hur du instruerar Service Fabric att använda 50% av tillgänglig CPU och 70% av tillgängligt minne:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

För de flesta kunder och scenarier är automatisk identifiering av nodkapaciteter för processorn och minnet den rekommenderade konfigurationen (automatisk identifiering är aktiverad som standard). Om du behöver fullständig manuell installation av nodkapacitet kan du konfigurera dem per nodtyp med hjälp av mekanismen för att beskriva noderna i klustret. Här är ett exempel på hur du ställer in nodtypen med fyra kärnor och 2 GB minne:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

När automatisk identifiering av tillgängliga resurser är aktiverat och nodkapaciteter definieras manuellt i klustermanifestet, kontrollerar Service Fabric att noden har tillräckligt med resurser för att stödja den kapacitet som användaren har definierat:

* Om nodkapaciteter som definieras i manifestet är mindre än eller lika med de tillgängliga resurserna på noden, använder Service Fabric de kapaciteter som anges i manifestet.

* Om nodkapaciteter som definieras i manifestet är större än tillgängliga resurser använder Service Fabric de tillgängliga resurserna som nodkapacitet.

Automatisk identifiering av tillgängliga resurser kan inaktiveras om det inte behövs. Om du vill inaktivera den ändrar du följande inställning:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

För optimal prestanda bör även följande inställning aktiveras i klustermanifestet:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Från och med Service Fabric version 7.0 har vi uppdaterat regeln för hur nodresurskapacitet beräknas i de fall där användaren manuellt tillhandahåller värdena för nodresurskapaciteter. Låt oss tänka på följande scenario:
>
> * Det finns 10 cpu-kärnor totalt på noden
> * SF är konfigurerat för att använda 80 % av det totala antalet resurser för användartjänsterna (standardinställning), vilket ger en buffert på 20 % för andra tjänster som körs på noden (inkl. systemtjänster för Service Fabric)
> * Användaren bestämmer sig för att manuellt åsidosätta nodresurskapaciteten för processorkärnorna och ställer in det till 5 kärnor
>
> Vi har ändrat regeln om hur den tillgängliga kapaciteten för Service Fabric-användartjänster beräknas på följande sätt:
>
> * Före Service Fabric 7.0 beräknas tillgänglig kapacitet för användartjänster till **5 kärnor** (kapacitetsbuffert på 20 % ignoreras)
> * Från och med Service Fabric 7.0 beräknas tillgänglig kapacitet för användartjänster till **4 kärnor** (kapacitetsbuffert på 20 % ignoreras inte)

## <a name="specify-resource-governance"></a>Ange resursstyrning

Begränsningar för resursstyrning anges i programmanifest (avsnittet ServiceManifestImport) som visas i följande exempel:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

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

I det här exemplet får servicepaketet **ServicePackageA** en kärna på noderna där det placeras. Det här servicepaketet innehåller två kodpaket (**CodeA1** och `CpuShares` **CodeA2**) och båda anger parametern. Andelen CpuShares 512:256 delar kärnan över de två kodpaketen.

Således, i det här exemplet, CodeA1 får två tredjedelar av en kärna, och CodeA2 får en tredjedel av en kärna (och en mjuk garanti reservation av samma). Om CpuShares inte anges för kodpaket delar Service Fabric kärnorna lika mellan dem.

Minnesgränser är absoluta, så båda kodpaketen är begränsade till 1024 MB minne (och en mjuk garantireservation av samma). Kodpaket (behållare eller processer) kan inte allokera mer minne än den här gränsen, och försök att göra det resulterar i ett undantag för slutminnet. För att tvingande resursbegränsning ska fungera bör minnesbegränsningar ha angetts för alla kodpaket inom ett tjänstpaket.

### <a name="using-application-parameters"></a>Använda programparametrar

När du anger inställningar för resursstyrning är det möjligt att använda [programparametrar](service-fabric-manage-multiple-environment-app-configuration.md) för att hantera flera appkonfigurationer. I följande exempel visas användningen av programparametrar:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

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

I det här exemplet anges standardparametervärden för produktionsmiljön, där varje servicepaket får 4 kärnor och 2 GB minne. Det är möjligt att ändra standardvärden med programparameterfiler. I det här exemplet kan en parameterfil användas för att testa programmet lokalt, där det skulle få mindre resurser än i produktion:

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
> Det är tillgängligt att ange resursstyrning med programparametrar som börjar med Service Fabric version 6.1.<br>
>
> När programparametrar används för att ange resursstyrning kan Service Fabric inte nedgraderas till en version före version 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Tvinga resursgränserna för användartjänster

När du tillämpar resursstyrning på tjänstinfrastrukturtjänsterna garanterar att dessa resursstyrda tjänster inte kan överskrida sin resurskvot, måste många användare fortfarande köra vissa av sina Service Fabric-tjänster i oreglerat läge. När du använder ostyrda Service Fabric-tjänster är det möjligt att stöta på situationer där "skenande" oreglerade tjänster förbrukar alla tillgängliga resurser på Service Fabric-noderna, vilket kan leda till allvarliga problem som:

* Resurssvältning av andra tjänster som körs på noderna (inklusive Service Fabric-systemtjänster)
* Noder hamnar i ett ohälsosamt tillstånd
* Api:er för hantering av tjänst fabric-klusterresurser som inte svarar

För att förhindra att dessa situationer uppstår kan du med Service Fabric *tillämpa resursgränserna för alla tjänst fabric-användartjänster som körs på noden* (både styrda och ostyrda) för att garantera att användartjänster aldrig kommer att använda mer än den angivna mängden resurser. Detta uppnås genom att ange värdet för enforceUserServiceMetricCapacities config i avsnittet PlacementAndLoadBalancing i ClusterManifest till true. Den här inställningen är inaktiverad som standard.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Ytterligare anmärkningar:

* Tvingande resursgräns gäller `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` endast för och resursmått
* Tvingande resursgräns fungerar bara om nodkapacitet för resursmåtten är tillgängliga för Service Fabric, antingen via mekanism för automatisk identifiering eller via användare som manuellt anger nodkapaciteten (vilket förklaras i [avsnittet Klusterinställningar för att aktivera resursstyrningsavsnitt).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)Om nodkapaciteter inte är konfigurerade kan resursgränsen inte användas eftersom Service Fabric inte kan veta hur mycket resurser som ska reserveras för användartjänster.Service Fabric utfärdar en hälsovarning om "EnforceUserServiceMetricCapacities" är sant men nodkapaciteter är inte konfigurerade.

## <a name="other-resources-for-containers"></a>Andra resurser för containrar

Förutom CPU och minne är det möjligt att ange andra resursgränser för behållare. Dessa gränser anges på kodpaketsnivå och tillämpas när behållaren startas. Till skillnad från cpu och minne är Cluster Resource Manager inte medveten om dessa resurser och gör inga kapacitetskontroller eller belastningsutjämning för dem.

* *MemorySwapInMB*: Mängden växlingsminne som en behållare kan använda.
* *MemoryReservationInMB*: Den mjuka gränsen för minnesstyrning som endast tillämpas när minneskonkurrens upptäcks på noden.
* *CpuPercent*: Den procentandel av CPU som behållaren kan använda. Om CPU-gränser anges för tjänstpaketet ignoreras den här parametern i praktiken.
* *MaximumIOps*: Den maximala IOPS som en behållare kan använda (läsa och skriva).
* *MaximumIOBytesps*: Den maximala I/O (byte per sekund) som en behållare kan använda (läsa och skriva).
* *BlockIOWeight*: Blocket IO-vikt för i förhållande till andra behållare.

Dessa resurser kan kombineras med CPU och minne. Här är ett exempel på hur du anger ytterligare resurser för behållare:

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

* Mer information om Cluster Resource Manager finns [i Introduktion till resurshanteraren för klusterresurshanteraren för service fabric.](service-fabric-cluster-resource-manager-introduction.md)
* Om du vill veta mer om programmodellen, servicepaket och kodpaket – och hur repliker mappas till dem – läs [Modell ett program i Service Fabric](service-fabric-application-model.md).
