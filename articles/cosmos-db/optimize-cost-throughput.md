---
title: Optimera kostnaden för dataflöde i Azure Cosmos DB
description: I den här artikeln beskrivs hur du optimerar dataflödeskostnaderna för data som lagras i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c80ab4acd745717e2e68ae7d9dc818594ad1ce9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501461"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB

Genom att erbjuda etablerad dataflödesmodell erbjuder Azure Cosmos DB förutsägbara prestanda i valfri skala. Reservera eller etablera dataflöde i förväg eliminerar "bullriga granne effekt" på din prestation. Du anger exakt hur mycket dataflöde du behöver och Azure Cosmos DB garanterar det konfigurerade dataflödet, som backas upp av SLA.

Du kan börja med ett minimum av dataflöde på 400 RU/sek och skala upp till tiotals miljoner begäranden per sekund eller ännu mer. Varje begäran som du utfärdar mot din Azure Cosmos-behållare eller databas, till exempel en läsbegäran, skrivbegäran, frågebegäran, lagrade procedurer har en motsvarande kostnad som dras av från ditt etablerade dataflöde. Om du etablerar 400 RU/s och utfärdar en fråga som kostar 40 ru: er, kommer du att kunna utfärda 10 sådana frågor per sekund. Varje begäran utöver det kommer att få rate-limited och du bör försöka begäran igen. Om du använder klientdrivrutiner stöder de logiken för automatiskt återförsök.

Du kan etablera dataflöden för databaser och containrar, och de olika strategierna kan ge besparingar beroende på scenariot.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimera genom att etablera dataflöde på olika nivåer

* Om du etablerar dataflöde i en databas kan alla behållare, till exempel samlingar/tabeller/diagram i databasen, dela dataflödet baserat på inläsningen. Dataflöde som reserverats på databasnivå delas ojämnt, beroende på arbetsbelastningen på en viss uppsättning behållare.

* Om du etablerar dataflöde på en behållare garanteras dataflödet för den behållaren, som backas upp av serviceavtalet. Valet av en logisk partitionsnyckel är avgörande för jämn fördelning av belastningen över alla logiska partitioner i en behållare. Mer information finns i artiklar om [partitionering](partitioning-overview.md) och [vågrät skalning.](partition-data.md)

Följande är några riktlinjer för att besluta om en etablerad strategi för dataflöde:

**Överväg att etablera dataflöde i en Azure Cosmos-databas (som innehåller en uppsättning behållare) om:**

1. Du har några dussin Azure Cosmos-behållare och vill dela dataflöde över vissa eller alla av dem. 

2. Du migrerar från en databas med en enda klient som är utformad för att köras på IaaS-värddatorn eller lokala, till exempel NoSQL eller relationsdatabaser till Azure Cosmos DB. Och om du har många samlingar / tabeller / grafer och du inte vill göra några ändringar i din datamodell. Du kanske måste kompromettera några av fördelarna med Azure Cosmos DB om du inte uppdaterar datamodellen när du migrerar från en lokal databas. Vi rekommenderar att du alltid åternjerar din datamodell för att få ut det mesta när det gäller prestanda och även för att optimera för kostnader. 

3. Du vill absorbera oplanerade toppar i arbetsbelastningar på grund av poolat dataflöde på databasnivå som utsätts för oväntade ökningar i arbetsbelastningen. 

4. I stället för att ange specifikt dataflöde på enskilda behållare bryr du dig om hur du hämtar det samlade dataflödet över en uppsättning behållare i databasen.

**Överväg att etablera dataflöde på en enskild behållare om:**

1. Du har några Azure Cosmos-behållare. Eftersom Azure Cosmos DB är schemaoberoende kan en behållare innehålla objekt som har heterogena scheman och inte kräver att kunderna skapar flera behållartyper, en för varje entitet. Det är alltid ett alternativ att överväga om gruppering separat säga 10-20 behållare i en enda behållare är vettigt. Med en 400 RU minimum för behållare, pooling alla 10-20 behållare i en kan vara mer kostnadseffektivt. 

2. Du vill styra dataflödet på en viss behållare och få det garanterade dataflödet på en viss behållare som backas upp av SLA.

**Tänk på en hybrid av ovanstående två strategier:**

1. Som tidigare nämnts kan du med Azure Cosmos DB blanda och matcha ovanstående två strategier, så att du nu kan ha några behållare i Azure Cosmos-databasen, som kan dela dataflödet som etablerats i databasen samt vissa behållare i samma databas , som kan ha särskilda mängder av avsedd dataflöde. 

2. Du kan använda ovanstående strategier för att komma med en hybridkonfiguration, där du har både databasnivå etablerat dataflöde med vissa behållare som har dedikerat dataflöde.

Som visas i följande tabell, beroende på valet av API, kan du etablera dataflöde på olika granularities.

