---
title: Hantera Azure mikrotjänster belastningen med hjälp av mätvärden | Microsoft Docs
description: Lär dig mer om hur du konfigurerar och använder mått i Service Fabric för att hantera tjänsten resursförbrukning.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7457a820d9179248eab976ceec64f6b7a4a38563
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643346"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Hantera resursförbrukning och belastningen i Service Fabric med
*Mått* är de resurser som din dig för tjänster och som tillhandahålls av noder i klustret. Ett mått är något som du vill hantera för att förbättra eller övervaka prestanda för dina tjänster. Exempelvis kan du titta på minnesförbrukning om du vill veta om din tjänst är överbelastad. Ett annat syfte är att ta reda på om tjänsten kan flytta någon annanstans där minne är mindre begränsad för att få bättre prestanda.

Till exempel minne, Disk och CPU-användning är exempel på mått. De här måtten är fysiska statistik, och resurser som motsvarar fysiska resurser på den nod som måste hanteras. Mått kan också vara (och ofta är) logiska mått. Logisk är till exempel ”MyWorkQueueDepth” eller ”MessagesToProcess” eller ”TotalRecords”. Logiska mått är programdefinierade och motsvarar indirekt vissa fysiska resursförbrukning. Logiska mått är vanliga eftersom det kan vara svårt att mått och rapportera förbrukning av fysiska resurser för per tjänst. Komplexitet mäta och rapportera egna fysiska mått är också varför Service Fabric innehåller vissa siffror som standard.

## <a name="default-metrics"></a>Standard-mått
Anta att du vill börja skriva och distribuera din tjänst. Nu vet du inte vilka fysiska eller logiska resurser som den förbrukar. Det är bra! Resurshanteraren för Service Fabric klustret använder vissa standard mått när inga andra mått har angetts. De är:

  - PrimaryCount - antal primära repliker på noden 
  - ReplicaCount - antal totala tillståndskänslig repliker på noden
  - Antal - antalet för alla service-objekt (tillståndslösa och tillståndskänsliga) på noden

| Mått | Läs in tillståndslös instans | Tillståndskänsliga sekundära belastning | Tillståndskänsliga primära belastning | Vikt |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |0 |
| ReplicaCount |0 |1 |1 |0 |
| Antal |1 |1 |1 |0 |


För grundläggande arbetsbelastningar anger mätvärdena som standard en hyfsad fördelning av arbete i klustret. I följande exempel, låt oss se vad som händer när vi skapar två tjänster och förlitar sig på standard-mätvärden för belastningsutjämning. Den första tjänsten är en tillståndskänslig service med tre partitioner och ett mål replikuppsättningen storleken på tre. Andra tjänsten är en tillståndslös tjänst med en partition och instansantal av tre.

Här är vad du får:

<center>
![Klustret Layout med standard-mått][Image1]
</center>

Några saker att Observera:
  - Primära repliker för tjänsten tillståndskänslig distribueras över flera noder
  - Repliker för samma partition som finns på olika noder
  - Det totala antalet primärfärgerna och sekundärservrar distribueras i klustret
  - Det totala antalet tjänstobjekt fördelas jämnt på varje nod

Bra!

Standard-mått fungera utmärkt som en start. Men hanterar mätvärdena som standard endast du hittills. Till exempel: Vad är sannolikheten för att partitionering schemat du plockats resultat i perfekt även användning av alla partitioner? Vad har du möjlighet att belastningen för en viss tjänst är konstant över tid eller ens över flera partitioner på samma sätt just nu?

Du kan köra med bara mätvärdena som standard. Men om du gör det vanligtvis innebär att din klusteranvändning lägre och mer ojämn än du vill. Detta beror på att mätvärdena som standard inte anpassningsbar och förutsätter att allt är likvärdiga. Till exempel bidra en primär är upptagen och ett som inte är båda ”1” till PrimaryCount måttet. I värsta fall kan med bara mätvärdena som standard också resultera i overscheduled noder, vilket resulterar i prestandaproblem. Om du är intresserad av att mest av klustret och undvika prestandaproblem måste du använda anpassade mått och dynamisk rapportering.

## <a name="custom-metrics"></a>Anpassade mått
Mått är konfigurerade på grundval av per med namnet-service-instans när du skapar tjänsten.

Alla mått har vissa egenskaper som beskriver den: ett namn, en vikt och en standard-belastning.

