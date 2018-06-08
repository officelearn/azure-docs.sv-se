---
title: Partitionering och teckenbredden i Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitions-nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd1b52dd32976ce65458e1dfe1b50d228fbd6d0e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850533"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partitionera och skala i Azure Cosmos DB

[Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) är en global och flera olika modeller databastjänst som hjälper dig uppnå snabb och förutsägbar prestanda. Skalningen sömlöst tillsammans med ditt program när den växer. Den här artikeln innehåller en översikt över hur partitionering fungerar för alla data modeller i Azure Cosmos-databasen. Här beskrivs också hur du kan konfigurera Azure Cosmos DB behållare för att effektivt skala ditt program.

Partitionering och partitionsnycklar beskrivs i den här videon:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB
Du kan lagra och fråga schemat mindre data med en siffra millisekunders latens skaländras i Azure Cosmos-databasen. Azure Cosmos-DB innehåller behållare för lagring av data kallas *samlingar* (för dokument) *diagram*, eller *tabeller*. 

Behållare är logiska nätverksresurser och kan sträcka sig över en eller flera partitioner fysiska servrar. Antalet partitioner bestäms av Azure Cosmos DB baserat på lagringsstorleken och dataflödet för en behållare eller en uppsättning av behållare. 

En *fysiska* partitionen är en fast mängd reserverade SSD-baserad lagring som kombineras med variabel mängd beräkningsresurser (processor och minne). Varje fysiska partition replikeras för hög tillgänglighet. Varje uppsättning behållare kan dela en eller flera fysiska partitioner. Hantering av fysiska partition hanteras helt av Azure Cosmos DB och du behöver inte skriva komplex kod eller hantera din partitioner. Azure DB Cosmos-behållare är obegränsad lagring och genomflöde. 

En *logiska* är en partition inom en fysiska partition som lagrar alla data som är associerade med värdet för en enskild partition. Flera logiska partitioner kan hamna i samma fysiska partition. I följande diagram visas har en enskild behållare tre logiska partitioner. Varje logisk partition lagrar data för en partitionsnyckel, LAX och AMS MEL respektive. Alla logiska partitioner LAX och AMS MEL växer inte gräns maximala logisk partition på 10 GB. 

![Resursen partitionering](./media/introduction/azure-cosmos-db-partitioning.png) 