|API|Konfigurera **shared** |Konfigurera **dedicated** |
|----|----|----|
|API för SQL|Databas|Container|
|API för Azure Cosmos DB för MongoDB|Databas|Samling|
|Cassandra-API|Nyckelutrymme|Tabell|
|Gremlin-API|Databaskonto|Graph|
|Tabell-API|Databaskonto|Tabell|

Genom att etablera dataflöde på olika nivåer kan du optimera dina kostnader baserat på egenskaperna för din arbetsbelastning. Som tidigare nämnts kan du programmässigt och när som helst öka eller minska det etablerade dataflödet för antingen enskilda behållare eller tillsammans över en uppsättning behållare. Genom att elastiskt skala dataflödet när arbetsbelastningen ändras betalar du bara för det dataflöde som du har konfigurerat. Om din behållare eller en uppsättning behållare är fördelade över flera regioner, är det garanterat att dataflödet som du konfigurerar på behållaren eller en uppsättning behållare görs tillgängligt i alla regioner.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimera med hastighetsbegränsande dina begäranden

För arbetsbelastningar som inte är känsliga för svarstid kan du etablera mindre dataflöde och låta programmet hantera hastighetsbegränsande när det faktiska dataflödet överskrider det etablerade dataflödet. Servern avslutar begäran i förebyggande syfte `RequestRateTooLarge` med (HTTP-statuskod 429) och returnerar `x-ms-retry-after-ms` huvudet som anger hur lång tid, i millisekunder, som användaren måste vänta innan han eller hon försöker igen begäran. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Återförsökslogik i SDK:er 

De inbyggda SDK:erna (.NET/.NET Core, Java, Node.js och Python) fångar implicit det här svaret, respekterar det serverspecificerade återförsöks-efter-huvudet och försöker igen begäran. Om inte ditt konto nås samtidigt av flera klienter, kommer nästa återförsök att lyckas.

Om du har mer än en klient som är kumulativt konsekvent arbetar över begäranden, kanske standardantalet för återförsök, som för närvarande är inställt på 9, inte är tillräckligt. I sådana fall kastar klienten en `RequestRateTooLargeException` med statuskod 429 till programmet. Standardantalet för återförsök kan `RetryOptions` ändras genom att ange på ConnectionPolicy-instansen. Som standard `RequestRateTooLargeException` returneras med statuskoden 429 efter en sammanlagd väntetid på 30 sekunder om begäran fortsätter att fungera över begäranden. Detta inträffar även när det aktuella antalet försök på nytt är mindre än antalet försök per max, oavsett om det är standardvärdet 9 eller ett användardefinierat värde. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) är satt till 3, så i det här fallet, om en begäran operation är hastighet begränsas genom att överskrida reserverat dataflöde för behållaren, begäran åtgärden försöker tre gånger innan du kastar undantaget till programmet. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) är inställd på 60, så i det här fallet om den kumulativa väntetiden för återförsök i sekunder sedan den första begäran överstiger 60 sekunder genereras undantaget.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Partitioneringsstrategi och kostnader för etablerat dataflöde

Bra partitioneringsstrategi är viktigt för att optimera kostnaderna i Azure Cosmos DB. Kontrollera att det inte finns någon skevhet av partitioner som exponeras via lagringsmått. Kontrollera att det inte finns någon skevhet av dataflöde för en partition, som exponeras med dataflödesmått. Se till att det inte finns någon skevhet mot vissa partitionsnycklar. Dominerande nycklar i lagring exponeras via mått, men nyckeln kommer att vara beroende av ditt programåtkomstmönster. Det är bäst att tänka på rätt logisk partitionsnyckel. En bra partitionsnyckel förväntas ha följande egenskaper:

* Välj en partitionsnyckel som sprider arbetsbelastningen jämnt över alla partitioner och jämnt över tid. Med andra ord bör du inte ha några nycklar till med majoriteten av data och vissa nycklar med mindre eller inga data. 

* Välj en partitionsnyckel som gör att åtkomstmönster kan spridas jämnt över logiska partitioner. Arbetsbelastningen är någorlunda jämn över alla nycklar. Med andra ord bör majoriteten av arbetsbelastningen inte fokuseras på några specifika nycklar. 

* Välj en partitionsnyckel som har ett brett spektrum av värden. 

Grundtanken är att sprida data och aktiviteten i behållaren över uppsättningen logiska partitioner, så att resurser för datalagring och dataflöde kan distribueras över de logiska partitionerna. Kandidater för partitionsnycklar kan innehålla de egenskaper som ofta visas som ett filter i dina frågor. Frågor kan dirigeras effektivt genom att inkludera partitionsnyckeln i filterpredicatet. Med en sådan partitionering strategi, optimera etablerat dataflöde kommer att bli mycket enklare. 