* Måttnamnet: Namnet på måttet. Måttnamnet är en unik identifierare för måttet inom klustret ur med Resource Manager.
* Vikt: Tjänstmåttets vikt definierar hur viktigt det här måttet är i förhållande till andra mått för den här tjänsten.
* Standard-belastning: Standard belastningen representeras på olika sätt beroende på om tjänsten är tillståndslösa och tillståndskänsliga.
  * För tillståndslösa tjänster har varje mått en egenskap med namnet DefaultLoad
  * För tillståndskänsliga tjänster ange du:
    * PrimaryDefaultLoad: Det här måttet standardmängden tjänsten förbrukar när det är en primär
    * SecondaryDefaultLoad: Det här måttet standardmängden tjänsten förbrukar när det är en sekundär

> [!NOTE]
> Om du definiera anpassade mått och du vill _också_ använda mätvärdena som standard måste du _explicit_ lägga till mätvärdena som standard tillbaka och definiera vikterna och värden för dessa. Det beror på att du måste definiera relationen mellan mätvärdena som är standard och din anpassade mått. Till exempel kanske du bryr dig om ConnectionCount eller WorkQueueDepth mer än primära distributionsplatser. Som standard är vikten av måttet PrimaryCount hög, så att du vill minska till mellan när du lägger till dina andra mått så de högre prioritet.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiera mått för din tjänst - exempel
Anta att du vill konfigurera följande:

  - Tjänsten rapporterar ett mått med namnet ”ConnectionCount”
  - Du vill använda standard-mått 
  - Du har gjort vissa mått och vet att en primär replik av den tjänsten tar normalt 20 enheter ”ConnectionCount”
  - Sekundärservrar använda 5 enheter i ”ConnectionCount”
  - Du vet att ”ConnectionCount” är det viktigaste måtten som hanterar prestandan för viss tjänsten
  - Du ändå vill primära repliker belastningsutjämnade. NLB primära repliker är vanligtvis bra oavsett vad. Detta förhindrar förlust av vissa nod eller fault domän påverkar en majoritet av primära repliker tillsammans med den. 
  - I annat fall är mätvärdena som standard bra

Här är koden som du skulle kunna skriva att skapa en tjänst med mått konfigurationen:

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

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Ovanstående exempel och resten av det här dokumentet beskriver du hantera mått på grundval av per-med namnet-tjänst. Det är också möjligt att definiera mätvärden för dina tjänster på tjänsten _typen_ nivå. Detta åstadkoms genom att ange dem i din service manifest. Definiera typ nivån mått rekommenderas inte av flera skäl. Det första skälet är att mått namn är ofta miljöspecifikt. Om det inte finns ett fast kontrakt på plats, kan du vara säker på att måttet ”kärnor” i en miljö är inte ”MiliCores” eller ”kärnor” i andra. Om din mått har definierats i ditt manifest som du behöver skapa nya manifesten per miljö. Detta leder vanligtvis till en ökande mängden av olika manifest med bara smärre skillnader, vilket kan leda till problem med hantering.  
>
> Mått belastningar tilldelas ofta på grundval av per med namnet-service-instans. Anta exempelvis att du skapar en instans av tjänsten för CustomerA som planerar att använda den inte. Anta också att du skapar en annan för CustomerB som har en större arbetsbelastning. I så fall måste vill du förmodligen justera belastningarna som standard för dessa tjänster. Om du har mått och belastningar som anges via manifesten och du vill kunna använda det här scenariot kräver olika program och typer av tjänster för varje kund. De värden som definierats vid tidpunkten för skapandet av tjänsten åsidosätta de som anges i manifestet, så att du kan använda som kan ange specifika standardinställningar. Dock gör gör de värden som deklarerats i tjänsten körs med manifesten till stämmer inte överens. Detta kan leda till förvirring. 
>

Som en påminnelse: Om du vill använda mätvärdena som är standard, behöver du inte alls touch samlingen mått eller göra något speciellt när du skapar din tjänst. Standard-mått hämta används automatiskt när inga andra definieras. 

Nu går vi igenom de här inställningarna i detalj och talar om hur den inverkar.

## <a name="load"></a>Läsa in
Det hela definiera mått är representerar vissa belastningen. *Läs in* är hur mycket av ett visst mått som används av vissa tjänstinstansen eller repliken på en viss nod. Läs in kan konfigureras på nästan alla punkt. Exempel:

  - Läs in kan definieras när en tjänst skapas. Detta kallas _standard belastningen_.
  - Mått information, inklusive standard belastning för en tjänst kan uppdateras när tjänsten har skapats. Detta kallas _uppdatera en tjänst_. 
  - Belastning för en given partition kan du återställa standardvärdena för tjänsten. Detta kallas _återställa partitionsbelastning_.
  - Läs in rapporteras på en per service objektet bas dynamiskt vid körning. Detta kallas _reporting belastningen_. 
  
