---
title: Hantera Azure Service Fabric app belastning med hjälp av mätvärden | Microsoft Docs
description: Mer information om hur du konfigurerar och använder mått i Service Fabric för att hantera tjänsten resursförbrukning.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 2d1818f42cb2bcb19f979f25962a6c9bdea10155
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728020"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Hantera resursförbrukning och belastning i Service Fabric med mått
*Mått* vilka resurser som din dig för tjänster och som tillhandahålls av noder i klustret. Ett mått är något som du vill hantera för att förbättra eller övervaka prestanda för dina tjänster. Du kan till exempel bevaka minnesförbrukning om du vill veta om din tjänst är överbelastad. Ett annat användningsområde är att ta reda på om tjänsten kan flytta någon annanstans där minne är mindre begränsad för att få bättre prestanda.

Till exempel minne, Disk och CPU-användning är exempel på mått. De här måtten är fysiska mätvärden, resurser som motsvarar fysiska resurser på den nod som måste hanteras. Mått kan också vara (och ofta är) logiska mått. Logiska mått är till exempel ”MyWorkQueueDepth” eller ”MessagesToProcess” eller ”TotalRecords”. Logiska mått är programdefinierade och motsvarar indirekt vissa fysiska resursförbrukning. Logiska mått är vanliga eftersom det kan vara svårt att mått och rapportera användning av fysiska resurser på basis av per tjänst. Komplexiteten för att mäta och rapportera dina egna fysiska mått är också varför Service Fabric tillhandahåller vissa standard-mått.

## <a name="default-metrics"></a>Standard-mått
Anta att du vill börja skriva och distribuera din tjänst. Nu vet du inte vilka fysiska eller logiska resurser som har det förbrukar. Det är bra! Service Fabric Cluster Resource Manager använder vissa standardstatistik när inga andra mått har angetts. De är:

  - PrimaryCount - antal primära repliker på noden 
  - ReplicaCount - antal totala tillståndskänsliga repliker på noden
  - Antal – antal alla service-objekt (tillståndslösa och tillståndskänsliga) på noden

| Mått | Tillståndslösa instans belastning | Tillståndskänsliga sekundära belastning | Tillståndskänsliga primära belastning | Vikt |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Hög |
| ReplicaCount |0 |1 |1 |Medel |
| Antal |1 |1 |1 |Låg |


För grundläggande arbetsbelastningar och ange mått som standard en vettigt fördelning av arbete i klustret. I följande exempel, låt oss se vad som händer när vi skapar två tjänster och förlitar sig på standard-mått för belastningsutjämning. Den första tjänsten är en tillståndskänslig tjänst med tre partitioner och en mål-replik ange storleken på tre. Andra tjänsten är en tillståndslös tjänst med en partition och ett instansantal på tre.

Här är vad du får:

<center>
![Klustret Layout med standard-mått][Image1]
</center>

Några saker att tänka på:
  - Primära repliker för den tillståndskänsliga tjänsten som är distribuerade över flera noder
  - Repliker för samma partition som finns på olika noder
  - Det totala antalet USA: s presidentval och sekundärservrar distribueras i klustret
  - Det totala antalet-tjänstobjekt fördelas jämnt på varje nod

Bra!

Mått som standard fungerar bra som en start. Men har mått som standard bara du hittills. Exempel: Vad är sannolikheten att partitionering system som du valt resultat i perfekt även användningen av alla partitioner? Vad är risken att belastningen för en viss tjänst är konstant över tid eller även över flera plattformar på samma just nu?

Du kan köra med bara mått som standard. Detta vanligtvis innebär dock att din klusteranvändning är lägre och mer ojämn än vad du vill ha. Detta beror på mått som standard inte anpassningsbar och förutsätter att allt är likvärdiga. Till exempel bidra en primär är upptagen och som inte är båda ”1” till PrimaryCount mått. I värsta fall kan med bara mått som standard också resultera i overscheduled noder, vilket resulterar i prestandaproblem. Om du är intresserad av att få ut mest av ditt kluster och undvika problem med prestanda kan behöva du använda anpassade mått och rapportering om dynamisk belastning.

## <a name="custom-metrics"></a>Anpassade mått
Mått är konfigurerade på basis av per med namnet-service-instans när du skapar tjänsten.

Vilka mått som har vissa egenskaper som beskriver den: ett namn, en vikt och en standard-belastning.

