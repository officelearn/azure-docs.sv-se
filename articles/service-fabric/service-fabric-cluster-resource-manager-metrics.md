---
title: Hantera azure service fabric-appinläsning med mått
description: Lär dig mer om hur du konfigurerar och använder mått i Service Fabric för att hantera förbrukning av tjänstresurser.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452005"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Hantera resursförbrukning och belastning i Service Fabric med mått
*Mått* är de resurser som dina tjänster bryr sig om och som tillhandahålls av noderna i klustret. Ett mått är allt som du vill hantera för att förbättra eller övervaka dina tjänsters prestanda. Du kan till exempel titta på minnesförbrukning för att veta om tjänsten är överbelastad. En annan användning är att ta reda på om tjänsten kan flytta någon annanstans där minnet är mindre begränsat för att få bättre prestanda.

Saker som minne, disk och CPU-användning är exempel på mått. Dessa mått är fysiska mått, resurser som motsvarar fysiska resurser på noden som måste hanteras. Mått kan också vara (och ofta är) logiska mått. Logiska mått är saker som "MyWorkQueueDepth" eller "MessagesToProcess" eller "TotalRecords". Logiska mått är programdefinierade och motsvarar indirekt viss fysisk resursförbrukning. Logiska mått är vanliga eftersom det kan vara svårt att mäta och rapportera förbrukning av fysiska resurser per tjänst. Komplexiteten i att mäta och rapportera dina egna fysiska mått är också varför Service Fabric tillhandahåller vissa standardmått.

## <a name="default-metrics"></a>Standardmått
Anta att du vill börja skriva och distribuera tjänsten. Nu vet du inte vilka fysiska eller logiska resurser den förbrukar. Det är bra! Service Fabric Cluster Resource Manager använder vissa standardmått när inga andra mått anges. De är:

  - PrimaryCount - antal primära repliker på noden 
  - ReplicaCount - antal totala tillståndskänsliga repliker på noden
  - Antal - antal serviceobjekt (tillståndslösa och tillståndskänsliga) på noden

| Mått | Tillståndslös instansbelastning | Tillståndskänslig sekundär belastning | Tillståndskänslig primär belastning | Vikt |
| --- | --- | --- | --- | --- |
| Primärt Antal |0 |0 |1 |Hög |
| ReplicaCount (ReplicaCount) |0 |1 |1 |Medel |
| Antal |1 |1 |1 |Låg |


För grundläggande arbetsbelastningar ger standardmåtten en anständig fördelning av arbetet i klustret. I följande exempel ska vi se vad som händer när vi skapar två tjänster och förlitar oss på standardmåtten för balansering. Den första tjänsten är en tillståndskänslig tjänst med tre partitioner och en målreplikuppsättningsstorlek på tre. Den andra tjänsten är en tillståndslös tjänst med en partition och ett instansantal på tre.

Det här får vi:

<center>

![Klusterlayout med standardmått][Image1]
</center>

Några saker att tänka på:
  - Primära repliker för den tillståndskänsliga tjänsten distribueras över flera noder
  - Repliker för samma partition finns på olika noder
  - Det totala antalet primärval och sekundärer fördelas i klustret
  - Det totala antalet serviceobjekt fördelas jämnt på varje nod

Bra!

Standardmåtten fungerar bra som en början. Standardmåtten kommer dock bara att bära dig hittills. Till exempel: Vad är sannolikheten att partitionering systemet du plockade resulterar i helt jämn användning av alla partitioner? Vad är chansen att belastningen för en viss tjänst är konstant över tiden, eller till och med samma över flera partitioner just nu?

Du kan köra med bara standardmått. Men om du gör det innebär det vanligtvis att klusterutnyttjandet är lägre och mer ojämnt än du vill. Detta beror på att standardmåtten inte är anpassningsbara och förutsätter att allt är likvärdigt. Till exempel en primär som är upptagen och en som inte båda bidrar med "1" till PrimaryCount-måttet. I värsta fall kan endast standardmått resultera i överplanerade noder som resulterar i prestandaproblem. Om du är intresserad av att få ut det mesta av klustret och undvika prestandaproblem måste du använda anpassade mått och dynamisk inläsningsrapportering.

## <a name="custom-metrics"></a>Anpassade mått
Mått konfigureras per-namngivna tjänst-instans när du skapar tjänsten.

Alla mått har vissa egenskaper som beskriver det: ett namn, en vikt och en standardbelastning.