Alla dessa strategier kan användas inom samma tjänst under sin livslängd. 

## <a name="default-load"></a>Standard-inläsning
*Standard belastningen* är hur mycket av måttet förbrukar varje serviceobjektet (tillståndslös instans eller tillståndskänslig replik) för den här tjänsten. Resurshanteraren klustret använder det här numret för belastningen på serviceobjektet tills det mottar annan information, till exempel en dynamisk rapport. Standard-belastningen är en statisk definition för enklare tjänster. Standard-belastningen uppdateras aldrig och används för livslängden för tjänsten. Standard laddar fungerar bra för enkel kapacitetsplanering scenarier där vissa mängder resurser är dedikerade till olika arbetsbelastningar och ändras inte.

> [!NOTE]
> Mer information om kapacitetshantering av och definiera kapacitet för noder i klustret finns [i den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Klustret Resource Manager tillåter tillståndskänsliga tjänster att ange en annan standard belastning för sina primärfärgerna och sekundärservrar. Tillståndslösa tjänster kan bara ange ett värde som gäller för alla instanser. Repliker är vanligtvis olika för tillståndskänsliga tjänster, standard-belastningen för primär och sekundär eftersom repliker har olika typer av resurser i varje roll. Till exempel primärfärgerna vanligtvis fungerar både läsningar och skrivningar och hantera de flesta av beräkningar belastningen medan sekundärservrar inte. Vanligtvis är standard belastningen för en primär replik högre än standard belastningen för sekundära repliker. Reellt tal bör beror på dina egna mått.

## <a name="dynamic-load"></a>Dynamisk
Anta att du har kört din tjänst för en stund. Med viss övervakning upplever du som:

1. Några partitioner eller instanser av en viss tjänst förbruka mer resurser än andra
2. Vissa tjänster ha belastningen varierar över tiden.

Det finns många saker som kan leda till att dessa typer av belastningen variationer. Till exempel är olika tjänster eller partitioner associerade med olika kunder med olika krav. Läs in kan också ändra eftersom mängden arbete tjänsten varierar under loppet av dagen. Oavsett orsak är det vanligtvis ingen tal som du kan använda för standardvärde. Detta gäller särskilt om du vill ha de flesta användning utanför klustret. Ett värde som du väljer för standard är felaktigt del av tiden. Felaktig standard läser in resultatet i klustret Resource Manager antingen över eller under fördela resurser. Därför kan har du noder som över eller under används även om klustret Resource Manager tror att klustret är Balanserat. Standard-belastning är fortfarande bra eftersom de ger viss information för inledande placering, men de har inte en fullständig artikel för verkliga arbetsbelastningar. För att avbilda korrekt ändra resursbehov, kan klustret Resource Manager varje serviceobjektet att uppdatera sin egen belastning under körningen. Detta kallas dynamisk reporting.

Dynamisk rapporter kan repliker eller instanser att justera sina allokering/rapporterade belastningen på mått under sin livslängd. En replik för tjänsten eller instans som var kall och inte gör allt arbete rapporteras vanligtvis som använde små mängder av ett visst mått. En upptagen replik eller skulle rapporterar att de använder mer.

Belastningen per målrepliker eller instanser kan klustret resurshanteraren att ordna om enskilda service-objekt i klustret. Organisera om tjänsterna säkerställer att de får de resurser som de behöver. Upptagen tjänster få effektivt ”frigöra” resurser från andra repliker eller instanser som är för närvarande kall eller göra mindre arbete.

Inom Reliable Services kod för att rapportera belastning dynamiskt ser ut så här:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

En tjänst kan rapportera på någon av de mätvärden som definierats för den vid skapandet. Om en tjänst rapporter belastning för ett mått som inte är konfigurerad för att använda, ignorerar Service Fabric rapporten. Om det finns andra mått som rapporteras vid samma tidpunkt som är giltiga, godkänns rapporterna. Service-kod kan mäta och rapportera alla mått som vet hur till, och operatörer kan ange den mått konfigurationen som ska användas utan att ändra kod. 

### <a name="updating-a-services-metric-configuration"></a>Ett mått tjänstkonfigurationen uppdaterades
Listan över mått som är associerad med tjänsten och egenskaperna för dessa mått kan uppdateras dynamiskt när tjänsten är aktiv. Detta ger undersökningar och flexibilitet. Några exempel när det är praktiskt är:

  - inaktivera ett mått med en buggy rapport för en viss tjänst
  - Konfigurera om vikten av mått baserat på önskat beteende
  - Aktivera ett nytt mått när koden har redan distribuerats och godkänts via andra mekanismer
  - Ändra standard-belastningen för en tjänst baserat på observerade beteende och förbrukning

De huvudsakliga API: er för att ändra mått konfiguration är `FabricClient.ServiceManagementClient.UpdateServiceAsync` i C# och `Update-ServiceFabricService` i PowerShell. Den information som du anger med dessa API: er ersätter befintliga mått information för tjänsten omedelbart. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Blandning av belastningen standardvärden och dynamisk rapporter
Standard belastning och dynamisk last kan användas för samma tjänst. När en tjänst använder både standard belastning och dynamisk rapporter, standard belastning som fungerar som en uppskattning tills dynamiska rapporter visas. Standard belastningen är bra eftersom den ger klustret Resource Manager något att arbeta med. Standard belastningen kan klustret Resource Manager ska placeras service-objekt i god platser när de skapas. Om ingen standard belastningen information tillhandahålls är placering av tjänster effektiv slumpmässiga. När belastningen rapporter kommer senare inledande slumpmässiga placering är ofta fel och klustret Resource Manager har flytta tjänster.

Vi tar det tidigare exemplet och se vad som händer när vi lägger till vissa anpassade mått och dynamisk rapportering. I det här exemplet använder vi ”MemoryInMb” som ett exempel mått.

> [!NOTE]
> Minne är en system-mått som Service Fabric kan [resursen styr](service-fabric-resource-governance.md), och rapportering själv är vanligtvis svårt. Vi faktiskt inte förväntar dig att rapportera om minnesförbrukning; Minne används här som en hjälp att lära dig mer om funktionerna i klustret Resource Manager.
>

Vi förutsätter att det ursprungligen skapades tillståndskänslig tjänsten med följande kommando:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Som en påminnelse är den här syntaxen (”MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad”).

Nu ska vi se vad en layout för möjliga klustret kan se ut så:

<center>
![Kluster balanserad med både standard- och anpassade mått][Image2]
</center>

Vissa saker som är noteras:

* Sekundära repliker inom en partition kan ha sina egna belastning
* Mätvärdena leta övergripande belastningsutjämnade. För minne, är förhållandet mellan den högsta och lägsta lasten 1,75 (noden med mest belastningen är N3, minst är N2 och 28/16 = 1,75).

Det finns vissa saker som fortfarande behövs för att förklara:

* Vad fastställa om ett förhållande på 1,75 var rimliga? Hur klustret Resource Manager vet om som är tillräckligt bra eller om det finns mer arbete att göra?
* När sker belastningsutjämning?
* Vad innebär det att minnet var viktas ”hög”?

## <a name="metric-weights"></a>Tjänstmåttets vikt
Det är viktigt att spåra samma mått över olika tjänster. Den globala vyn är vad gör klustret Resource Manager kan spåra användning i klustret, balanserar förbrukning mellan noder och kontrollera att noderna inte gå igenom kapacitet. Tjänster kan dock ha olika vyer om vikten av samma mått. Även i ett kluster med många mått och många tjänster finns perfekt belastningsutjämnade lösningar inte för alla. Hur ska klustret Resource Manager hanterar sådana situationer?

Tjänstmåttets vikt kan klustret resurshanteraren att bestämma hur du ska hantera klustret när det finns inget perfekta svar. Tjänstmåttets vikt kan också klustret resurshanteraren att balansera specifika tjänster på olika sätt. Mått kan ha fyra olika vikt nivåer: noll, lågt, Medium och hög. Ett mått med en vikt på noll bidrar ingenting när du överväger saker är balanserade eller inte. Men bidrar belastningen fortfarande till kapacitetshantering av. Mått med noll är användbar och används ofta som en del av tjänst och prestandaövervakning. [Den här artikeln](service-fabric-diagnostics-event-generation-infra.md) finns mer information om användning av mätvärden för övervakning och diagnostik-tjänster. 

Den verkliga effekten av olika mått vikter i klustret är att klustret Resource Manager genererar olika lösningar. Tjänstmåttets vikt berätta klustret Resource Manager för att vissa mått är viktigare än andra. När det finns ingen perfekt lösning föredrar klustret Resource Manager lösningar som balanserar högre viktat mätvärdena bättre. Om en tjänst tror ett viss mått är oviktigt kan hittas deras användning av den måtten imbalanced. På så sätt kan en annan tjänsten för att hämta en jämn fördelning av vissa mått som är viktiga för den.

Nu ska vi titta på ett exempel på några belastningen rapporter och hur olika mått viktas resulterar i olika allokeringar i klustret. I det här exemplet finns att växla relativa viktade mätvärden gör att klustret resurshanteraren att skapa olika åtgärder av tjänster.

<center>
![Tjänstmåttets vikt exempel och dess påverkan på NLB lösningar][Image3]
</center>

I det här exemplet finns fyra olika tjänster, alla reporting olika värden för två olika mått, MetricA och MetricB. Alla tjänster definiera MetricA i ett fall är det viktigaste (vikt = hög) och MetricB som oviktigt (vikt = låg). Därför kan vi kan se att klustret Resource Manager placerar tjänsterna så att MetricA balanseras bättre än MetricB. ”Bättre belastningsutjämnade” innebär att MetricA har en lägre har en lägre standardavvikelse än MetricB. I det andra fallet omvända vi mått vikterna. Därför växlingar klustret Resource Manager services A och B för att få fram en allokering där MetricB är bättre belastningsutjämnade än MetricA.

> [!NOTE]
> Tjänstmåttets vikt avgöra hur klustret Resource Manager ska utjämna, men inte när NLB ska hända. Mer information om belastningsutjämning kolla [den här artikeln](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globala mått vikterna
Anta att ServiceA definierar MetricA som vikt hög och ServiceB vikten för MetricA till låg eller noll. Vad är den faktiska vikten hamnar används?

Det finns flera vikter som spåras för varje mått. Första vikten är den som angetts för måttet när tjänsten har skapats. Andra vikten är en global vikt, som beräknas automatiskt. Klustret Resource Manager använder båda dessa vikter när bedömningen lösningar. Det är viktigt att hänsyn tas till båda vikter. Detta gör att klustret Resource Manager att balansera varje tjänst enligt sin egen prioriteter och se också till att klustret som helhet fördelas på rätt sätt.

Vad som skulle hända om klustret Resource Manager inte bryr dig om både globala och lokala saldo? Det är enkelt att skapa lösningar som balanseras globalt, men vilket kan resultera i sämre resursbalansen för enskilda tjänster. I följande exempel ska vi titta på en tjänst som konfigurerats med bara mätvärdena som standard och se vad som händer när endast globala saldo betraktas som:

<center>
![Effekten av en Global endast lösning][Image4]
</center>

I översta exempel endast baseras på globalt saldo balanseras verkligen klustret som helhet. Alla noder som har samma antal primärfärgerna och samma antal totala repliker. Men om du tittar på faktiska effekten av den här allokeringen är det inte så bra: förlust av alla noder som påverkar en viss arbetsbelastning oproportionerligt, eftersom det tar bort all dess primärfärgerna. Till exempel om den första noden misslyckas tre primärfärgerna för de tre olika partitionerna för tjänsten cirkel skulle vara förlorade. Däremot har tjänsterna triangel och Sexhörning deras partitioner förlorar en replik. Detta medför att några avbrott än att återställa på repliken.

I exemplet nedre har klustret Resource Manager distribuerade repliker baserat på båda globala och per service saldot. Vid beräkning av poängen för lösningen ger de flesta av vikten till globala lösningen och en (konfigureras) del enskilda tjänster. Globala balansera för ett mått beräknas baserat på medelvärdet av mått vikterna från varje tjänst. Varje tjänst balanseras enligt sin egen definierade mått vikter. Detta säkerställer att tjänsterna är balanserade i själva efter egna behov. Därför distribueras om den första noden samma misslyckades för alla partitioner för alla tjänster. Påverkan på varje är samma.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definiera defragmentering mått är ett sätt att konsolidera belastningen på noder i stället för att sprida. Om du vill veta hur du konfigurerar defragmentering avser [i den här artikeln](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Om du vill veta mer om hur klustret Resource Manager hanterar och balanserar belastningen i klustret kan ta en titt i artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric klustret Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Förflyttningskostnad är ett sätt att signalering till klustret Resource Manager att vissa tjänster är dyrare att flytta än andra. Mer information om förflyttningskostnad avser [i den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
