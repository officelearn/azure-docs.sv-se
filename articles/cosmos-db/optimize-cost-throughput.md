---
title: Optimera dataflödet kostnaden i Azure Cosmos DB
description: Den här artikeln förklarar hur du optimerar dataflöde kostnader för data som lagras i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: e4d4d15ebb8200f16be8953e955b2e793be03c3a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452199"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimera kostnader för etablerat dataflöde i Azure Cosmos DB

Genom att erbjuda etablerat dataflöde modellen, erbjuder Azure Cosmos DB förutsebara prestanda oavsett skala. Reservera eller etablera dataflöde förväg eliminerar ”resursfördelningen effekten” på din prestanda. Du anger den exakta mängden dataflöde som du behöver och Azure Cosmos DB garanterar konfigurerade dataflöden och backas upp av ett serviceavtal (SLA).

Du kan börja med en minsta dataflöde på 400 RU/sek och skala upp till flera miljoner förfrågningar per sekund eller ännu mer. Varje begäran som du utfärdar mot ditt Azure Cosmos-behållare eller databasen, t.ex en läsbegäran, skrivbegäran, fråga, lagrade procedurer har en motsvarande kostnad som dras av från ditt etablerade dataflöde. Om du etablerar 400 RU/s och utfärda en fråga som kostar 40 ru: er, kommer du att kunna utfärda 10 sådana frågor per sekund. Varje begäran utöver det kommer att få hastighet begränsad och du bör försöka. Om du använder klientdrivrutiner stöder de automatisk omprövningslogiken.

Du kan etablera dataflöden för databaser eller behållare och varje strategi kan hjälpa dig att minska kostnaderna beroende på scenario.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimera genom att etablera dataflöde på olika nivåer

* Om du etablera dataflöde för en databas, alla behållare, kan till exempel samlingar/tabeller/diagram i databasen dela dataflödet utifrån belastningen. Dataflödet som reserverats på databasnivå delas ojämnt, beroende på arbetsbelastningen för en specifik uppsättning behållare.

* Om du etablerar dataflödet i en behållare kan garanteras dataflödet för behållaren backas upp av serviceavtalet. Valet av en logisk partitionsnyckel är avgörande för jämn fördelning av belastningen över alla logiska partitioner i en behållare. Se [partitionering](partitioning-overview.md) och [horisontell skalning](partition-data.md) artiklar för mer information.

Här följer några riktlinjer för att avgöra om en strategi för etablerat dataflöde:

**Överväg att etablera dataflöde i en Azure Cosmos DB-databas (som innehåller en uppsättning behållare) om**:

1. Du har några dussin Azure Cosmos-behållare och vill dela dataflöde över vissa eller alla. 

2. Du migrerar från en enda klient-databas som är avsedd att köras på IaaS-värd virtuella datorer eller på plats, till exempel NoSQL eller relationella databaser till Azure Cosmos DB. Och om du har många samlingar/tabeller/diagram och du inte vill göra ändringar i datamodellen. Observera att du kan behöva äventyra några av fördelarna som erbjuds av Azure Cosmos DB om du inte uppdaterar din datamodell när du migrerar från en lokal databas. Vi rekommenderar att du alltid komma datamodellen att få ut mest i termer av prestanda och optimera kostnaderna. 

3. Vill du att absorbera oväntade toppar i arbetsbelastningar tack vare pool dataflöde på databasnivå som utsatts för oväntade toppar i arbetsbelastning. 

4. I stället för att inställningen för specifika dataflöden på enskilda behållare och du bryr dig om att få den sammanlagda genomströmningen i en behållare i databasen.

**Överväg att etablera dataflöde på en enskild behållare om:**

1. Du har några Azure Cosmos-behållare. Eftersom Azure Cosmos DB är schemaoberoende, kan en behållare innehålla objekt som har heterogena scheman och kräver inte kunderna skapa flera behållartyper, en för varje entitet. Det är alltid ett alternativ att överväga om gruppering separat säger 10-20 behållare till en enskild behållare bra. Med en 400 ru: er minimum för behållare, kan det vara mer kostnadseffektivt att poolning alla 10-20 behållare till ett. 

2. Du vill styra dataflödet i en specifik behållare och få garanterat dataflöde i en given behållare som backas upp av ett serviceavtal (SLA).

**Överväg en kombination av dessa två strategier:**

1. Såsom nämnts tidigare kan Azure Cosmos DB du blanda och matcha av dessa två strategier, så du kan nu ha vissa behållare i Azure Cosmos-databasen, som kan dela dataflödet som tillhandahållits på databasen samt vissa behållare inom samma databas , som kan ha dedikerade mängder etablerat dataflöde. 

2. Du kan använda dessa strategier för att få fram en hybrid-konfiguration där du har både databasen på etablerat dataflöde med vissa behållare att ha dedikerade dataflöde.

