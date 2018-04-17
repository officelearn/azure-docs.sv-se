---
title: Partitionering och teckenbredden i Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitions-nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fcb33dff131106fd801b72a0bfaafd528d9f1af9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partitionera och skala i Azure Cosmos DB

[Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) är en global, multimodel databas-tjänst som hjälper dig uppnå snabb och förutsägbar prestanda. Skalningen sömlöst tillsammans med ditt program när den växer. Den här artikeln innehåller en översikt över hur partitionering fungerar för alla data modeller i Azure Cosmos-databasen. Här beskrivs också hur du kan konfigurera Azure Cosmos DB behållare för att effektivt skala ditt program.

Partitionering och partitionsnycklar beskrivs i den här videon med Azure Cosmos DB Programhanteraren Andrew Liu:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB
Du kan lagra och fråga schemat mindre data med ordning millisekunder svarstider skaländras i Azure Cosmos-databasen. Azure Cosmos-DB innehåller behållare för lagring av data kallas *samlingar* (för dokument) *diagram*, eller *tabeller*. 

Behållare är logiska nätverksresurser och kan sträcka sig över en eller flera partitioner fysiska servrar. Antalet partitioner bestäms av Azure Cosmos DB baserat på lagringsstorleken och etablerat dataflöde på behållaren. 

En fysisk partition är en fast mängd reserverade SSD-baserad lagring. Varje fysiska partition replikeras för hög tillgänglighet. En eller flera fysiska partitioner utgör en behållare. Hantering av fysiska partition hanteras helt av Azure Cosmos DB och du behöver inte skriva komplex kod eller hantera din partitioner. Azure DB Cosmos-behållare är obegränsad lagring och genomflöde. 

En logisk partition är en partition inom en fysiska partition som lagrar alla data som är associerade med värdet för en enskild partition. En logisk partition har en 10 GB max. I följande diagram visas har en enskild behållare tre logiska partitioner. Varje logisk partition lagrar data för en partitionsnyckel, LAX och AMS MEL respektive. Alla logiska partitioner LAX och AMS MEL växer inte gräns maximala logisk partition på 10 GB. 

![Resursen partitionering](./media/introduction/azure-cosmos-db-partitioning.png) 