* Måttnamn: Namnet på måttet. Måttnamnet är en unik identifierare för måttet i klustret från Resurshanterarens perspektiv.
* Vikt: Måttvikt definierar hur viktigt det här måttet är i förhållande till de andra måtten för den här tjänsten.
* Standardbelastning: Standardbelastningen representeras på olika sätt beroende på om tjänsten är tillståndslös eller tillståndskänslig.
  * För tillståndslösa tjänster har varje mått en enda egenskap med namnet DefaultLoad
  * För tillståndskänsliga tjänster som du definierar:
    * PrimaryDefaultLoad: Standardbeloppet för det här måttet som den här tjänsten förbrukar när den är primär
    * SecondaryDefaultLoad: Standardbeloppet för det här måttet som den här tjänsten förbrukar när det är en sekundär

> [!NOTE]
> Om du definierar anpassade mått och du _också_ vill använda standardmåtten måste du _uttryckligen_ lägga till standardmåtten tillbaka och definiera vikter och värden för dem. Detta beror på att du måste definiera relationen mellan standardmåtten och dina anpassade mått. Du kanske till exempel bryr dig mer om ConnectionCount eller WorkQueueDepth än Primär distribution. Som standard är tjockheten för PrimaryCount-måttet Hög, så du vill minska den till Medel när du lägger till dina andra mått för att säkerställa att de har företräde.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiera mått för din tjänst - ett exempel
Anta att du vill ha följande konfiguration:

  - Din tjänst rapporterar ett mått med namnet "ConnectionCount"
  - Du vill också använda standardmåtten 
  - Du har gjort några mätningar och vet att normalt en primär kopia av den tjänsten tar upp 20 enheter av "ConnectionCount"
  - Sekundärer använder 5 enheter av "ConnectionCount"
  - Du vet att "ConnectionCount" är det viktigaste måttet när det gäller att hantera prestanda för den här tjänsten
  - Du vill fortfarande att primära repliker ska balanseras. Balansera primära repliker är i allmänhet en bra idé oavsett vad. Detta förhindrar att förlust av någon nod eller feldomän påverkar en majoritet av primära repliker tillsammans med den. 
  - I annat fall är standardmåtten bra

Här är koden som du skulle skriva för att skapa en tjänst med den måttkonfigurationen:

Kod:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Ovanstående exempel och resten av det här dokumentet beskriver hantering av mått per namngiven tjänst. Det är också möjligt att definiera mått för dina tjänster på _tjänsttypsnivå._ Detta åstadkoms genom att ange dem i dina servicemanifest. Det rekommenderas inte att definiera typnivåmått av flera skäl. Den första orsaken är att måttnamn ofta är miljöspecifika. Om det inte finns ett fast kontrakt på plats, kan du inte vara säker på att måttet "Kärnor" i en miljö är inte "MiliCores" eller "CoReS" i andra. Om dina mått definieras i ditt manifest måste du skapa nya manifest per miljö. Detta leder vanligtvis till en spridning av olika manifest med endast mindre skillnader, vilket kan leda till förvaltningssvårigheter.  
>
> Måttbelastningar tilldelas ofta per namngiven tjänst-instans. Anta till exempel att du skapar en instans av tjänsten för CustomerA som planerar att använda den bara lätt. Anta också att du skapar en annan för CustomerB som har en större arbetsbelastning. I det här fallet skulle du förmodligen vill justera standardbelastningar för dessa tjänster. Om du har mått och inläsningar definierade via manifest och du vill stödja det här scenariot kräver det olika program- och tjänsttyper för varje kund. De värden som definierats vid skapande av tjänster åsidosätter de som definierats i manifestet, så du kan använda det för att ange specifika standardvärden. Men om du gör det får de värden som deklarerats i manifesten att inte matcha dem som tjänsten faktiskt körs med. Detta kan leda till förvirring. 
>

Som en påminnelse: om du bara vill använda standardmåtten behöver du inte röra måttsamlingen alls eller göra något speciellt när du skapar tjänsten. Standardmåtten används automatiskt när inga andra definieras. 

Nu ska vi gå igenom var och en av dessa inställningar mer i detalj och prata om det beteende som det påverkar.

## <a name="load"></a>Läsa in
Hela poängen med att definiera mått är att representera en del belastning. *Inläsning* är hur mycket av ett givet mått som förbrukas av någon tjänstinstans eller replik på en viss nod. Belastningen kan konfigureras vid nästan vilken punkt som helst. Ett exempel:

  - Belastning kan definieras när en tjänst skapas. Detta kallas _standardbelastning_.
  - Måttinformationen, inklusive standardbelastningar, för en tjänst kan uppdateras när tjänsten har skapats. Detta kallas _för att uppdatera en tjänst_. 
  - Lasterna för en viss partition kan återställas till standardvärdena för den tjänsten. Detta kallas _återställning av partitionsbelastning_.
  - Belastning kan rapporteras per tjänstobjekt basis dynamiskt under körning. Detta kallas _rapporteringsbelastning_. 
  