Enligt tabellen nedan, beroende på valet av API: et kan etablera du dataflöde på olika precision.

|API|För **delade** dataflöde, konfigurera |För **dedikerade** dataflöde, konfigurera |
|----|----|----|
|API för SQL|Databas|Container|
|API för Azure Cosmos DB för MongoDB|Databas|Samling|
|Cassandra-API|Nyckelutrymme|Tabell|
|Gremlin-API|Databaskonto|Graph|
|Tabell-API|Databaskonto|Tabell|

Du kan optimera dina kostnader utifrån arbetsbelastningens beskaffenhet genom att etablering dataflöde på olika nivåer. Som vi nämnde tidigare kan du kan via programmering och en tid ökning eller minska det etablerade dataflödet för antingen enskilda behållare eller gemensamt i en behållare. Genom att skala Elastiskt dataflöde som ändringarna arbetsbelastning, betalar du bara för det dataflöde som du har konfigurerat. Om din behållare eller en uppsättning behållare distribueras i flera regioner, dataflödet du konfigurera i behållaren eller en uppsättning behållare kommer att vara tillgängliga i alla regioner.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimera med hastighetsbegränsande dina begäranden

Du kan etablera mindre dataflöde och låta programmet Hantera hastighetsbegränsande när det faktiska dataflödet överskrider det etablerade dataflödet för arbetsbelastningar som inte är känslig för fördröjning. Servern kommer sluta på begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera den `x-ms-retry-after-ms` huvud som anger hur lång tid i millisekunder som användaren måste vänta innan en ny begäran. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logik för omprövning i SDK: er 

De plattformsspecifika SDK: er (.NET/.NET Core, Java, Node.js och Python) implicit fånga upp svaret, respekterar det server angiven sidhuvudet retry-after och försök begäran. Om inte ditt konto används samtidigt av flera klienter, lyckas nästa återförsök.

Om du har mer än en för klienten kumulativt konsekvent ovan blir förfrågningsfrekvensen kanske standard antal försök för närvarande som har angetts till 9 inte tillräckligt med. I detta fall är klienten genererar ett `DocumentClientException` med status code 429 till programmet. Standardvärdet för antal återförsök kan ändras genom att ange den `RetryOptions` på ConnectionPolicy-instans. Som standard returneras DocumentClientException med statuskoden 429 efter en kumulativ väntetid på 30 sekunder om begäran fortsätter att fungera ovan blir förfrågningsfrekvensen. Detta inträffar även när det aktuella antalet återförsök är mindre än antalet försök, oavsett om det är standardvärdet 9 eller ett användardefinierat värde. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) har angetts till 3, så i detta fall om en begäransåtgärden har begränsats av som överstiger det reserverade genomflödet för insamling, begäransåtgärden försöker tre gånger innan du utlöser den undantag i programmet.  [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) är inställd på 60, så i det här fallet om cumulative återförsök väntar tid i sekunder sedan först begäran överstiger 60 sekunder, undantaget utlöses.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Partitionering och kostnader för etablerat dataflöde

Bra partitioneringsstrategin är viktigt att optimera kostnader i Azure Cosmos DB. Kontrollera att det finns inte skeva partitioner som exponeras via storage-mått. Kontrollera att det finns inte skeva dataflöde för en partition som exponeras med genomflödesmått. Kontrollera att det finns inte skeva mot specifika partitionsnycklar. Dominant nycklar i storage exponeras via mått men nyckeln är beroende av ditt åtkomst-mönster. Det är bäst att tänka på rätt logiska Partitionsnyckeln. En bra partitionsnyckel förväntas ha följande egenskaper:

* Välj en partitionsnyckel som sprids arbetsbelastning jämnt över alla partitioner och jämnt över tid. Du bör inte med andra ord har vissa nycklar till med majoriteten av data och vissa nycklar har mindre än eller inga data. 

* Välj en partitionsnyckel som möjliggör åtkomst till mönster fördelas jämnt över logiska partitioner. Arbetsbelastningen är förhållandevis även över alla nycklar. Flesta av arbetsbelastningen bör inte med andra ord fokuserar på några specifika nycklar. 

* Välj en partitionsnyckel som har ett brett urval av värden. 

Grundläggande tanken är att sprida data och aktivitet i din behållare över en uppsättning logiska partitioner, så att resurser för lagring och dataflöde som kan distribueras på de logiska partitionerna. Kandidater för partitionsnycklar kan innehålla egenskaper som ofta visas som ett filter i dina frågor. Frågor kan dirigeras effektivt genom att inkludera Partitionsnyckeln i filterpredikatet. Med sådana en partitioneringsstrategi blir optimera etablerade dataflödet mycket enklare. 