### <a name="design-smaller-items-for-higher-throughput"></a>Utforma mindre objekt för högre dataflöde 

Begärandeavgiften eller kostnaden för begäran bearbetning för en viss operation är direkt korrelerad till artikelns storlek. Operationer på stora artiklar kostar mer än operationer på mindre artiklar. 

## <a name="data-access-patterns"></a>Dataåtkomstmönster 

Det är alltid bra att logiskt separera dina data i logiska kategorier baserat på hur ofta du kommer åt data. Genom att kategorisera dem som heta, medelstora eller kalla data kan du finjustera den lagring som förbrukas och det dataflöde som krävs. Beroende på åtkomstfrekvensen kan du placera data i separata behållare (till exempel tabeller, diagram och samlingar) och finjustera det etablerade dataflödet på dem för att tillgodose behoven hos det datasegmentet. 

Om du använder Azure Cosmos DB och du vet att du inte kommer att söka efter vissa datavärden eller sällan kommer åt dem, bör du lagra de komprimerade värdena för dessa attribut. Med den här metoden sparar du på lagringsutrymme, indexutrymme och etablerat dataflöde och resulterar i lägre kostnader.

## <a name="optimize-by-changing-indexing-policy"></a>Optimera genom att ändra indexeringsprincipen 

Som standard indexerar Azure Cosmos DB automatiskt varje egenskap för varje post. Detta är avsett att underlätta utvecklingen och säkerställa utmärkt prestanda för många olika typer av ad hoc-frågor. Om du har stora poster med tusentals egenskaper kan det hända att betala dataflödeskostnaden för indexering av varje egenskap, särskilt om du bara frågar mot 10 eller 20 av dessa egenskaper. När du kommer närmare att få grepp om din specifika arbetsbelastning är vår vägledning att justera din indexpolicy. Fullständig information om Azure Cosmos DB indexeringsprincip finns [här](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Övervakning av etablerat och förbrukat dataflöde 

Du kan övervaka det totala antalet ru:er som etablerats, antalet hastighetsbegränsade begäranden samt antalet ru:er som du har förbrukat i Azure-portalen. Följande bild visar ett exempel användningsmått:

![Övervaka förfrådeenheter i Azure-portalen](./media/optimize-cost-throughput/monitoring.png)

Du kan också ställa in aviseringar för att kontrollera om antalet hastighetsbegränsade begäranden överskrider ett visst tröskelvärde. Mer information finns i Hur du övervakar Azure [Cosmos](use-metrics.md) DB-artikel. Dessa aviseringar kan skicka ett e-postmeddelande till kontoadministratörerna eller anropa en anpassad HTTP Webhook eller en Azure-funktion för att automatiskt öka etablerat dataflöde. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Skala dataflödet elastiskt och på begäran 

Eftersom du faktureras för dataflödet som etablerats kan det etablerade dataflödet hjälpa dig att undvika avgifterna för det oanvända dataflödet. Du kan skala upp eller ned det etablerade dataflödet när som helst efter behov. Om dina dataflödesbehov är mycket förutsägbara kan du använda Azure Functions och använda en timerutlösare för att [öka eller minska dataflödet enligt ett schema](scale-on-schedule.md). 

* Övervakning av förbrukningen av dina ru:er och förhållandet mellan hastighetsbegränsade begäranden kan visa att du inte behöver hålla etablerat under hela konstanten under hela dagen eller veckan. Du kan få mindre trafik på natten eller under helgen. Genom att använda azure-portalen eller Azure Cosmos DB native SDK:er eller REST API kan du skala ditt etablerade dataflöde när som helst. Azure Cosmos DB:s REST API tillhandahåller slutpunkter för att programmässigt uppdatera prestandanivån för dina behållare, vilket gör det enkelt att justera dataflödet från koden beroende på tid på dagen eller veckodagen. Åtgärden utförs utan avbrott och börjar vanligtvis gälla på mindre än en minut. 

* Ett av de områden som du bör skala dataflödet är när du intar data i Azure Cosmos DB, till exempel under datamigrering. När du har slutfört migreringen kan du skala etablerat dataflöde nedåt för att hantera lösningens steady state.  

* Kom ihåg att faktureringen är på granulariteten på en timme, så du sparar inga pengar om du ändrar ditt etablerade dataflöde oftare än en timme i taget.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Fastställa vilket dataflöde som behövs för en ny arbetsbelastning 

Om du vill ta reda på det etablerade dataflödet för en ny arbetsbelastning kan du använda följande steg: 

1. Utför en första, grov utvärdering med hjälp av kapacitetsplaneraren och justera dina uppskattningar med hjälp av Azure Cosmos Explorer i Azure-portalen. 

2. Vi rekommenderar att du skapar behållarna med högre dataflöde än förväntat och sedan skalas ned efter behov. 

3. Vi rekommenderar att du använder en av de inbyggda Azure Cosmos DB SDK:erna för att dra nytta av automatiska återförsök när begäranden blir begränsad. Om du arbetar på en plattform som inte stöds och använder Cosmos DB:s REST `x-ms-retry-after-ms` API implementerar du din egen princip för återförsök med hjälp av huvudet. 

4. Kontrollera att programkoden är smidigt stöd för ärendet när alla försök misslyckas. 

5. Du kan konfigurera aviseringar från Azure-portalen för att få meddelanden för hastighetsbegränsning. Du kan börja med konservativa gränser som 10 rate-begränsad förfrågningar under de senaste 15 minuterna och byta till mer ivriga regler när du räkna ut din faktiska konsumtion. Tillfälliga hastighetsgränser är bra, de visar att du spelar med de gränser du har angett och det är precis vad du vill göra. 

6. Använd övervakning för att förstå ditt trafikmönster, så att du kan överväga behovet av att dynamiskt justera dataflödet etablering under dagen eller en vecka. 

7. Övervaka ditt etablerade kontra förbrukade dataflödesförhållande regelbundet för att se till att du inte har etablerat mer än önskat antal behållare och databaser. Att ha lite över etablerad genomströmning är en bra säkerhetskontroll.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Metodtips för att optimera etablerat dataflöde 

Följande steg hjälper dig att göra dina lösningar mycket skalbara och kostnadseffektiva när du använder Azure Cosmos DB.  

1. Om du har betydligt över etablerat dataflöde över behållare och databaser bör du granska ru:er som etablerats jämfört med förbrukade ru:er och finjustera arbetsbelastningarna.  

2. En metod för att uppskatta mängden reserverat dataflöde som krävs av ditt program är att registrera den RU-avgift för begäranhet som är associerad med att köra typiska åtgärder mot en representativ Azure Cosmos-behållare eller databas som används av ditt program och sedan uppskatta antalet åtgärder som du räknar med att utföra varje sekund. Var noga med att mäta och inkludera typiska frågor och deras användning också. Mer information om hur du beräknar RU-kostnader för frågor programmässigt eller använder [portalen](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)finns Optimera kostnaden för frågor . 

3. Ett annat sätt att få åtgärder och deras kostnader i RU: er är genom att aktivera Azure Monitor-loggar, vilket ger dig uppdelning av drift / varaktighet och begäran avgift. Azure Cosmos DB tillhandahåller begärandeavgift för varje åtgärd, så att varje åtgärdsavgift kan lagras tillbaka från svaret och sedan användas för analys. 

4. Du kan elastiskt skala upp och ned etablerat dataflöde som du behöver för att tillgodose dina arbetsbelastningsbehov. 

5. Du kan lägga till och ta bort regioner som är associerade med ditt Azure Cosmos-konto när du behöver och kontrollera kostnader. 

6. Se till att du har jämn distribution av data och arbetsbelastningar över logiska partitioner av dina behållare. Om du har ojämn partitionsfördelning kan detta leda till att det etablerar en högre mängd dataflöde än det värde som behövs. Om du identifierar att du har en skev distribution rekommenderar vi att du distribuerar arbetsbelastningen jämnt över partitionerna eller partitionerar om data. 

7. Om du har många behållare och dessa behållare inte kräver SLA, kan du använda det databasbaserade erbjudandet för de fall där sla-systemen per behållaredataflöde inte gäller. Du bör identifiera vilka av Azure Cosmos-behållarna som du vill migrera till dataflödeserbjudandet på databasnivå och sedan migrera dem med hjälp av en ändringsfeedbaserad lösning. 

8. Överväg att använda "Cosmos DB Free Tier" (gratis i ett år), Prova Cosmos DB (upp till tre regioner) eller nedladdningsbara Cosmos DB-emulatorn för utvecklings-/testscenarier. Genom att använda dessa alternativ för test-dev, kan du avsevärt sänka dina kostnader.  

9. Du kan utföra arbetsbelastningsspecifika kostnadsoptimeringar ytterligare, till exempel öka batchstorlek, belastningsutjämningsläsningar över flera regioner och de-duplicera data, om tillämpligt.

10. Med Azure Cosmos DB reserverad kapacitet kan du få betydande rabatter för upp till 65% i tre år. Azure Cosmos DB reserverad kapacitet modell är ett förskott åtagande på begäranden enheter som behövs över tiden. Rabatterna är nivåindelade så att ju fler begäranheter du använder under en längre period, desto mer blir din rabatt. Dessa rabatter tillämpas omedelbart. Alla ru:er som används ovanför dina etablerade värden debiteras baserat på den icke-reserverade kapacitetskostnaden. Se [Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)) för mer information. Överväg att köpa reserverad kapacitet för att ytterligare sänka dina etablerade dataflödeskostnader.  

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att lära dig mer om kostnadsoptimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)