Alla dessa strategier kan användas inom samma tjänst under sin livstid. 

## <a name="default-load"></a>Standardinläsning
*Standardbelastning* är hur mycket av måttet varje tjänstobjekt (tillståndslös instans eller tillståndskänslig replik) av den här tjänsten förbrukar. Klusterresurshanteraren använder det här numret för belastningen av tjänstobjektet tills det tar emot annan information, till exempel en dynamisk inläsningsrapport. För enklare tjänster är standardbelastningen en statisk definition. Standardbelastningen uppdateras aldrig och används under tjänstens livstid. Standardbelastningar fungerar utmärkt för enkla kapacitetsplaneringsscenarier där vissa mängder resurser är avsedda för olika arbetsbelastningar och inte ändras.

> [!NOTE]
> Mer information om kapacitetshantering och definiera kapacitet för noderna i klustret finns i [den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Klusterresurshanteraren gör det möjligt för tillståndskänsliga tjänster att ange en annan standardbelastning för sina primärval och sekundärfiler. Tillståndslösa tjänster kan bara ange ett värde som gäller för alla instanser. För tillståndskänsliga tjänster är standardbelastningen för primära och sekundära repliker vanligtvis olika eftersom repliker utför olika typer av arbete i varje roll. Till exempel, Primärval tjänar vanligtvis både läser och skriver, och hantera de flesta av beräkningsbördan, medan sekundärer inte. Vanligtvis är standardbelastningen för en primär replik högre än standardbelastningen för sekundära repliker. De verkliga siffrorna bör bero på dina egna mått.

## <a name="dynamic-load"></a>Dynamisk belastning
Låt oss säga att du har kört din tjänst ett tag. Med lite övervakning har du märkt att:

1. Vissa partitioner eller instanser av en viss tjänst förbrukar mer resurser än andra
2. Vissa tjänster har belastning som varierar över tiden.

Det finns massor av saker som kan orsaka dessa typer av belastningsfluktuationer. Till exempel är olika tjänster eller partitioner associerade med olika kunder med olika krav. Belastningen kan också ändras eftersom mängden arbete som tjänsten gör varierar under dagen. Oavsett orsaken finns det vanligtvis inget enstaka nummer som du kan använda som standard. Detta gäller särskilt om du vill få ut så mycket som används i klustret. Alla värden du väljer för standardbelastning är fel en del av tiden. Felaktiga standardbelastningar resulterar i att Klusterresurshanteraren antingen över eller under allokerar resurser. Därför har du noder som är över eller underutnyttjade även om Klusterresurshanteraren anser att klustret är balanserat. Standardbelastningar är fortfarande bra eftersom de ger viss information för inledande placering, men de är inte en fullständig historia för verkliga arbetsbelastningar. Om du vill fånga upp ändrade resurskrav på ett korrekt sätt kan varje tjänstobjekt uppdatera sin egen belastning under körning. Detta kallas dynamisk lastrapportering.

Dynamiska inläsningsrapporter gör det möjligt för repliker eller instanser att justera sin allokering/rapporterade belastning av mått under deras livstid. En tjänstreplik eller instans som var kall och som inte gör något arbete skulle vanligtvis rapportera att den använde låga mängder av ett visst mått. En upptagen replik eller instans skulle rapportera att de använder mer.

Rapporteringsbelastning per replik eller instans gör det möjligt för Klusterresurshanteraren att ordna om de enskilda tjänstobjekten i klustret. Omorganisera tjänsterna hjälper till att säkerställa att de får de resurser de behöver. Upptagna tjänster får effektivt "återta" resurser från andra repliker eller instanser som för närvarande är kalla eller gör mindre arbete.

Inom Reliable Services ser koden för att rapportera inläsning dynamiskt ut så här:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

En tjänst kan rapportera om något av de mått som definierats för den vid skapande. Om en tjänst rapporterar belastning för ett mått som den inte är konfigurerad att använda, ignorerar Service Fabric den rapporten. Om det finns andra mått som rapporteras samtidigt som är giltiga, accepteras dessa rapporter. Servicekod kan mäta och rapportera alla mått som den vet hur till, och operatörer kan ange den måttkonfiguration som ska användas utan att behöva ändra servicekoden. 

### <a name="updating-a-services-metric-configuration"></a>Uppdatera en tjänsts måttkonfiguration
Listan över mått som är associerade med tjänsten och egenskaperna för dessa mått kan uppdateras dynamiskt medan tjänsten är live. Detta möjliggör experiment och flexibilitet. Några exempel på när detta är användbart är:

  - inaktivera ett mått med en buggyrapport för en viss tjänst
  - konfigurera om vikterna för mått baserat på önskat beteende
  - aktivera ett nytt mått först efter att koden redan har distribuerats och validerats via andra mekanismer
  - ändra standardbelastningen för en tjänst baserat på observerat beteende och förbrukning

De viktigaste API:erna `FabricClient.ServiceManagementClient.UpdateServiceAsync` för att `Update-ServiceFabricService` ändra måttkonfigurationen finns i C# och i PowerShell. Oavsett vilken information du anger med dessa API:er ersätter den befintliga måttinformationen för tjänsten omedelbart. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Blanda standardbelastningsvärden och dynamiska belastningsrapporter
Standardbelastning och dynamiska belastningar kan användas för samma tjänst. När en tjänst använder både standardbelastningsrapporter och dynamiska belastningsrapporter fungerar standardbelastningen som en uppskattning tills dynamiska rapporter visas. Standardbelastningen är bra eftersom det ger Klusterresurshanteraren något att arbeta med. Standardbelastningen gör att Klusterresurshanteraren kan placera tjänstobjekten på bra platser när de skapas. Om ingen standardinläsningsinformation tillhandahålls är placering av tjänster effektivt slumpmässig. När belastningsrapporter anländer senare är den första slumpmässiga placeringen ofta fel och Cluster Resource Manager måste flytta tjänster.

Låt oss ta vårt tidigare exempel och se vad som händer när vi lägger till några anpassade mått och dynamisk belastningsrapportering. I det här exemplet använder vi "MemoryInMb" som ett exempelmått.

> [!NOTE]
> Minne är ett av de systemmått som Service Fabric kan [styra](service-fabric-resource-governance.md)och det är vanligtvis svårt att rapportera det själv. Vi förväntar oss faktiskt inte att du ska rapportera om minnesförbrukning. Minnet används här som ett hjälpmedel för att lära sig om funktionerna i Cluster Resource Manager.
>

Låt oss anta att vi först skapade den tillståndskänsliga tjänsten med följande kommando:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Som en påminnelse är den här syntaxen ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Låt oss se hur en möjlig klusterlayout kan se ut:

<center>

![Klusterbalanserad med både standard- och anpassade mått][Image2]
</center>

Några saker som är värda att notera:

* Sekundära repliker i en partition kan var och en ha sin egen belastning
* Totalt sett ser måtten balanserade ut. För Minne är förhållandet mellan den maximala och minsta belastningen 1,75 (noden med mest belastning är N3, minst N2 och 28/16 = 1,75).

Det finns några saker som vi fortfarande måste förklara:

* Vad avgjorde om ett förhållande på 1,75 var rimligt eller inte? Hur vet Cluster Resource Manager om det är tillräckligt bra eller om det finns mer arbete att göra?
* När sker balansering?
* Vad betyder det att minnet var viktat "Hög"?

## <a name="metric-weights"></a>Metriska vikter
Det är viktigt att spåra samma mått för olika tjänster. Den globala vyn är det som gör att Cluster Resource Manager kan spåra förbrukning i klustret, balansera förbrukningen mellan noder och se till att noder inte överskrider kapaciteten. Tjänster kan dock ha olika åsikter om vikten av samma mått. I ett kluster med många mått och många tjänster kanske det inte finns perfekt balanserade lösningar för alla mått. Hur ska Klusterresurshanteraren hantera dessa situationer?

Måttvikter gör att Cluster Resource Manager kan bestämma hur klustret ska balanseras när det inte finns något perfekt svar. Måttvikter gör det också möjligt för Klusterresurshanteraren att balansera specifika tjänster på olika sätt. Mått kan ha fyra olika viktnivåer: Noll, Låg, Medel och Hög. Ett mått med vikten Zero bidrar inte med någonting när man överväger om saker och ting är balanserade eller inte. Belastningen bidrar dock fortfarande till kapacitetshanteringen. Mått med nollvikt är fortfarande användbara och används ofta som en del av tjänstens beteende och prestandaövervakning. [Den här artikeln](service-fabric-diagnostics-event-generation-infra.md) innehåller mer information om användningen av mått för övervakning och diagnostik av dina tjänster. 

Den verkliga effekten av olika måttvikter i klustret är att Cluster Resource Manager genererar olika lösningar. Måttvikter talar om för Cluster Resource Manager att vissa mått är viktigare än andra. När det inte finns någon perfekt lösning kan Cluster Resource Manager föredra lösningar som balanserar de högre viktade måtten bättre. Om en tjänst anser att ett visst mått är oviktigt kan det bero på att de använder det måttet som är obalanserat. Detta gör att en annan tjänst för att få en jämn fördelning av vissa mått som är viktiga för den.

Låt oss titta på ett exempel på några belastningsrapporter och hur olika måttvikter resulterar i olika allokeringar i klustret. I det här exemplet ser vi att om du byter den relativa vikten för måtten skapas olika arrangemang av tjänster om du byter den relativa vikten av måtten.

<center>

![Exempel på måttvikt och dess inverkan på balanseringslösningar][Image3]
</center>

I det här exemplet finns det fyra olika tjänster, alla rapporterar olika värden för två olika mått, MetricA och MetricB. I ett fall definierar alla tjänster MetricA är den viktigaste (Vikt = Hög) och MetricB som oviktiga (Vikt = Låg). Därför ser vi att Cluster Resource Manager placerar tjänsterna så att MetricA är bättre balanserad än MetricB. "Bättre balanserad" innebär att MetricA har en lägre har en lägre standardavvikelse än MetricB. I det andra fallet återför vi måttvikterna. Som ett resultat byter Cluster Resource Manager tjänster A och B för att komma med en allokering där MetricB är bättre balanserad än MetricA.

> [!NOTE]
> Måttvikter avgör hur Cluster Resource Manager ska balansera, men inte när avbalansering ska ske. Mer information om hur du balanserar finns i [den här artikeln](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globala måttvikter
Anta att ServiceA definierar MetricA som hög vikt och ServiceB anger vikten för MetricA till Låg eller Noll. Vad är den faktiska vikt som slutar att vänja sig?

Det finns flera vikter som spåras för varje mått. Den första vikten är den som definierats för måttet när tjänsten skapas. Den andra vikten är en global vikt som beräknas automatiskt. Klusterresurshanteraren använder båda dessa vikter när du poängsättningslösningar. Att ta hänsyn till båda vikterna är viktigt. Detta gör det möjligt för Klusterresurshanteraren att balansera varje tjänst enligt sina egna prioriteringar och även se till att klustret som helhet allokeras korrekt.

Vad skulle hända om Cluster Resource Manager inte brydde sig om både global och lokal balans? Tja, det är lätt att konstruera lösningar som är globalt balanserade, men som resulterar i dålig resursbalans för enskilda tjänster. I följande exempel ska vi titta på en tjänst som konfigurerats med bara standardmåtten och se vad som händer när endast globalt saldo beaktas:

<center>

![Effekten av en global enda lösning][Image4]
</center>

I det översta exemplet som endast baseras på globalt saldo är klustret som helhet verkligen balanserat. Alla noder har samma antal primärfärger och samma antal totalta repliker. Men om man tittar på den faktiska effekten av denna tilldelning är det inte så bra: förlusten av en nod påverkar en viss arbetsbelastning oproportionerligt, eftersom det tar ut alla sina primärval. Om den första noden till exempel misslyckas skulle de tre primärfärgerna för de tre olika partitionerna i Circle-tjänsten gå förlorade. Omvänt har Triangle och Hexagon-tjänsterna sina partitioner förlorar en replik. Detta orsakar inga störningar, annat än att behöva återställa den ned repliken.

I det nedre exemplet har Cluster Resource Manager distribuerat replikerna baserat på både det globala och per tjänst-saldot. Vid beräkning av lösningens poäng ger den större delen av vikten till den globala lösningen och en (konfigurerbar) del till enskilda tjänster. Globalt saldo för ett mått beräknas baserat på medelvärdet av måttvikterna från varje tjänst. Varje tjänst balanseras enligt sina egna definierade måttvikter. Detta säkerställer att tjänsterna balanseras inom sig själva efter sina egna behov. Om samma första nod misslyckas distribueras därför felet över alla partitioner för alla tjänster. Effekten på var och en är densamma.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster finns i Läs mer om hur du [konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definiera Defragmentering Metrics är ett sätt att konsolidera belastning på noder istället för att sprida ut den. Mer information om hur du konfigurerar defragmentering finns i [den här artikeln](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret kan du läsa artikeln om [utjämningsbelastning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Rörelsekostnad är ett sätt att signalera till Cluster Resource Manager att vissa tjänster är dyrare att flytta än andra. Mer information om rörelsekostnader finns i [den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