### <a name="design-smaller-items-for-higher-throughput"></a>Utforma mindre objekt för högre dataflöde 

Kostnad för begäran eller begäran om bearbetning av kostnaden för en viss åtgärd korreleras direkt till objektets storlek. Åtgärder på stora objekt kostar mer än åtgärder på mindre objekt. 

## <a name="data-access-patterns"></a>Dataåtkomstmönstren 

Det är alltid en bra idé att logiskt separera data i logiska kategorier baserat på hur ofta dataåtkomst. Du kan finjustera förbrukad lagring och dataflöde som krävs genom att kategorisera den som frekvent, medel eller kalla data. Beroende på åtkomstfrekvensen, kan du placera data i separata behållare (till exempel tabeller, diagram och samlingar) och finjustera det etablerade dataflödet på dem för att tillgodose behoven hos segmentet av data. 

Om du använder Azure Cosmos DB och du vet du inte kommer att söka efter vissa datavärden eller sällan kommer åt dem, ska du dessutom lagra komprimerade värdena för dessa attribut. Med den här metoden sparar på lagringsutrymme och index utrymme etablerat dataflöde och minska kostnaderna.

## <a name="optimize-by-changing-indexing-policy"></a>Optimera genom att ändra indexeringsprincip 

Som standard indexerar Azure Cosmos DB automatiskt varje egenskap för varje post. Detta är avsett att förenkla utvecklingen och se till att utmärkta prestanda för många olika typer av ad hoc-frågor. Om du har stora poster med tusentals egenskaper kan kanske betala dataflöde kostnaden för indexering av varje egenskap inte användbart, särskilt om du bara skickar frågor mot 10 eller 20 i dessa egenskaper. Som du närmar dig att få en referens på din arbetsbelastning, är vår vägledning för att finjustera principen index. Fullständig information om Azure Cosmos DB indexeringspolicy finns [här](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Övervakning etablerats och Förbrukat dataflöde 

Du kan övervaka det totala antalet ru som etableras, antal rate-limited begäranden samt antalet mediereserverade enheter som du har förbrukat i Azure-portalen. Följande bild visar ett exempel användningsmått:

![Övervaka begäransenheter i Azure portal](./media/optimize-cost-throughput/monitoring.png)

Du kan också ställa in aviseringar för att kontrollera om antalet rate-limited begäranden överskrider ett visst tröskelvärde. Se [så här övervakar du Azure Cosmos DB](use-metrics.md) nedan för mer information. Dessa aviseringar kan skicka ett e-postmeddelande till kontoadministratörer eller anropa en anpassad HTTP-Webhook eller en Azure-funktion för att öka etablerat dataflöde. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Skala ditt dataflöde Elastiskt och på begäran 

Eftersom du debiteras för etablerat dataflöde matchar det etablerade dataflödet efter dina behov kan hjälpa dig att undvika kostnader för oanvända dataflödet. Du kan skala ditt dataflöde upp eller ned när som helst, efter behov.  

* Övervaka användningen av dina enheter för programbegäran och förhållandet mellan pris-limited begäranden kan avslöja att du inte behöver hålla etablerade i hela konstant under hela dagen eller veckan. Du kan få mindre trafik på natten eller när det är helgen. Genom att använda Azure portal eller Azure Cosmos DB plattformsspecifika SDK: er eller REST API kan skala du ditt dataflöde när som helst. Azure Cosmos DB REST API: et tillhandahåller slutpunkter för att programmässigt uppdatera prestandanivån för dina behållare som gör det enkelt att justera dataflödet från din kod beroende på dagen eller dag i veckan. Åtgärden kan utföras utan någon avbrottstid och vanligtvis träder i kraft i mindre än en minut. 

* En av de områden som du bör skala dataflödet är när du mata in data i Azure Cosmos DB, till exempel under migreringen. När du har slutfört migreringen, kan du skala dataflöde för att hantera lösningens stabilt tillstånd.  

* Kom ihåg att faktureringen är på Granulariteten för en timme, så du inte kommer att spara pengar om du ändrar det tilldelade dataflödet oftare än en timme i taget.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Fastställa det dataflöde som krävs för en ny arbetsbelastning 

Du kan använda följande steg för att fastställa det etablerade dataflödet för en ny arbetsbelastning: 

1. Utföra en första, grov utvärderingen med capacity planner och justera dina beräkningar med hjälp av Azure Cosmos-Explorer i Azure-portalen. 

2. Vi rekommenderar för att skapa behållarna med högre dataflöde än förväntat och sedan skala efter behov. 

3. Vi rekommenderar att du använder en av de inbyggda Azure Cosmos DB SDK kan dra nytta av automatiska återförsök när begäranden får rate-limited. Om du arbetar på en plattform som inte stöds och använda Cosmos DB REST API, implementera dina egna försök igen genom att använda den `x-ms-retry-after-ms` rubrik. 

4. Kontrollera att din programkod smidigt stöder fallet när alla försök misslyckas. 

5. Du kan konfigurera aviseringar från Azure portal för att få meddelanden för frekvensbegränsning. Du kan börja med konservativ gränser som 10 rate-limited förfrågningar om de senaste 15 minuter och växla till mer eager regler när du räknat ut din faktiska användning. Tillfällig hastighetsbegränsningar är okej, de visar att du vill spela upp med gränser du har ställt in och det är precis vad du vill göra. 

6. Använd övervakning för att förstå mönster för trafik så kan du behöva justera dina dataflöde etablering över dagen eller veckan. 

7. Övervaka ditt etablerade jämfört med förbrukade dataflöde förhållande regelbundet för att se till att du inte har etablerats fler än antalet behållare och databaser. Har lite över etablerat dataflöde är en bra säkerhet-kontroll.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Bästa praxis för att optimera etablerat dataflöde 

Följande steg hjälper dig att göra dina lösningar höggradigt skalbar och kostnadseffektiv när du använder Azure Cosmos DB.  

1. Om du har betydligt över etablerat dataflöde i behållare och databaser, bör du granska RUs etablerade Vs förbrukade ru: er och finjustera arbetsbelastningarna.  

2. En metod för att uppskatta hur mycket reserverat dataflöde som krävs för programmet är att registrera begäran RU kostnaden för enheten förknippade med vanliga åtgärder mot en representativ Azure Cosmos-behållare eller databas som används av ditt program och sedan beräkna antalet åtgärder som du hade tänkt utföra varje sekund. Var noga med att mäta och ta med vanliga frågor och deras användning samt. Lära dig hur du beräkna RU kostnader för frågor via programmering eller med hjälp av portalen finns i [optimera kostnaden för frågor](online-backup-and-restore.md). 

3. Ett annat sätt att hämta åtgärder och sina kostnader i ru: er är genom att aktivera Azure Monitor-loggar, vilket ger en analys på detaljnivå av åtgärden/varaktighet och kostnad för begäran. Azure Cosmos DB tillhandahåller kostnad för begäran för varje åtgärd, så att varje åtgärdsavgift kan lagras från svaret och sedan används för analys. 

4. Du kan Elastiskt skala upp och ned etablerat dataflöde som du behöver att tillgodose behoven arbetsbelastning. 

5. Du kan lägga till och ta bort regioner som associeras med ditt Azure Cosmos-konto som du behöver och kontrollera kostnaderna. 

6. Kontrollera att du har jämn fördelning av data och arbetsbelastningar över logiska partitioner för dina behållare. Om du har ojämn partition distribution, kan detta orsaka för att etablera större mängd dataflöde än värdet som krävs. Om du har identifierat att du har en skeva distribution kan vi rekommenderar att omdistribuera arbetsbelastningen jämnt över partitionerna eller partitionera om data. 

7. Om du har många behållare och de här behållarna inte kräver serviceavtal, kan du använda erbjudandet i databasen i fall där den per dataflöden i behållare serviceavtal gäller inte. Du måste identifiera vilka av Azure Cosmos-behållare som du vill migrera till databasen på genomflödet erbjuder och sedan migrera dem med hjälp av en ändring feed-baserad lösning. 

8. Överväg att använda ”Cosmos DB kostnadsfri nivå” (kostnadsfri under ett år), Try Cosmos DB (upp till tre regioner) eller nedladdningsbara Cosmos DB-emulatorn för utveckling och testning. Du kan avsevärt sänka dina kostnader med hjälp av dessa alternativ för test-utveckling.  

9. Du kan utföra belastningsspecifikt kostnadsoptimeringar – till exempel ytterligare öka läsningar för batch-storlek, belastningsutjämning över flera regioner och ta bort duplicering av data, om tillämpligt.

10. Med Azure Cosmos DB reserverad kapacitet kan du få mer rabatt för upp till 65% i tre år. Azure Cosmos DB reserverad kapacitet modellen är ett krav på åtagande på enheter för programbegäran behövs över tid. Rabatterna nivåindelas så att flera enheter för programbegäran som du använder under en längre period, desto mer blir dina rabatter. Dessa rabatter tillämpas omedelbart. Alla ru: er som används över dina etablerade värden debiteras baserat på icke-reserverade kapacitetskostnaden. Se [Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)) för mer information. Överväg att köpa reserverade kapacitet för att ytterligare sänka dina kostnader för etablerat dataflöde.  

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta till mer information om kostnadsoptimeringar i Azure Cosmos DB i följande artiklar:

* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [förstå din faktura för Azure Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för Azure Cosmos-konton för flera regioner](optimize-cost-regions.md)

