---
title: Partitionering och teckenbredden i Azure Cosmos DB | Microsoft Docs
description: "Lär dig mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitions-nycklar och hur du väljer rätt Partitionsnyckeln för ditt program."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e19ea08823575a535b7bc3e18a97902f72e802eb
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partitionera och skala i Azure Cosmos DB

[Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) är en global, multimodel databas-tjänst som hjälper dig uppnå snabb och förutsägbar prestanda. Skalningen sömlöst tillsammans med ditt program när den växer. Den här artikeln innehåller en översikt över hur partitionering fungerar för alla data modeller i Azure Cosmos-databasen. Här beskrivs också hur du kan konfigurera Azure Cosmos DB behållare för att effektivt skala ditt program.

Partitionering och partitionsnycklar beskrivs i den här Azure fredag video med Scott Hanselman och Azure Cosmos DB Principal tekniker Manager Shireesh Thota:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB
Du kan lagra och fråga schemat mindre data med ordning millisekunder svarstider skaländras i Azure Cosmos-databasen. Azure Cosmos-DB innehåller behållare för lagring av data kallas *samlingar* (för dokument) *diagram*, eller *tabeller*. Behållare är logiska nätverksresurser och kan sträcka sig över en eller flera partitioner fysiska servrar. Antalet partitioner bestäms av Azure Cosmos DB baserat på lagringsstorleken och etablerat dataflöde på behållaren. Varje partition i Azure Cosmos-databasen har en fast mängd SSD-baserad lagring som är kopplad till den och replikeras för hög tillgänglighet. Partitionen management hanteras helt av Azure Cosmos DB och du behöver inte skriva komplex kod eller hantera din partitioner. Azure DB Cosmos-behållare är obegränsad lagring och genomflöde. 

![Resursen partitionering](./media/introduction/azure-cosmos-db-partitioning.png) 

Partitionering är transparent för programmet. Azure Cosmos-DB stöder snabb läsning och skrivning, frågor, transaktionella logik, konsekvensnivåer och detaljerad åtkomstkontroll via metoder/API: er till en enskild behållare resurs. Tjänsten hanterar distribuera data över partitioner och routning frågebegäranden till rätt partition. 

Hur fungerar partitionering? Varje objekt måste ha en partitionsnyckel och en rad, som kan identifieras. Din partitionsnyckel fungerar som en logisk partition för dina data och ger Azure Cosmos DB en naturlig gräns för att distribuera data över partitioner. I korthet är här hur partitionering fungerar i Azure Cosmos DB:

* Du etablerar en Azure DB som Cosmos-behållare med `T` begäranden/s genomströmning.
* I bakgrunden, etablerar Azure Cosmos DB partitioner som behövs för att hantera `T` begäranden/s. Om `T` är högre än det största genomflödet per partition `t`, sedan Azure Cosmos DB tillhandahåller `N`  =  `T/t` partitioner.
* Azure Cosmos-DB allokerar viktiga utrymme på partitionen viktiga hashvärden jämnt i bredd i `N` partitioner. Varje partition (fysiska partition) värdar så `1/N` partitions nyckelvärdena (logiska partitioner).
* När en fysisk partition `p` når sin lagringsgräns, Azure Cosmos DB sömlöst delar `p` till två nya partitioner `p1` och `p2`. Den distribuerar värden som motsvarar ungefär hälften nycklar till var och en av partitionerna. Den här delade åtgärden är osynliga för ditt program.
* På liknande sätt, när du etablerar genomströmning som är högre än `t*N`, Azure Cosmos DB delar upp en eller flera partitioner att stödja högre genomströmning.

Semantik för partitionsnycklar är något annorlunda att matcha semantiken för varje API, som visas i följande tabell:

| API | Partitionsnyckeln | Radnyckel |
| --- | --- | --- |
| Azure Cosmos DB | Anpassad partitionering nyckelsökvägen | Åtgärdade `id` | 
| MongoDB | Anpassade delad nyckel  | Åtgärdade `_id` | 
| Graph | Anpassad partitionering nyckelegenskapen | Åtgärdade `id` | 
| Tabell | Åtgärdade `PartitionKey` | Åtgärdade `RowKey` | 

Azure Cosmos-DB använder hash-baserad partitionering. När du skriver ett objekt Azure Cosmos DB hashar nyckelvärdet partition och använder hashformaterats resultatet för att avgöra vilken partition för att lagra objekt i. Azure Cosmos-DB lagrar alla objekt med samma partitionsnyckel i samma fysiska partition. Valet av Partitionsnyckeln är ett viktigt beslut som du behöver göra i designläge. Du måste välja ett egenskapsnamn som har ett stort antal värden och har även åtkomstmönster.

> [!NOTE]
> Det är bäst att ha en partitionsnyckel med många distinkta värden (500 till 1 000 minst).
>

Azure DB Cosmos-behållare kan skapas som *fast* eller *obegränsade*. Fast storlek behållare har en maxgräns på 10 GB och 10 000 RU/s genomströmning. Vissa API: er kan partitionsnyckel utelämnas för behållare med fast storlek. Du måste ange en lägsta dataflöde på 2 500 RU/s för att skapa en behållare som obegränsade.

Det är en bra idé att kontrollera hur dina data är distribuerad i partitioner. Du kan kontrollera detta i portalen, gå till Azure DB som Cosmos-konto och klicka på **mått** i **övervakning** avsnittet och klicka sedan i den högra rutan på **lagring** fliken för att se hur dina data är partitionerade i olika fysiska partition.

![Resursen partitionering](./media/partition-data/partitionkey-example.png)

Vänster bild visar resultatet av en felaktig partitionsnyckel och den högra bilden visar resultatet av en bra partitionsnyckel. Du kan se data inte är fördelas jämnt mellan partitioner i vänster bild. Du bör sträva efter att distribuera dina data så att diagrammet liknar rätt avbildning.

## <a name="partitioning-and-provisioned-throughput"></a>Partitionering och etablerat dataflöde
Azure Cosmos-DB är utformad för förutsägbar prestanda. När du skapar en behållare kan du reservera genomflöde i  *[programbegäran](request-units.md) (RU) per sekund*. Varje begäran har tilldelats ett RU-tillägg som är proportionell mot mängden systemresurser som CPU, minne och i/o som används av åtgärden. En läsning av ett 1-KB-dokument med sessionskonsekvens förbrukar 1 RU. Läs är 1 RU oavsett hur många objekt som lagras eller antalet samtidiga begäranden som körs på samma gång. Större objekt kräver högre RUs beroende på storleken. Om du vet storleken på dina enheter och antalet läsningar som du behöver stöd för ditt program kan etablera du den exakta mängden dataflödet som krävs för ditt program är behöver läsa. 

> [!NOTE]
> Du måste välja en partitionsnyckel som gör det möjligt att fördela begäranden mellan vissa distinkta partitionsnyckelvärden för att uppnå det totala genomflödet i behållaren.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Arbeta med Azure Cosmos DB-API: er
Du kan använda Azure-portalen eller Azure CLI för att skapa behållare och skala dem när som helst. Det här avsnittet visar hur du skapar behållare och ange definitionen av genomflöde och partition i var och en API som stöds.

### <a name="azure-cosmos-db-api"></a>Azure Cosmos DB-API
I följande exempel visas hur du skapar en behållare (insamling) med hjälp av Azure Cosmos DB API. 

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

Med tabell-API anger du genomströmning för tabeller i appSettings-konfigurationen för programmet.

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

Sedan kan du skapa en tabell med hjälp av Azure Table storage SDK. Partitionsnyckeln skapas implicit som den `PartitionKey` värde. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

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

Mer information finns i [Gremlin stöd för Azure Cosmos DB](gremlin-support.md).


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