* Måttnamn: Namnet på måttet. Måttnamnet är en unik identifierare för mått i klustret ur Resource Manager.
* Vikt: Tjänstmåttets vikt definierar hur viktigt det här måttet är i förhållande till andra mått för den här tjänsten.
* Standard-belastning: Standard load representeras på olika sätt beroende på om tjänsten är tillståndslösa eller tillståndskänsliga.
  * För tillståndslösa tjänster kan har varje mått en enskild egenskap med namnet DefaultLoad
  * För tillståndskänsliga tjänster som du definierar:
    * PrimaryDefaultLoad: Det här måttet standardmängden förbrukar den här tjänsten när den är en primär
    * SecondaryDefaultLoad: Det här måttet standardmängden förbrukar den här tjänsten när den är en sekundär

> [!NOTE]
> Om du definierar anpassade mått och du vill _också_ använder mått som standard, måste du _uttryckligen_ lägga till mått som standard tillbaka och anger vikterna och värden för dessa. Det beror på att du måste definiera relationen mellan mått som standard och din anpassade mått. Till exempel kanske du bryr dig om ConnectionCount eller WorkQueueDepth mer än primära. Som standard är vikten för måttet PrimaryCount hög, så att du vill minska det till medel när du lägger till din andra mått för att se till att de har företräde.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiera mått för din tjänst - exempel
Anta att du vill konfigurera följande:

  - Tjänsten rapporterar ett mått med namnet ”ConnectionCount”
  - Du vill använda standard-mått 
  - Du har gjort vissa mätning av faktisk användning och vet att en primär replik av den tjänsten tar normalt 20 enheter för ”ConnectionCount”
  - Sekundärservrar använder 5 enheter av ”ConnectionCount”
  - Du vet att ”ConnectionCount” är det viktigaste måttet när det gäller hantering av prestanda för den här viss tjänst
  - Vill primära repliker nätverksbelastning. Belastningsutjämning primära repliker är vanligtvis bra oavsett vad. Detta förhindrar förlusten av en nod eller fel domän påverkar en majoritet av primära repliker tillsammans med den. 
  - I annat fall är mått som standard bra

Här är koden som du skulle skriva för att skapa en tjänst med mått konfigurationen:

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
> Ovanstående exempel och resten av det här dokumentet beskriver du hantera mått på basis av per med namnet-tjänst. Det är också möjligt att definiera mått för dina tjänster på tjänsten _typ_ nivå. Detta åstadkoms genom att ange dem i din tjänstmanifest. Du bör inte definiera statistik för typ av flera skäl. Det första skälet är att Måttnamn ofta miljöspecifika. Om det inte finns ett fast avtal på plats, kan du vara säker på att mått ”kärnor” i en miljö inte är ”MiliCores” eller ”kärnor” i andra. Om dina mått har definierats i ditt manifest som du behöver skapa nya manifest per miljö. Detta leder ofta till en ökningen av olika manifest med bara smärre skillnader, vilket kan leda till problem med hantering.  
>
> Metrisk belastningar tilldelas ofta på basis av per med namnet-service-instans. Anta exempelvis att du skapar en instans av tjänsten för CustomerA som planerar att använda den inte. Anta också att du skapar ett annat för CustomerB som har en större arbetsbelastning. I det här fallet skulle du förmodligen vill justera belastningar som standard för dessa tjänster. Om du har mått och belastningar som anges via manifest och du vill kunna använda det här scenariot, kräver olika program och typer av tjänster för varje kund. De värden som definieras vid tidpunkten för skapandet av tjänsten åsidosätta de som anges i manifestet, så att du kan använda som för att ange specifika standardvärdena. Dock gör gör det de värden som deklarerats i manifest så att den inte matchar de som tjänsten körs faktiskt med. Detta kan leda till förvirring. 
>

Som en påminnelse: Om du bara vill använda mått som standard behöver du inte alls touch samlingen mått eller göra något speciellt när du skapar din tjänst. Mått som standard används hämta automatiskt när inga andra har definierats. 

Nu ska vi gå igenom dessa inställningar i detalj och prata om det beteende som den inverkar.

## <a name="load"></a>Läsa in
Hela syftet med att definiera mått är att representera vissa belastningen. *Läs in* är hur mycket av ett visst mått som används av vissa tjänstinstansen eller repliken på en viss nod. Belastningen kan konfigureras på nästan vilken punkt. Exempel:

  - Belastningen kan definieras när en tjänst har skapats. Detta kallas _standard load_.
  - Metrisk information, inklusive standard belastning, för en tjänst kan uppdateras när tjänsten har skapats. Detta kallas _uppdatera en tjänst_. 
  - Belastning för en given partition kan återställas till standardvärdena för tjänsten. Detta kallas _återställer partitionsinläsning_.
  - Belastningen kan rapporteras på en per tjänst objektet basis dynamiskt vid körning. Detta kallas _reporting belastningen_. 
  