När en behållare uppfyller de [partitionering krav](#prerequisites), partitionering är helt transparent för programmet. Azure Cosmos-DB stöder snabb läsning och skrivning, frågor, transaktionella logik, konsekvensnivåer och detaljerad åtkomstkontroll via metoder/API: er till en enskild behållare resurs. Tjänsten hanterar distribuerar data över fysiska och logiska partitioner och routning av begäran till rätt partition. 

## <a name="how-does-partitioning-work"></a>Hur fungerar partitionering

Hur fungerar partitionering? Varje objekt måste ha en *partitionsnyckel* och en *radnyckel*, som unikt identifierar. Din partitionsnyckel fungerar som en logisk partition för dina data och ger Azure Cosmos DB en naturlig gräns för att distribuera data över fysiska partitioner. Data för en enskild logisk partition måste finnas inuti en enda fysisk partition, men fysiska partition management hanteras av Azure Cosmos DB. 

I korthet är här hur partitionering fungerar i Azure Cosmos DB:

* Du etablerar en uppsättning Azure Cosmos DB behållare med **T** RU/s (begäranden per sekund) genomflöde.
* I bakgrunden, etablerar Azure Cosmos DB fysiska partitioner som behövs för att hantera **T** begäranden per sekund. Om **T** är högre än det maximalt dataflödet per fysiska partition **t**, sedan Azure Cosmos DB tillhandahåller **N = T/t** fysiska partitioner. Värdet för maximalt dataflöde per partition(t) konfigureras med Azure Cosmos DB, detta värde tilldelas baserat på totala etablerat dataflöde och maskinvarukonfiguration som används. 
* Azure Cosmos-DB allokerar viktiga utrymme på partitionen viktiga hashvärden jämnt i bredd i **N** fysiska partitioner. Så här: varje fysiska partition värdar **1/N** partitions nyckelvärdena (logiska partitioner).
* När en fysisk partition **p** når sin lagringsgräns, Azure Cosmos DB sömlöst delar upp **p** till två nya fysiska partitioner **p1** och **p2**. Den distribuerar värden som motsvarar ungefär hälften av nycklar till var och en av de nya fysiska partitionerna. Den här delade åtgärden är helt osynlig för ditt program. Om en fysiska partition når sin lagringsgräns och alla data på den fysiska partitionen tillhör samma logiska partitionsnyckel, uppstår inte split-åtgärden. Det beror på att alla data för en enskild logisk partitionsnyckel måste finnas i samma fysiska partition. I det här fallet bör en annan partition viktiga strategi användas.
* När du etablerar genomströmning som är högre än **t * N**, Azure Cosmos DB delar upp en eller flera fysiska partitioner att stödja högre genomströmning.

Semantik för partitionsnycklar är något annorlunda att matcha semantiken för varje API, som visas i följande tabell:

| API | Partitionsnyckeln | Radnyckel |
| --- | --- | --- |
| SQL | Anpassad partitionering nyckelsökvägen | Åtgärdade `id` | 
| MongoDB | anpassade Fragmentera nyckel  | Åtgärdade `_id` | 
| Gremlin | Anpassad partitionering nyckelegenskapen | Åtgärdade `id` | 
| Tabell | Åtgärdade `PartitionKey` | Åtgärdade `RowKey` | 

Azure Cosmos-DB använder hash-baserad partitionering. När du skriver ett objekt Azure Cosmos DB hashar nyckelvärdet partition och använder hashformaterats resultatet för att avgöra vilken partition för att lagra objekt i. Azure Cosmos-DB lagrar alla objekt med samma partitionsnyckel i samma fysiska partition. 

Valet av Partitionsnyckeln är ett viktigt beslut som du behöver göra i designläge. Välj ett egenskapsnamn som har ett stort antal värden och har även åtkomstmönster. Det är bäst att ha en partitionsnyckel med ett stort antal distinkta värden (t.ex. hundratals eller tusentals). Det kan du distribuera din arbetsbelastning jämnt mellan dessa värden. En perfekt Partitionsnyckeln är en som visas ofta som ett filter i dina frågor och kardinalitet är tillräcklig för att säkerställa att din lösning är skalbart.

Om en fysiska partition når sin lagringsgräns och data i partitionen har samma partitionsnyckel, Azure Cosmos-databas returnerar den *”partitionsnyckel nått maximal storlek på 10 GB”* meddelandet och partitionen inte delar upp. Att välja en bra partitionsnyckel är ett viktigt beslut. Fysiska partitioner är ett internt begreppet Azure Cosmos DB och är tillfälligt. Azure Cosmos-DB skala automatiskt antalet fysiska partitioner baserat på din arbetsbelastning. Så du inte bör corelate databasens utformning baserat på antalet fysiska partitioner bör i stället du kontrollera att välja rätt Partitionsnyckeln (logiska partitioner). 

Välj en partitionsnyckel så att:

* Storage-distribution är även över alla nycklar.
* Volymen fördelning begäranden vid en viss tidpunkt är även över alla nycklar.
* Frågor som anropas med hög samtidighet kan effektivt vidarebefordras genom att inkludera Partitionsnyckeln i filterpredikatet.  
* Om du väljer en partitionsnyckel med högre kardinalitet rekommenderas vanligtvis – becaue det vanligtvis ger bättre distribution och skalbarhet. En sammansatt nyckel kan till exempel bildas genom att sammanbinda värden från flera egenskaper för att öka kardinalitet. 

När du väljer en partitionsnyckel med ovan överväganden kommer du inte behöver bry dig om antalet partitioner eller hur mycket genomströmning fördelas per fysiska partition som Azure Cosmos DB skalas ut antalet fysiska partitioner och kan även skala den enskilda partitioner efter behov.

Azure DB Cosmos-behållare kan skapas som *fast* eller *obegränsade* i Azure-portalen. Behållare med fast storlek har en maxgräns på 10 GB och en genomströmning på 10 000 RU/s. Du måste ange en partitionsnyckel och en minsta genomströmning på 1 000 RU/s för att skapa en behållare som obegränsade. Azure DB Cosmos-behållare kan också konfigureras för att dela dataflödet mellan en uppsättning behållare, där varje behållare måste ange en partition nyckeln och kan växa obegränsade.

Det är en bra idé att kontrollera hur dina data fördelade över partitioner. Gå till Azure DB som Cosmos-konto för att kontrollera Datadistributionen i portalen och klicka på **mått** i **övervakning** avsnittet och klicka sedan på **lagring** fliken för att se hur din data är partitionerad över olika fysiska partitioner.

![Resursen partitionering](./media/partition-data/partitionkey-example.png)

Vänster bilden ovan visar resultatet av en felaktig partitionsnyckel och rätt bilden ovan visar resultatet när en bra partitionsnyckel har valts. I vänster bild ser du att data inte är jämnt fördelad över partitionerna. Du bör sträva efter att välja en partitionsnyckel som distribuerar dina data så att den ser ut ungefär i rätt avbildning.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Krav för partitionering

För fysiska partitioner för att automatiskt dela upp i **p1** och **p2** enligt beskrivningen i [hur fungerar partitionering arbete](#how-does-partitioning-work), behållaren måste skapas med en genomströmning på 1 000 RU/s eller mer (eller resursen genomflöde i en behållare) och en partitionsnyckel som måste anges. När du skapar en behållare (t.ex. en samling, ett diagram eller en tabell) i Azure portal väljer den **obegränsad** kapacitet lagringsalternativ dra nytta av obegränsad skalning. 

Om du har skapat en behållare i Azure-portalen eller programmässigt och inledande kapaciteten var 1 000 RU/s eller mer, och du har angett en partitionsnyckel, kan du dra nytta av obegränsad skalning utan ändringar av din behållare. Detta inkluderar **fast** behållare, så länge den första behållaren har skapats med minst 1 000 RU/s genomströmning och en partitionsnyckel har angetts.

Alla behållare som konfigurerats för att dela dataflödet som en del av en uppsättning behållare behandlas som **obegränsad** behållare.

Om du har skapat en **fast** behållare inga partitionsnyckel eller genomströmning är mindre än 1 000 RU/s behållaren kommer inte Autoskala enligt beskrivningen i den här artikeln. Om du vill migrera data från en fast behållare till ett obegränsat antal behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel), måste du använda den [datamigreringsverktyget](import-data.md) eller [ändra Feed biblioteket](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Partitionering och etablerat dataflöde
Azure Cosmos-DB är utformad för förutsägbar prestanda. När du skapar en behållare eller uppsättning behållare du reservera genomflöde i  *[begära enheter](request-units.md) (RU) per sekund*. Varje begäran gör en RU kostnad som är i proportion till storleken på systemresurser som CPU, minne och i/o som används av åtgärden. En läsning av ett 1-KB-dokument med sessionskonsekvens förbrukar 1 RU. Läs är 1 RU oavsett hur många objekt som lagras eller antalet samtidiga begäranden som körs på samma gång. Större objekt kräver högre RUs beroende på storleken. Om du vet storleken på dina enheter och antalet läsningar som du behöver stöd för ditt program kan etablera du den exakta mängden dataflödet som krävs för programmets behov. 

> [!NOTE]
> För att kunna utnyttja dataflödet för en behållare eller en uppsättning av behållare, måste du välja en partitionsnyckel som hjälper dig att distribuera begäranden jämnt över alla nyckelvärden för olika partition.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Arbeta med Azure Cosmos DB-API: er
Du kan använda Azure-portalen eller Azure CLI för att skapa behållare och skala dem när som helst. Det här avsnittet visar hur du skapar behållare och ange etablerade genomflöde och partition nyckeln med hjälp av varje API.


### <a name="sql-api"></a>API för SQL
I följande exempel visas hur du skapar en behållare (en samling) med SQL-API. 

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

Du kan läsa ett objekt (dokument) med hjälp av den `GET` metod i REST API eller med hjälp av `ReadDocumentAsync` i något av de SDK: er.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Mer information finns i [partitionering i Azure Cosmos-databasen med SQL-API](sql-api-partition-data.md).

### <a name="mongodb-api"></a>MongoDB-API
Du kan skapa ett delat samling via din favorit-verktyget, drivrutiner och SDK med MongoDB-API. I det här exemplet använda vi Mongo-gränssnittet för att skapa en samling.

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

Du kan skapa en tabell med tabell-API med den `CreateIfNotExists` metoden. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Dataflöde har angetts som ett argument av `CreateIfNotExists`. Partitionsnyckeln skapas implicit som den `PartitionKey` värde. 

Du kan hämta en enda entitet med hjälp av följande kod:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Mer information finns i [utveckla med tabell-API: et](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Gremlin-API

Med Gremlin-API kan du använda Azure-portalen eller Azure CLI för att skapa en behållare som representerar ett diagram. Du kan också eftersom Azure Cosmos DB är flera modellen, kan du använda en av de andra API: er att skapa och skala graph-behållaren.

Du kan läsa alla hörn eller en kant med partitionsnyckel och ID: T i Gremlin. För ett diagram med region (”USA”) som partitionsnyckel och ”Seattle” som Radnyckeln kan du hitta en nod med hjälp av följande syntax:

```
g.V(['USA', 'Seattle'])
```

Du kan referera en kant med hjälp av Partitionsnyckeln och Radnyckeln.

```
g.E(['USA', 'I5'])
```

Mer information finns i [använder ett partitionerat diagram i Azure Cosmos DB](graph-partitioning.md).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Design för skalan
Om du vill skala effektivt med Azure Cosmos DB, måste du välja en bra partitionsnyckel när du skapar en behållare. Det finns två huvudsakliga överväganden för att välja en bra partitionsnyckel:

* **Fråga gräns och transaktioner**. Ditt val av partitionsnyckel bör balansera behovet att använda transaktioner mot kravet för att distribuera din entiteter över flera partitionsnycklar för att säkerställa en skalbar lösning. Du kan ange samma partitionsnyckel för alla objekt i en extreme, men det här alternativet kan begränsa skalbarhet i lösningen. Du kan tilldela en unik partitionsnyckel för varje objekt på andra extreme. Det här alternativet är mycket skalbar, men det hindrar dig från att använda transaktioner mellan dokument via lagrade procedurer och utlösare. En perfekt partitionsnyckel kan du använda effektiva frågor och kardinalitet är tillräcklig för att säkerställa att din lösning är skalbart. 
* **Inga lagrings- och prestandakrav flaskhalsar**. Det är viktigt att välja en egenskap som gör att skriver över olika distinkta värden. Begäranden till samma partitionsnyckel får inte överskrida det tillhandahållna dataflöde som allokerats till en partition och kommer att vara begränsade-hastighet. Det är därför viktigt att välja en partitionsnyckel inte resulterar i ”aktiva punkter” i ditt program. Eftersom alla data för en enda partitionsnyckel måste lagras i en partition, bör du undvika partitionsnycklar där stora mängder data för samma värde. 

Nu ska vi titta på några verkliga scenarier och bra partitionsnycklar för varje:
* Om du implementerar en profil serverdel användaren den *användar-ID* är ett bra alternativ för en partitionsnyckel.
* Om du lagrar IoT data, till exempel enhetens tillstånd, en *enhets-ID* är ett bra alternativ för en partitionsnyckel.
* Om du använder Azure Cosmos DB för loggning av time series-data, de *värdnamn* eller *process-ID* är ett bra alternativ för en partitionsnyckel.
* Om du har en multitenant arkitektur i *klient-ID* är ett bra alternativ för en partitionsnyckel.

I vissa fall som IoT- och profiler som använder, Partitionsnyckeln kan vara samma som din *ID* (Dokumentnyckel). I andra som de time series-data, du kan ha en partitionsnyckel som skiljer sig från den *ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionering och loggning-tidsserier data
Vanliga användningsområden i Azure Cosmos DB är loggning och telemetri. Det är viktigt att välja en bra partitionsnyckel i detta scenario eftersom du kan behöva läsa/skriva stora mängder data. Alternativ för en partitionsnyckel beror på din läsning och skrivning priser och typer av frågor som du förväntar dig att köra. Här följer några tips på hur du väljer en bra partitionsnyckel:

* Om din användningsfall innebär en liten andel skrivningar som samlas under lång tid och du behöver fråga efter intervall för tidsstämplar med andra filter, använder du en sammanslagning av tidsstämpel. Till exempel är ett bra sätt att använda datum som en partitionsnyckel. Med den här metoden kan du fråga över alla data för ett datum från en enda partition. 
* Om din arbetsbelastning är skrivintensiv, vilket är vanligt i det här scenariot, använder du en partitionsnyckel som inte är baserad på tidsstämpel. Därför Azure Cosmos DB kan distribuera och skala skrivningar jämnt mellan olika partitioner. Här en *värdnamn*, *process-ID*, *aktivitets-ID*, eller en annan egenskap med hög kardinalitet är ett bra alternativ. 
* En annan metod är en hybrid-metod, om du har flera behållare, ett för varje dag/månad och Partitionsnyckeln är en mer detaljerad egenskap som *värdnamn*. Det här tillvägagångssättet har fördelen som du kan ange olika genomflödet för varje behållare eller en uppsättning behållare baserat på tidsfönstret och behov skalning och prestanda. En behållare för den aktuella månaden kan till exempel etableras med en högre genomströmning eftersom den fungerar läsningar och skrivningar. Tidigare månader kan etableras med en lägre genomströmning eftersom de bara fungerar läsningar.

### <a name="partitioning-and-multitenancy"></a>Partitionering och multitenancy
Om du implementerar en multitenant program med hjälp av Azure Cosmos DB, det finns två populära Designer att tänka på: *en partitionsnyckel per klient* och *-behållare per klient*. Här följer- och nackdelar för varje:

* **En partitionsnyckel per klient**. Klienter är samordnade inom en enskild behållare i den här modellen. Frågor och infogningar för en enskild klient kan utföras mot en enskild partition. Du kan även implementera logik för transaktionell över alla objekt som hör till en klient. Eftersom flera innehavare delar en behållare kan använda du bättre lagring och etablerat dataflöde genom poolning resurser för alla klienter i en enskild behållare i stället för att etablera för varje klient. Nackdelen är att du inte har isolering av prestandan per klient. Öka genomflödet att garantera prestanda gäller för hela behållaren med innehavarna som jämfört med riktade ökar för en enskild klientorganisation.
* **En behållare per klient**. Varje innehavare har sin egen behållare i den här modellen, och du kan reservera dataflöde med garanterad prestanda per klient. Den här modellen är mer kostnadseffektivt för flera program med några klienter.

Du kan också använda en hybrid-metod som colocates små klienter tillsammans och isolerar större klienter till sina egna behållare.

## <a name="next-steps"></a>Nästa steg
I den här artikeln som vi en översikt över begrepp och bästa praxis för att skala och partitionering i Azure Cosmos DB. 

* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distributionsplatsen i Azure Cosmos DB](distribute-data-globally.md).



