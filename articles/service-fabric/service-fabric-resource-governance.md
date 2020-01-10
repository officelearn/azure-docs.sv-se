---
title: Resursstyrning för container och tjänster
description: Med Azure Service Fabric kan du ange resurs gränser för tjänster som körs inuti eller utanför behållare.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 85520876d7f0c89450b572d28dee6cb66ed2231d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772388"
---
# <a name="resource-governance"></a>Resursstyrning

När du kör flera tjänster på samma nod eller kluster är det möjligt att en tjänst kan förbruka fler resurser, svälter andra tjänster i processen. Det här problemet kallas för problem med "störningarnas granne". Azure Service Fabric gör det möjligt för utvecklare att ange reservationer och gränser per tjänst för att garantera resurser och begränsa resursanvändning.

> Innan du fortsätter med den här artikeln rekommenderar vi att du bekanta dig med [Service Fabric program modellen](service-fabric-application-model.md) och [Service Fabric värd modell](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Resurs styrnings mått

Resurs styrning stöds i Service Fabric i enlighet med [tjänst paketet](service-fabric-application-model.md). De resurser som är tilldelade till tjänst paketet kan delas ytterligare mellan kod paket. Resurs gränserna som anges innebär också att resurserna reserveras. Service Fabric har stöd för att ange CPU och minne per tjänst paket, med två inbyggda [mått](service-fabric-cluster-resource-manager-metrics.md):

* *Processor* (metric Name `servicefabric:/_CpuCores`): en logisk kärna som är tillgänglig på värddatorn. Alla kärnor på alla noder viktas likadant.

* *Minne* (metric Name `servicefabric:/_MemoryInMB`): minnet uttrycks i megabyte och det mappas till det fysiska minne som är tillgängligt på datorn.

För dessa två mått spårar [kluster resurs hanteraren](service-fabric-cluster-resource-manager-cluster-description.md) den totala kluster kapaciteten, belastningen på varje nod i klustret och återstående resurser i klustret. Dessa två mått motsvarar andra användare eller anpassade mått. Alla befintliga funktioner kan användas med dem:

* Klustret kan [bal anse](service-fabric-cluster-resource-manager-balancing.md) ras enligt dessa två mått (standard beteende).
* Klustret kan [defragmentas](service-fabric-cluster-resource-manager-defragmentation-metrics.md) enligt dessa två mått.
* När du [beskriver ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)kan du ange en buffrad kapacitet för dessa två mått.

[Dynamisk inläsnings rapportering](service-fabric-cluster-resource-manager-metrics.md) stöds inte för dessa mått och belastningar för dessa mått definieras vid skapande tillfället.

## <a name="resource-governance-mechanism"></a>Resurs styrnings funktion

Det finns för närvarande ingen reservation för resurser i Service Fabric Runtime. När en process eller behållare öppnas, anger körningen resurs gränserna till de belastningar som definierades vid skapande tillfället. Dessutom avvisar körnings miljön öppningen av nya tjänst paket som är tillgängliga när resurserna överskrids. För att bättre förstå hur processen fungerar, tar vi ett exempel på en nod med två processor kärnor (mekanismen för minnes styrning är likvärdig):

1. Först placeras en behållare på noden och begär en processor kärna. Körningen öppnar behållaren och anger processor gränsen till en kärna. Behållaren kan inte använda mer än en kärna.

2. Sedan placeras en replik av en tjänst på noden, och motsvarande tjänst paket anger en gräns på en processor kärna. Körningen öppnar kod paketet och anger dess processor gräns till en kärna.

I det här läget är summan av gränserna lika med nodens kapacitet. En process och en behållare körs med en kärna och inte stör varandra. Service Fabric placerar inte fler behållare eller repliker när de anger processor gränsen.

Det finns dock två situationer där andra processer kan tävla för CPU. I sådana fall kan en process och en behållare från vårt exempel drabbas av problem med problem med störningar:

* *Blanda reglerade och icke-styrda tjänster och behållare*: om en användare skapar en tjänst utan någon resurs styrning som har angetts, ser körnings miljön den som förbrukar inga resurser och kan placera den på noden i vårt exempel. I det här fallet förbrukar sig den här nya processen mycket processor vid kostnaden för de tjänster som redan körs på noden. Det finns två lösningar på det här problemet. Du kan antingen inte blanda reglerade och icke-styrda tjänster i samma kluster, eller använda [placerings begränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) så att dessa två typer av tjänster inte slutar på samma uppsättning noder.

* *När en annan process startas på noden, utanför Service Fabric (t. ex. en OS-tjänst)* : i det här fallet följer processen utanför Service Fabric även för CPU med befintliga tjänster. Lösningen på det här problemet är att konfigurera nodens kapacitet på rätt sätt för att redovisa OS-kostnader, som du ser i nästa avsnitt.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Kluster konfiguration för att aktivera resurs styrning

När en nod startar och ansluter till klustret, identifierar Service Fabric mängden tillgängligt minne och det tillgängliga antalet kärnor och anger sedan nodens kapacitet för dessa två resurser.

För att lämna buffertutrymme för operativ systemet, och för andra processer som körs på noden, använder Service Fabric bara 80% av de tillgängliga resurserna på noden. Den här procent andelen kan konfigureras och kan ändras i kluster manifestet.

Här är ett exempel på hur du kan instruera Service Fabric att använda 50% av tillgänglig processor och 70% av tillgängligt minne:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

För de flesta kunder och scenarier är den automatiska identifieringen av nodens kapacitet för processor och minne den rekommenderade konfigurationen (automatisk identifiering är aktiverat som standard). Men om du behöver fullständig manuell konfigurering av Node-kapaciteter kan du konfigurera de här typerna per nodtyp med hjälp av mekanismen för att beskriva noderna i klustret. Här är ett exempel på hur du ställer in nodtypen med fyra kärnor och 2 GB minne:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

När automatisk identifiering av tillgängliga resurser är aktiverat och nodens kapacitet definieras manuellt i kluster manifestet, Service Fabric kontrollerar att noden har tillräckligt med resurser för att stödja den kapacitet som användaren har definierat:

* Om nodens kapaciteter som har definierats i manifestet är mindre än eller lika med de tillgängliga resurserna på noden, använder Service Fabric de kapaciteter som anges i manifestet.

* Om nodens kapaciteter som har definierats i manifestet är större än tillgängliga resurser, använder Service Fabric de tillgängliga resurserna som nodens kapacitet.

Automatisk identifiering av tillgängliga resurser kan stängas av om det inte behövs. Ändra följande inställning för att inaktivera den:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

För optimala prestanda bör följande inställning också aktive ras i kluster manifestet:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Från och med Service Fabric version 7,0 har vi uppdaterat regeln för hur resurs kapacitet för noder beräknas i de fall där användaren manuellt tillhandahåller värdena för resurs kapacitet. Vi tar hänsyn till följande scenario:
>
> * Det finns 10 CPU-kärnor totalt på noden
> * SF är konfigurerat för att använda 80% av det totala antalet resurser för användar tjänsterna (standardinställning), vilket lämnar en buffert på 20% för de andra tjänsterna som körs på noden (inklusive Service Fabric system tjänster)
> * Användaren bestämmer sig för att manuellt åsidosätta resurs kapaciteten för processor kärnor, och ställer in den på 5 kärnor
>
> Vi har ändrat regeln för hur den tillgängliga kapaciteten för Service Fabric användar tjänster beräknas på följande sätt:
>
> * Innan Service Fabric 7,0 beräknas tillgänglig kapacitet för användar tjänster till **5 kärnor** (kapacitets bufferten på 20% ignoreras)
> * Från och med Service Fabric 7,0 beräknas tillgänglig kapacitet för användar tjänster till **4 kärnor** (kapacitets bufferten på 20% ignoreras inte)

## <a name="specify-resource-governance"></a>Ange resurs styrning

Resurs styrnings gränser anges i avsnittet applikations manifest (service manifest import), som du ser i följande exempel:

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

I det här exemplet hämtar tjänst paketet som heter **ServicePackageA** en kärna på noderna där det placeras. Det här tjänst paketet innehåller två kod paket (**CodeA1** och **CodeA2**) och båda anger `CpuShares`-parametern. Proportionen av CpuShares 512:256 delar upp kärnan i de två kod paketen.

I det här exemplet hämtar CodeA1 två tredjedelar av en kärna och CodeA2 får en tredjedel av en kärna (och en återreservation av mjuk garanti av samma). Om CpuShares inte anges för kod paket, dividerar Service Fabric kärnorna jämnt mellan dem.

Minnes gränserna är absoluta, så båda kod paketen är begränsade till 1024 MB minne (och en reservation av mjuk garanti av samma). Kod paket (behållare eller processer) kan inte allokera mer minne än den här gränsen, och om du försöker göra detta resulterar det i ett minnes sluts undantag. För att tvingande resursbegränsning ska fungera bör minnesbegränsningar ha angetts för alla kodpaket inom ett tjänstpaket.

### <a name="using-application-parameters"></a>Använda program parametrar

När du anger resurs styrnings inställningar är det möjligt att använda [program parametrar](service-fabric-manage-multiple-environment-app-configuration.md) för att hantera flera AppData. I följande exempel visas användningen av program parametrarna:

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

I det här exemplet anges standard parameter värden för produktions miljön, där varje tjänst paket får 4 kärnor och 2 GB minne. Det går att ändra standardvärden med program parameter-filer. I det här exemplet kan en parameter fil användas för att testa programmet lokalt, där det skulle få mindre resurser än i produktion:

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
> Att ange resurs styrning med program parametrar är tillgängligt från och med Service Fabric version 6,1.<br>
>
> När program parametrar används för att ange resurs styrning kan Service Fabric inte nedgraderas till en tidigare version än version 6,1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Tvinga resurs begränsningar för användar tjänster

När du använder resurs styrning för Service Fabric tjänster garanterar det att de resursbaserade tjänsterna inte får överskrida sina resursers kvoter, men många användare behöver fortfarande köra vissa av deras Service Fabric tjänster i ett icke-styrd läge. När du använder unreglerade Service Fabric-tjänster är det möjligt att köra i situationer där "lediga" ej styrda tjänster förbrukar alla tillgängliga resurser på Service Fabric-noderna, vilket kan leda till allvarliga problem som:

* Resurs-effekter för andra tjänster som körs på noderna (inklusive Service Fabric system tjänster)
* Noder som slutar i ett ohälsosamt tillstånd
* Inga svar på Service Fabric kluster hanterings-API: er

För att förhindra att dessa situationer inträffar kan du med Service Fabric *upprätthålla resurs gränserna för alla Service Fabric användar tjänster som körs på noden* (både styrd och utan regler) för att garantera att användar tjänsterna aldrig kommer att använda mer än den angivna mängden resurser. Detta uppnås genom att ange värdet för EnforceUserServiceMetricCapacities-konfigurationen i avsnittet PlacementAndLoadBalancing i ClusterManifest till true. Den här inställningen är inaktive rad som standard.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Ytterligare anmärkningar:

* Tvingande resurs gräns gäller endast för `servicefabric:/_CpuCores` och `servicefabric:/_MemoryInMB` resurs mått
* Tillämpning av resurs begränsningen fungerar bara om nodens kapacitet för resurs måtten är tillgängligt för Service Fabric, antingen via mekanismen för automatisk identifiering eller genom att användare manuellt anger nodens kapacitet (enligt beskrivningen i [kluster konfigurationen för att aktivera avsnittet resurs styrning](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ). Om nodens kapacitet inte har kon figurer ATS kan du inte använda funktionen för tvingande resurs gräns eftersom Service Fabric inte vet hur mycket resurser som ska reserveras för användar tjänster. Service Fabric kommer att utfärda en hälso varning om "EnforceUserServiceMetricCapacities" är sant men nodens kapacitet inte har kon figurer ATS.

## <a name="other-resources-for-containers"></a>Andra resurser för behållare

Förutom CPU och minne är det möjligt att ange andra resurs gränser för behållare. Dessa gränser anges på kod-paket nivå och tillämpas när behållaren startas. Till skillnad från CPU och minne är kluster resurs hanteraren inte medveten om dessa resurser och gör inga kapacitets kontroller eller belastnings utjämning för dem.

* *MemorySwapInMB*: mängden växlings minne som en behållare kan använda.
* *MemoryReservationInMB*: den mjuka gränsen för minnes styrning som endast tillämpas när minnes konkurrens identifieras på noden.
* *CpuPercent*: procent andelen CPU som containern kan använda. Om processor gränser har angetts för tjänst paketet, ignoreras den här parametern i praktiken.
* *MaximumIOps*: högsta IOPS som en behållare kan använda (läsa och skriva).
* *MaximumIOBytesps*: den maximala IO (byte per sekund) som en behållare kan använda (läsa och skriva).
* *BlockIOWeight*: BLOCKets IO-vikt för relativa till andra behållare.

Dessa resurser kan kombineras med processor och minne. Här är ett exempel på hur du anger ytterligare resurser för behållare:

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

* Läs mer om kluster resurs hanteraren [i Introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Om du vill veta mer om program modellen, service paket och kod paket – och hur repliker mappar till dem, kan du läsa [en app i Service Fabric](service-fabric-application-model.md).