Alla dessa strategier kan användas inom samma tjänst under livslängden. 

## <a name="default-load"></a>Standard load
*Standard load* är hur mycket av det mått som förbrukar varje serviceobjektet (tillståndslös instans eller tillståndskänslig replik) för den här tjänsten. Cluster Resource Manager använder det här numret för belastningen på objektet tills det mottar annan information, till exempel en dynamisk-rapport. Standard belastningen är en statisk definition för enklare tjänster. Standard load uppdateras aldrig och används för livslängden för tjänsten. Standard arbetsbelastning fungerar bra för enkel kapacitetsplanering för scenarier där vissa mängder resurser har reserverats för olika arbetsbelastningar och ändras inte.

> [!NOTE]
> Mer information om kapacitetshantering av och definiera kapaciteter för noderna i klustret finns i [i den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Cluster Resource Manager kan tillståndskänsliga tjänster att ange en annan standard-belastningen för sina USA: s presidentval och sekundära databaser. Tillståndslösa tjänster kan bara ange ett värde som gäller för alla instanser. För tillståndskänsliga tjänster, standard-belastningen för primära och sekundära skiljer repliker sig vanligtvis eftersom repliker göra olika typer av arbete i varje roll. Till exempel USA: s presidentval vanligtvis fungerar både läsningar och skrivningar och hantera de flesta av databaserad belastningen medan sekundära databaser inte. Standard-belastningen för en primär replik är vanligtvis högre än standard-belastningen för sekundära repliker. Reella tal bör beror på dina egna mått.

## <a name="dynamic-load"></a>Dynamisk belastning
Anta att du har kört din tjänst under en tid. Du har märkt som med viss övervakning:

1. Vissa partitioner eller instanser av en viss tjänst använda fler resurser än andra
2. Vissa tjänster har belastning som varierar över tid.

Det finns många olika saker som kan orsaka dessa typer av belastningen variationer. Till exempel är olika tjänster eller partitioner associerade med olika kunder med olika krav. Belastningen kan även ändras eftersom mängden arbete som tjänsten varierar under loppet av dagen. Oavsett orsak är det vanligtvis inget enskilt tal som du kan använda för standard. Detta gäller särskilt om du vill hämta de flesta användning från klustret. Ett värde som du väljer för standard är felaktigt del av tiden. Felaktig standard läser in resultatet i Cluster Resource Manager över eller under tilldela resurser. Därför kan ha du noder som är över eller under används även om Klusterresurshanteraren tror att klustret är balanserade. Standard belastning är fortfarande bra eftersom de ger viss information för inledande placering, men de är inte en helt representativt för verkliga arbetsbelastningar. Om du vill samla in föränderliga resurskraven korrekt, kan Cluster Resource Manager varje service-objekt att uppdatera sin egen belastning under körning. Detta kallas rapportering om dynamisk belastning.

Dynamisk rapporter kan repliker eller instanser för att justera sina allokering/rapporterade belastningen på mått under deras livstid. En tjänsterepliken eller instans som var kall och inte gör allt arbete skulle vanligtvis rapporterar att den använde små mängder av ett visst mått. En upptagen målreplik eller skulle rapporterar att de använder mer.

Reporting belastningen per målrepliker eller instanser kan Cluster Resource Manager att ordna om enskilda service-objekt i klustret. Organisera om tjänsterna hjälper till att säkerställa att de får de resurser som de behöver. Upptagen tjänster få effektivt ”frigöra” resurser från andra repliker eller instanser som är för närvarande kall eller göra mindre arbete.

I Reliable Services koden för att rapportera belastning dynamiskt ser ut så här:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

En tjänst kan rapportera på någon av de mått som definierats för den vid tidpunkten för skapandet. Om en tjänstinläsning rapporter för ett mått som inte är konfigurerad för att använda, ignorerar den rapporten i Service Fabric. Om det finns andra mått rapporteras samtidigt som är giltiga, accepteras rapporterna. Koden kan mäta och rapportera alla mått som den vet hur du, och operatörer kan ange mått konfigurationen kan använda utan att behöva ändra kod. 

### <a name="updating-a-services-metric-configuration"></a>Uppdaterar konfigurationen för en tjänst-mått
I listan över mått som är associerad med tjänsten och egenskaperna för de mått som kan uppdateras dynamiskt när tjänsten är aktiv. På så sätt kan undersökningar och flexibilitet. Några exempel på när detta är användbart är:

  - inaktiverar ett mått med en buggy rapport för en viss tjänst
  - Konfigurera om vikten av mått baserat på önskat beteende
  - Aktivera ett nytt mått när koden har redan distribuerats och godkänts via andra mekanismer
  - Ändra standard-belastningen för en tjänst utifrån observerad beteende och förbrukning

De huvudsakliga API: er för att ändra konfigurationen för mått är `FabricClient.ServiceManagementClient.UpdateServiceAsync` i C# och `Update-ServiceFabricService` i PowerShell. Den information som du anger med dessa API: er ersätter den befintliga mått informationen om tjänsten omedelbart. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Blanda standardvärden för inläsning och dynamisk rapporter
Standard load och dynamisk belastning kan användas för samma tjänst. När en tjänst använder både standard belastning och dynamisk rapporter, standard belastning som fungerar som en uppskattning tills dynamiska rapporter visas. Standard-belastningen är bra eftersom den ger Klusterresurshanteraren något att arbeta med. Standard-belastningen gör Cluster Resource Manager att placera service-objekt på bra platser när de skapas. Om ingen information om standard load anges, är placeringen av tjänster effektivt slumpmässigt. När belastningen rapporter kommer senare inledande slumpmässiga placering är ofta fel och Cluster Resource Manager måste flytta tjänster.

Låt oss ta vårt tidigare exempel och se vad som händer när vi lägger till vissa anpassade mått och rapportering om dynamisk belastning. I det här exemplet använder vi ”MemoryInMb” som en exempel-mått.

> [!NOTE]
> Minnet är något av de systemmått som Service Fabric kan [resursen styr](service-fabric-resource-governance.md), och rapportering själv är vanligtvis svårt. Vi faktiskt tror inte du att rapportera om minnesförbrukning; Minnet används här som en hjälp att lära dig mer om funktionerna för Cluster Resource Manager.
>

Vi förutsätter vi ursprungligen skapats den tillståndskänsliga tjänsten med följande kommando:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Den här syntaxen är (”MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad”) som en påminnelse.

Låt oss se vilka ett möjligt kluster layout gick ut:

<center>
![Klustret balanserad med både standard och anpassade mått][Image2]
</center>

Några saker som är värt:

* Sekundära repliker inom en partition kan ha sina egna belastning
* Måtten titta övergripande belastningsutjämnade. Förhållandet mellan den högsta och lägsta belastningen är för minne, 1,75 (noden med mest belastning är N3, minst är N2 och 28/16 = 1,75).

Det finns några saker som vi behöver för att förklara:

* Vad fastställa om ett förhållande på 1,75 var detta rimligt? Hur Cluster Resource Manager vet om det är tillräckligt bra eller om det finns mer arbete att göra?
* När sker belastningsutjämning?
* Vad innebär det att minnet var viktad ”hög”?

## <a name="metric-weights"></a>Tjänstmåttets vikt
Det är viktigt att spåra mått som är samma mellan olika tjänster. Den globala vyn innebär det att Cluster Resource Manager att spåra användningen i klustret, balansera förbrukning över noder och se till att noderna inte går över kapacitet. Tjänster kan dock ha olika vyer om vikten av samma mått. Även i ett kluster med många mått och många olika tjänster, kanske perfekt balans lösningar inte finns för alla mått. Hur ska Cluster Resource Manager hanterar sådana situationer?

Metrisk vikterna Tillåt Cluster Resource Manager för att avgöra hur du balansera klustret när det finns inget perfekta svar. Tjänstmåttets vikt även låta Cluster Resource Manager att balansera specifika tjänster på olika sätt. Mått kan ha fyra olika vikt nivåer: Noll, låg, Medium och hög. Ett mått med en vikt på noll bidrar ingenting när du överväger om saker balanseras eller inte. Men bidrar belastningen fortfarande till kapacitetshanteringsområde. Mått med noll vikt är fortfarande användbart och används ofta som en del av tjänstbeteende och prestandaövervakning. [Den här artikeln](service-fabric-diagnostics-event-generation-infra.md) finns mer information om användning av mått för övervakning och diagnostik för dina tjänster. 

Verkliga effekten av olika mått vikter i klustret är att Cluster Resource Manager genererar olika lösningar. Metrisk vikterna berätta Cluster Resource Manager för att vissa mått är viktigare än andra. När det finns ingen perfekt lösning kan Klusterresurshanteraren föredrar lösningar som balanserar högre viktad måtten bättre. Om en tjänst uppfattar som ett visst mått oviktigt kan hittas deras användning av det måttet imbalanced. På så sätt kan en annan tjänst att få en jämn fördelning av vissa mått som är viktiga för den.

Låt oss titta på ett exempel på vissa belastningen rapporter och hur olika mått viktas resultat i olika allokeringar i klustret. I det här exemplet Se vi att byta den relativa vikten av mätvärden orsakar Cluster Resource Manager för att skapa olika uppställningar av tjänster.

<center>
![Tjänstmåttets vikt exempel och dess påverkan på nätverksbelastning lösningar][Image3]
</center>

I det här exemplet finns fyra olika tjänster, alla reporting olika värden för två olika mått, MetricA och MetricB. I ett fall definiera alla tjänster MetricA är det viktigaste (vikt = hög) och MetricB som oviktiga (vikt = låg). Därför kan se vi att Cluster Resource Manager placerar tjänsterna så att MetricA fördelas bättre än MetricB. ”Bättre belastningsutjämnade” innebär att MetricA har ett lägre har en lägre standardavvikelse än MetricB. I det andra fallet kan omvänd vi mått vikterna. Klusterresurshanteraren växlingar därför tjänster A och B för att få fram en allokering där MetricB bättre är balanserade än MetricA.

> [!NOTE]
> Metrisk vikterna avgöra hur Cluster Resource Manager ska stämma överens, men inte när belastningsutjämning som ska hända. Mer information om belastningsutjämning Kolla in [i den här artikeln](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Global mått vikterna
Vi antar att ServiceA definierar MetricA som vikt hög och ServiceB vikten för MetricA till låg eller noll. Vad är den faktiska vikten identisk vänja?

Det finns flera vikterna som spåras för varje mått. Den första vikten är den som definierats för måttet när tjänsten skapas. Vikten som helst är en global vikt, som beräknas automatiskt. Cluster Resource Manager använder båda dessa vikterna när bedömning lösningar. Det är viktigt att hänsyn tas till båda vikterna. På så sätt kan Cluster Resource Manager så att balansera varje tjänst enligt sin egen prioriteringar Se också till att klustret som helhet tilldelas korrekt.

Vad som skulle hända om Cluster Resource Manager inte bryr dig om både globala och lokala saldo? Det är enkelt att skapa lösningar som balanseras globalt, men vilket resultera i sämre resursbalansen för enskilda tjänster. I följande exempel ska vi titta på en tjänst som konfigurerats med bara mått som standard och se vad som händer när endast globala saldo anses:

<center>
![Effekten av en Global endast lösning][Image4]
</center>

I övre exempel baseras på global saldo fördelas verkligen klustret som helhet. Alla noder har samma antal USA: s presidentval och samma antal totala replikerna. Om du tittar på den faktiska effekten av den här allokering det är dock inte så bra: förlusten av en nod påverkar en viss arbetsbelastning oproportionerligt, eftersom det tar bort all dess USA: s presidentval. Till exempel om den första noden misslyckas tre USA: s presidentval för tre olika partitioner av tjänsten cirkel skulle alla gå förlorade. Däremot har tjänsterna triangel och Sexhörning deras partitioner förlorar en replik. Detta gör att inga avbrott än att behöva återställa på repliken.

Cluster Resource Manager har distribuerade repliker baserat på båda globala och per tjänst saldo i exempel längst ned. När du beräknar resultatet av lösningen ger de flesta av vikten till global lösning och en (kan konfigureras) del till enskilda tjänster. Global balans för ett mått beräknas baserat på genomsnittliga av mått vikterna från varje tjänst. Varje tjänst är belastningsutjämnad enligt egna definierade mått vikterna. Detta säkerställer att tjänsterna är balanserade i själva efter egna behov. Om samma första nod misslyckas felet är därför fördelat över alla partitioner i alla tjänster. Påverkan på var och en är samma.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Läs om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definiera defragmentering mått är ett sätt att konsolidera belastningen på noder i stället för sprida. Läs hur du konfigurerar defragmentering [i den här artikeln](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret kan du läsa artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Förflyttningskostnad är ett sätt att signalering till Cluster Resource Manager att vissa tjänster är dyrare att flytta än andra. Läs mer om förflyttningskostnad [i den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