När en samling uppfyller de [partitionering krav](#prerequisites), partitionering är transparent för programmet. Azure Cosmos-DB stöder snabb läsning och skrivning, frågor, transaktionella logik, konsekvensnivåer och detaljerad åtkomstkontroll via metoder/API: er till en enskild behållare resurs. Tjänsten handtagen distribuerar data över fysiska och logiska partitioner och routning fråga begäranden till rätt partition. 

## <a name="how-does-partitioning-work"></a>Hur fungerar partitionering

Hur fungerar partitionering? Varje objekt måste ha en partitionsnyckel och en rad, som kan identifieras. Din partitionsnyckel fungerar som en logisk partition för dina data och ger Azure Cosmos DB en naturlig gräns för att distribuera data över fysiska partitioner. Kom ihåg att data för en enskild logisk partition måste finnas inuti en enda fysisk partition, men fysiska partition management hanteras av Azure Cosmos DB. 

I korthet är här hur partitionering fungerar i Azure Cosmos DB:

* Du etablerar en Azure DB som Cosmos-behållare med **T** begäranden per andra genomflöde.
* I bakgrunden, etablerar Azure Cosmos DB partitioner som behövs för att hantera **T** begäranden per sekund. Om **T** är högre än det största genomflödet per partition **t**, sedan Azure Cosmos DB tillhandahåller **N = T/t** partitioner.
* Azure Cosmos-DB allokerar viktiga utrymme på partitionen viktiga hashvärden jämnt i bredd i **N** partitioner. Varje partition (fysiska partition) värdar så **1/N** partitions nyckelvärdena (logiska partitioner).
* När en fysisk partition **p** når sin lagringsgräns, Azure Cosmos DB sömlöst delar upp **p** till två nya partitioner **p1** och **p2** . Den distribuerar värden som motsvarar ungefär hälften nycklar till var och en av partitionerna. Den här delade åtgärden är osynliga för ditt program. Om en fysiska partition når sin lagringsgräns och alla data på den fysiska partitionen tillhör samma logiska partitionsnyckel, uppstår inte split-åtgärden. Detta beror på att alla data för en enskild logisk partitionsnyckel måste finnas i samma fysiska partition och den fysiska partitionen kan därför delas in p1 och p2. I det här fallet bör en annan partition viktiga strategi användas.
* När du etablerar genomströmning som är högre än **t * N**, Azure Cosmos DB delar upp en eller flera partitioner att stödja högre genomströmning.

Semantik för partitionsnycklar är något annorlunda att matcha semantiken för varje API, som visas i följande tabell:

| API | Partitionsnyckeln | Radnyckel |
| --- | --- | --- |
| Azure Cosmos DB | Anpassad partitionering nyckelsökvägen | Åtgärdade `id` | 
| MongoDB | Anpassade delad nyckel  | Åtgärdade `_id` | 
| Graph | Anpassad partitionering nyckelegenskapen | Åtgärdade `id` | 
| Tabell | Åtgärdade `PartitionKey` | Åtgärdade `RowKey` | 

Azure Cosmos-DB använder hash-baserad partitionering. När du skriver ett objekt Azure Cosmos DB hashar nyckelvärdet partition och använder hashformaterats resultatet för att avgöra vilken partition för att lagra objekt i. Azure Cosmos-DB lagrar alla objekt med samma partitionsnyckel i samma fysiska partition. Valet av Partitionsnyckeln är ett viktigt beslut som du behöver göra i designläge. Du måste välja ett egenskapsnamn som har ett stort antal värden och har även åtkomstmönster. Om en fysiska partition når sin lagringsgräns och samma partitionsnyckel finns på alla data i partitionen Azure Cosmos DB Returnerar ”partitionsnyckel nått maximal storlek på 10 GB”-fel och partitionen inte dela därför att välja en partitionsnyckel är en mycket import Ant beslut.

> [!NOTE]
> Det är bäst att ha en partitionsnyckel med många distinkta värden (500 till 1 000 minst).
>

Azure DB Cosmos-behållare kan skapas som *fast* eller *obegränsade* i Azure-portalen. Behållare med fast storlek har en maxgräns på 10 GB och en genomströmning på 10 000 RU/s. Om du vill skapa en behållare som obegränsade, måste du ange en lägsta dataflöde på 1 000 RU/s och du måste ange en partitionsnyckel.

Det är en bra idé att kontrollera hur dina data är distribuerad i partitioner. Du kan kontrollera detta i portalen, gå till Azure DB som Cosmos-konto och klicka på **mått** i **övervakning** avsnittet och klicka sedan i den högra rutan på **lagring** fliken för att se hur dina data är partitionerade i olika fysiska partition.

![Resursen partitionering](./media/partition-data/partitionkey-example.png)

Vänster bild visar resultatet av en felaktig partitionsnyckel och den högra bilden visar resultatet av en bra partitionsnyckel. Du kan se data inte är fördelas jämnt mellan partitioner i vänster bild. Du bör sträva efter att distribuera dina data så att diagrammet liknar rätt avbildning.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Krav för partitionering

För fysiska partitioner för att automatiskt dela upp i **p1** och **p2** enligt beskrivningen i [hur fungerar partitionering arbete](#how-does-partitioning-work), behållaren måste skapas med en genomströmning på 1 000 RU/s eller mer , och en partitionsnyckel som måste anges. När du skapar en behållare i Azure portal väljer den **obegränsad** kapacitet lagringsalternativ dra nytta av partitionering och automatisk skalning. 

Om du har skapat en behållare i Azure-portalen eller programmässigt och inledande kapaciteten var 1 000 RU/s eller mer, och data innehåller en partitionsnyckel, du kan dra nytta av partitionering utan ändringar av din behållaren - detta inkluderar **fast**  storlek behållare, så länge den första behållaren har skapats med minst 1 000 RU/s i througput och en partitionsnyckel finns i data.

Om du har skapat en **fast** storlek behållare med någon partition nyckel eller skapat ett **fast** storlek behållare med genomflödet som är mindre än 1 000 RU/s, behållaren kan inte automatiskt-dela enligt beskrivningen i den här artikeln. Om du vill migrera data från behållaren så här till ett obegränsat antal behållare (en med minst 1 000 RU/s genomströmning och en partitionsnyckel), måste du använda den [datamigreringsverktyget](import-data.md) eller [ändra Feed biblioteket](change-feed.md) till Migrera ändringarna. 

## <a name="partitioning-and-provisioned-throughput"></a>Partitionering och etablerat dataflöde
Azure Cosmos-DB är utformad för förutsägbar prestanda. När du skapar en behållare kan du reservera genomflöde i  *[programbegäran](request-units.md) (RU) per sekund*. Varje begäran har tilldelats ett RU-tillägg som är proportionell mot mängden systemresurser som CPU, minne och i/o som används av åtgärden. En läsning av ett 1-KB-dokument med sessionskonsekvens förbrukar 1 RU. Läs är 1 RU oavsett hur många objekt som lagras eller antalet samtidiga begäranden som körs på samma gång. Större objekt kräver högre RUs beroende på storleken. Om du vet storleken på dina enheter och antalet läsningar som du behöver stöd för ditt program kan etablera du den exakta mängden dataflödet som krävs för ditt program är behöver läsa. 

> [!NOTE]
> Du måste välja en partitionsnyckel som gör det möjligt att fördela begäranden mellan vissa distinkta partitionsnyckelvärden för att uppnå det totala genomflödet i behållaren.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Arbeta med Azure Cosmos DB-API: er
Du kan använda Azure-portalen eller Azure CLI för att skapa behållare och skala dem när som helst. Det här avsnittet visar hur du skapar behållare och ange definitionen av genomflöde och partition i var och en API som stöds.

### <a name="sql-api"></a>API för SQL
I följande exempel visas hur du skapar en behållare (insamling) med hjälp av Azure Cosmos DB SQL API. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Du kan läsa ett objekt (dokument) med hjälp av den `GET` metod REST API eller genom att använda `ReadDocumentAsync` i något av de SDK: er.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Mer information finns i [partitionering i Azure Cosmos-databasen med SQL-API](sql-api-partition-data.md).

### <a name="mongodb-api"></a>MongoDB-API
Du kan skapa ett delat samling via din favorit-verktyget, drivrutiner och SDK med MongoDB-API. I det här exemplet använder vi Mongo-gränssnittet för att skapa samlingen.

I Mongo-gränssnittet:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Resultat:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>Tabell-API

Använd metoden CreateIfNotExists om du vill skapa en tabell med hjälp av Azure Cosmos DB tabell-API. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Dataflöde har angetts som argument för CreateIfNotExists.

Partitionsnyckeln skapas implicit som den `PartitionKey` värde. 

Du kan hämta en enda entitet med hjälp av följande utdrag:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Mer information finns i [utveckla med tabell-API: et](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>Graph API

Med Graph-API måste du använda Azure-portalen eller Azure CLI för att skapa behållare. Du kan också eftersom Azure Cosmos DB multimodel, kan du använda en av de andra modellerna att skapa och skala graph-behållaren.

Du kan läsa alla hörn eller en kant med partitionsnyckel och ID: T i Gremlin. För ett diagram med region (”USA”) som partitionsnyckel och ”Seattle” som Radnyckeln kan du hitta en nod med hjälp av följande syntax:

```
g.V(['USA', 'Seattle'])
```

Du kan referera en kant med hjälp av Partitionsnyckeln och Radnyckeln.

```
g.E(['USA', 'I5'])
```

Mer information finns i [använder ett partitionerat diagram i Azure Cosmos DB](graph-partitioning.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Design för partitionering
Om du vill skala effektivt med Azure Cosmos DB, måste du välja en bra partitionsnyckel när du skapar en behållare. Det finns två huvudsakliga överväganden för att välja en partitionsnyckel:

* **Gräns för frågan och transaktioner**. Ditt val av partitionsnyckel bör balansera behovet av att aktivera användning av transaktioner mot kravet att distribuera din entiteter över flera partitionsnycklar för att säkerställa en skalbar lösning. Du kan ange samma partitionsnyckel för alla objekt i en extreme, men det här alternativet kan begränsa skalbarhet i lösningen. Du kan tilldela en unik partitionsnyckel för varje objekt på andra extreme. Det här alternativet är mycket skalbar, men det hindrar dig från att använda transaktioner mellan dokument via lagrade procedurer och utlösare. En perfekt partitionsnyckel kan du använda effektiva frågor och kardinalitet är tillräcklig för att säkerställa att din lösning är skalbart. 
* **Inga lagrings- och prestandakrav flaskhalsar**. Det är viktigt att välja en egenskap som gör att skriver över olika distinkta värden. Begäranden till samma partitionsnyckel får inte överskrida genomflödet av en enskild partition och har begränsats. Det är därför viktigt att välja en partitionsnyckel inte resulterar i ”aktiva punkter” i ditt program. Eftersom alla data för en enda partitionsnyckel måste lagras i en partition, bör du undvika partitionsnycklar där stora mängder data för samma värde. 

Nu ska vi titta på några verkliga scenarier och bra partitionsnycklar för varje:
* Om du implementerar en serverdel för användaren-profil, är användar-ID ett bra alternativ för partitionsnyckel.
* Om du lagrar IoT data, till exempel enhetens tillstånd, är enhets-ID ett bra alternativ för partitionsnyckel.
* Om du använder Azure Cosmos DB för loggning av time series-data, är värdnamn eller process-ID ett bra alternativ för partitionsnyckel.
* Om du har en multitenant arkitektur, är klient-ID ett bra alternativ för partitionsnyckel.

I vissa fall som IoT- och profiler som använder, Partitionsnyckeln kan vara samma som ditt ID (Dokumentnyckel). I andra kan som de time series-data, du ha en partitionsnyckel som skiljer sig från ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionering och loggning-tidsserier data
En av de vanliga användningsområden för Azure Cosmos DB är för loggning och telemetri. Det är viktigt att välja en bra partitionsnyckel, eftersom du kan behöva läsa/skriva stora mängder data. Valet beror på din läsning och skrivning priser och typer av frågor som du förväntar dig att köra. Här följer några tips på hur du väljer en bra partitionsnyckel:

* Om din användningsfall innebär en liten andel skrivningar som samlas under lång tid och du behöver fråga efter intervall för tidsstämplar och filter, använder du en sammanslagning av tidsstämpeln. Till exempel är ett bra sätt att använda datum som en partitionsnyckel. Med den här metoden kan du fråga över alla data för ett datum från en enda partition. 
* Om din arbetsbelastning skrivs frekventa som är vanligare, använder du en partitionsnyckel som inte är baserad på tidsstämpel. Med den här metoden kan Azure Cosmos DB distribuera skrivningar jämnt mellan olika partitioner. Här är ett värdnamn, process-ID, aktivitets-ID eller en annan egenskap med hög kardinalitet ett bra alternativ. 
* En annan metod är en hybrid en om du har flera behållare, ett för varje dag/månad och Partitionsnyckeln är en detaljerad egenskap som värdnamn. Det här tillvägagångssättet har fördelen som du kan ange olika genomströmning baserat på tidsfönstret. Till exempel etableras behållaren för den aktuella månaden med högre genomströmning eftersom den fungerar läsningar och skrivningar. Tidigare månader etableras med lägre genomströmning eftersom de bara fungerar läsningar.

### <a name="partitioning-and-multitenancy"></a>Partitionering och multitenancy
Om du implementerar en multitenant program med hjälp av Azure Cosmos DB, det finns två populära mönster: en partitionsnyckel per klient och en behållare per klient. Här följer- och nackdelar för varje:

* **En partitionsnyckel per klient**. I den här modellen samordnat hyresgäster inom en enskild behållare. Men frågor och infogningar för objekt inom en enskild klient kan utföras mot en enskild partition. Du kan också implementera transaktionella logik i alla artiklar i en klient. Eftersom flera innehavare delar en behållare kan spara du kostnader för lagring och genomströmning genom poolning resurser för klienter i en enskild behållare i stället för att etablera extra utrymme för varje klient. Nackdelen är att du inte har isolering av prestandan per klient. Ökar prestanda/dataflöde gäller för hela behållaren jämfört med riktade ökar för klienter.
* **En behållare per klient**. Varje innehavare har sin egen behållare i den här modellen, och du kan reservera prestanda per klient. Den här modellen är mer kostnadseffektivt för flera program med några innehavare med Cosmos Azure DB nya etablering prissättning.

Du kan också använda en kombination/nivåer metod som collocates små klienter och migrerar större klienter till sina egna behållare.

## <a name="next-steps"></a>Nästa steg
I den här artikeln som vi en översikt över begrepp och bästa praxis för partitionering med Azure Cosmos DB API: er. 

* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distributionsplatsen i Azure Cosmos DB](distribute-data-globally.md).



