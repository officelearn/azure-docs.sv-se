---
title: Partitionering och horisontell skalning i Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionera nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cb668dba661ce05d6393aec2707b65918f0c2ac
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344138"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partitionera och skala i Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är en globalt distribuerad databas som hjälper dig få snabb, förutsägbar prestanda. Det kan skalas sömlöst tillsammans med ditt program. Den här artikeln innehåller en översikt över hur partitionering fungerar för alla data modeller i Azure Cosmos DB. Det beskriver också hur du konfigurerar Azure Cosmos DB-behållare för att effektivt skala dina program.

Partitionering och partitionsnycklar beskrivs i den här videon:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB
Azure Cosmos DB tillhandahåller behållare för lagring av anropade samlingar (för dokument), diagram och tabeller data. Behållare är logiska resurser och kan sträcka sig över en eller flera fysiska partitioner eller servrar. Antalet partitioner bestäms av Azure Cosmos DB baserat på lagringsutrymmet och dataflödet som etableras för en behållare eller en uppsättning behållare. 


### <a name="physical-partition"></a>Fysisk partition

En *fysiska* partitionen är en fast mängd reserverade SSD-uppbackad lagring tillsammans med variabeln mängden beräkningsresurser (processor och minne). Varje fysisk partition replikeras för hög tillgänglighet. Varje uppsättning behållare kan dela en eller flera fysiska partitioner. Fysisk partition management hanteras fullständigt av Azure Cosmos DB och du behöver inte skriva komplex kod eller hantera din partitioner. Azure Cosmos DB-behållare är obegränsade vad gäller lagring och dataflöde. Fysiska partitioner är en intern koncept i Azure Cosmos DB och är tillfälligt. Azure Cosmos DB skalar automatiskt antalet fysiska partitioner baserat på din arbetsbelastning. Så du inte bör corelate databasdesignen baserat på antalet fysiska partitioner bör i stället du kontrollera att välja rätt Partitionsnyckeln som avgör logiska partitioner. 

### <a name="logical-partition"></a>Logisk partition

En *logiska* partitionen är en partition inom en fysisk partition som lagrar alla data som är associerade med ett enskilt partitionsnyckelvärde. Flera logiska partitioner kan hamnar i samma fysiska partition. I följande diagram har tre logiska partitioner i en enskild behållare. Varje logisk partition innehåller data för en partitionsnyckel, LAX och AMS MEL respektive. Var och en av de LAX och AMS MEL logiska partitionerna kan inte behöver mer än den maximala logiska partitionsgränsen på 10 GB. 

![Resursen partitionering](./media/introduction/azure-cosmos-db-partitioning.png) 

När en behållare uppfyller de [partitionering krav](#prerequisites), partitionering är helt transparent för programmet. Azure Cosmos DB hanterar distribuera data över fysiska och logiska partitioner och routning av begäranden till rätt partition. 

## <a name="how-does-partitioning-work"></a>Hur fungerar partitionering

Varje dokument måste ha en *partitionsnyckel* och en *radnyckel*, som unikt identifierar den. Din partitionsnyckel fungerar som en logisk partition för dina data och ger en naturlig gräns Azure Cosmos DB för att distribuera data över fysiska partitioner. **Data för en enskild logisk partition måste finnas i en enda fysisk partition och fysisk partition management hanteras av Azure Cosmos DB**. 

Enkelt uttryckt fungerar så här partitionering i Azure Cosmos DB:

* Du etablerar en uppsättning Azure Cosmos DB-behållare med **T** RU/s (begäranden per sekund) dataflöde.  
* I bakgrunden, Azure Cosmos DB etablerar fysiska partitioner som krävs för att hantera **T** begäranden per sekund. Om **T** är högre än det maximala dataflöden per fysisk partition **t**, sedan Azure Cosmos DB tillhandahåller **N = T/t** fysiska partitioner. Värdet för maximalt dataflöde per partition(t) har konfigurerats av Azure Cosmos DB, det här värdet skapas baserat på totalt etablerat dataflöde och maskinvarukonfiguration som används.  
* Azure Cosmos DB allokerar viktiga utrymme på partitionen viktiga hashvärden jämnt över den **N** fysiska partitioner. Det antal logiska partitioner som är värdar för varje fysisk partition **1/N** * antal partitionsnyckelvärdena.  
* När en fysisk partition **p** når sin lagringsgräns, Azure Cosmos DB sömlöst delar upp **p** till två nya fysiska partitionerna **p1** och **p2**. Den distribuerar värden som motsvarar ungefär hälften av nycklar till var och en av de nya fysiska partitionerna. Det här Delningsåtgärden är helt osynlig för ditt program. Om en fysisk partition når sin lagringsgräns och alla data på den fysiska partitionen tillhör samma logiska partitionsnyckel, uppstår inte split-åtgärden. Det beror på att alla data för en enda logisk partitionsnyckel måste finnas i samma fysiska partition. I det här fallet bör en annan partition viktig strategi användas.  
* När du tilldelar en dataflöde som är högre än **t * N**, Azure Cosmos DB delar upp en eller flera av dina fysiska partitioner för högre dataflöde.

Semantik för partitionsnycklar är något annorlunda för att matcha semantiken för varje API som visas i följande tabell:

| API | Partitionsnyckeln | Radnyckel |
| --- | --- | --- |
| SQL | Anpassad partitionering Nyckelsökväg | Åtgärdade `id` | 
| MongoDB | Anpassade shardnyckel  | Åtgärdade `_id` | 
| Gremlin | Anpassad partitionering nyckelegenskapen | Åtgärdade `id` | 
| Tabell | Åtgärdade `PartitionKey` | Åtgärdade `RowKey` | 

Azure Cosmos DB använder hash-baserade partitionering. När du skriver ett objekt, Azure Cosmos DB hashar partitionsnyckelvärdet och använder det hashade resultatet för att avgöra vilken partition som ska lagra objektet i. 

> [!NOTE]
> Azure Cosmos DB lagrar alla objekt med samma partitionsnyckel i samma fysiska partition. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Bästa praxis när du väljer en partitionsnyckel

Valet av Partitionsnyckeln är ett viktigt beslut som du behöver göra vid designtillfället. Välj ett egenskapsnamn som har ett stort antal värden och har även mönster i databasåtkomst. Det är en bra idé att ha en partitionsnyckel med ett stort antal distinkta värden (t.ex. hundratals eller tusentals). Det kan du fördela arbetsbelastningen jämnt över dessa värden. En perfekt Partitionsnyckeln är en som visas ofta som ett filter i dina frågor och har tillräckligt med kardinalitet för att se till att din lösning är skalbar.

Om en fysisk partition når sin lagringsgräns och data i partitionen har samma partitionsnyckel, Azure Cosmos DB Returnerar den *”partitionsnyckel nått maximal storlek på 10 GB”* meddelandet och partitionen inte delas upp. En bra partitionsnyckel är ett viktigt beslut. 

Välja en partitionsnyckel så att:

* Storage-distribution är även över alla nycklar.  
* Volymen distributionen av begäranden som vid en viss tidpunkt är även över alla nycklar.  

  Det är en bra idé att kontrollera hur dina data fördelas över partitioner. Om du vill kontrollera Datadistribution i portalen, gå till ditt Azure Cosmos DB-konto och klicka på **mått** i **övervakning** och klicka sedan på **storage** fliken för att se hur din data partitioneras på olika fysiska partitioner.

  ![Resursen partitionering](./media/partition-data/partitionkey-example.png)

  Vänstra bilden ovan visar resultatet av en felaktig partitionsnyckel och rätt bilden ovan visar resultatet när en bra partitionsnyckel har valts. I den vänstra bilden ser du att data inte är jämnt fördelade mellan partitionerna. Du bör sträva efter att välja en partitionsnyckel som distribuerar dina data så att den liknar vilken avbildning.

* Frågor som anropas med hög samtidighet kan dirigeras effektivt genom att inkludera Partitionsnyckeln i filterpredikatet.  
* Välja en partitionsnyckel med högre kardinalitet föredras Allmänt – becaue det vanligtvis ger bättre distribution och skalbarhet. Till exempel kan en syntetisk nyckel skapas genom att sammanfoga värden från flera egenskaper för att öka kardinalitet.  

När du väljer en partitionsnyckel med ovan överväganden du inte behöver bekymra dig om antalet partitioner eller hur högt dataflöde tilldelas per fysisk partition som Azure Cosmos DB skalar ut antalet fysiska partitioner och det kan även skala den enskilda partitioner vid behov.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Krav för partitionering

Azure Cosmos DB-behållare kan skapas som fasta eller obegränsat i Azure-portalen. Containrar med fast storlek har en maxgräns på 10 GB och en genomströmning på 10 000 RU/s. Du måste ange en partitionsnyckel och en minsta genomströmning av 1 000 RU/s för att skapa en behållare som obegränsade. Azure Cosmos DB-behållare kan också konfigureras för att dela dataflödet mellan en uppsättning behållare, där varje behållare måste ange en partition nyckeln och kan växa obegränsad. Följande är förutsättningar för att tänka på för partitionering och skalning:

* När du skapar en behållare (t.ex. en samling, ett diagram eller en tabell) i Azure-portalen, väljer den **obegränsad** lagringsalternativ för kapacitet att utnyttja fördelarna med obegränsad skalning. För fysiska partitioner för att automatiskt dela upp i **p1** och **p2** enligt beskrivningen i [hur fungerar partitionering](#how-does-partitioning-work), behållaren måste skapas med ett dataflöde på 1 000 RU/s eller mer (eller dela genomflöde i en uppsättning behållare) och en partitionsnyckel som måste anges. 

* Om du har skapat en behållare i Azure portal eller programmässigt och inledande dataflöde var 1 000 RU/s eller mer, och du har angett en partitionsnyckel, kan du dra nytta av obegränsad skalning utan ändringar i din behållare. Detta inkluderar **fast** behållare, så länge som den första behållaren har skapats med minst 1 000 RU/s genomströmning och en partitionsnyckel anges.

* Alla behållare som är konfigurerad för att dela dataflöde som en del av en uppsättning behållare behandlas som **obegränsad** behållare.

Om du har skapat en **fast** behållare utan partitions nyckel eller dataflöde mindre än 1 000 RU/s, behållaren kommer inte automatisk skalning. Om du vill migrera data från en fast behållare till en obegränsad behållare, måste du använda den [datamigreringsverktyget](import-data.md) eller [biblioteket Change Feed](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Partitionering och etablerade dataflöde
Azure Cosmos DB är utformad för förutsebara prestanda. När du skapar en behållare eller en uppsättning behållare kan du reservera dataflöde i  *[programbegäran](request-units.md) (RU) per sekund*. Varje begäran som gör en RU-avgift som står i proportion till mängden systemresurser som processor, minne och I/O som förbrukats av åtgärden. En läsning av ett 1 KB-dokument med sessionskonsekvens förbrukar 1 RU. En läsning är 1 RU, oavsett hur många objekt som lagras eller antalet samtidiga begäranden som körs på samma gång. Större objekt kräver högre RUs beroende på storleken. Om du vet att storleken på dina enheter och antalet läsningar som du behöver stöd för ditt program kan etablera du den exakta mängden dataflödet som krävs för programmets behov. 

> [!NOTE]
> För att kunna utnyttja dataflödet som etableras för en behållare eller en uppsättning behållare, måste du välja en partitionsnyckel som gör att du kan distribuera begäranden jämnt över alla nyckelvärden för särskild partition.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="create-partition-key"></a>Skapa partitionsnyckel 
Du kan använda Azure portal eller Azure CLI för att skapa behållare och skala dem när som helst. Det här avsnittet visar hur du skapar behållare och ange den etablerade dataflöde och partition nyckeln med hjälp av varje API.


### <a name="sql-api"></a>API för SQL
I följande exempel visas hur du skapar en behållare (en samling) med SQL API. 

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

Du kan läsa ett objekt (dokument) med hjälp av den `GET` -metod i REST API eller använda `ReadDocumentAsync` i någon av de SDK: er.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Mer information finns i [partitionering i Azure Cosmos DB med SQL API](sql-api-partition-data.md).

### <a name="mongodb-api"></a>MongoDB-API
Med MongoDB-API kan du skapa en fragmenterad samling via dina favorit-verktyget, drivrutin eller SDK. I det här exemplet använder vi Mongo-gränssnittet för att skapa en samling.

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

Etablerat dataflöde har angetts som ett argument av `CreateIfNotExists`. Partitionsnyckeln skapas implicit som den `PartitionKey` värde. 

Du kan hämta en enda entitet med hjälp av följande kod:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Mer information finns i [utveckla med tabell-API](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Gremlin-API

Med Gremlin-API kan du använda Azure portal eller Azure CLI för att skapa en behållare som representerar ett diagram. Eftersom Azure Cosmos DB är flera modeller, kan du också använda en av de andra API: er att skapa och skala din graph-behållare.

Du kan läsa alla hörn eller edge med hjälp av partitionsnyckel och ID i Gremlin. För ett diagram med region (”USA”) som partitionsnyckel och ”Seattle” som radnyckel, kan du hitta ett hörn med hjälp av följande syntax:

```
g.V(['USA', 'Seattle'])
```

Du kan referera till en kant med Partitionsnyckeln och Radnyckeln.

```
g.E(['USA', 'I5'])
```

Mer information finns i [med hjälp av ett partitionerade diagram i Azure Cosmos DB](graph-partitioning.md).

## <a name="form-partition-key-by-concatenating-multiple-fields"></a>Partitionsnyckeln för formuläret genom att sammanfoga flera fält

Du kan också skapa en partitionsnyckel genom att sammanfoga och utfyllnad flera värden i en enda artificiella ”partitionKey”-egenskap i objektet. De här nycklarna kallas syntetiska nycklar.

Du har till exempel ett dokument som ser ut som:

```json
{
"deviceId": "abc-123",
"date": 2018
}
```

Ett alternativ är att ange partitionKey för /deviceId eller /date. Om vill bilda en partitionsnyckel på enhets-id och datum. Sammanfoga två gånger i en artificiella ”partitionKey”-egenskapen och ange Partitionsnyckeln till /partitionKey.

```json
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Du kan ha tusentals dokument så att du ska definiera klienten sida logik för att sammanfoga värden i en syntetisk nyckel, infoga syntetiska nyckeln i dokumenten och sedan använda den för att ange partitionsnyckel i realtid scenarier.

<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Design för skalbarhet
Om du vill skala effektivt med Azure Cosmos DB, måste du välja en bra partitionsnyckel när du skapar din behållare. Det finns två huvudsakliga överväganden för att välja en bra partitionsnyckel:

* **Fråga gräns och transaktioner**. Ditt val av partitionsnyckel bör balansera behovet av att använda transaktioner mot kravet för att distribuera dina entiteter över flera partitionsnycklar för att se till att en skalbar lösning. Du kan ange samma partitionsnyckel för alla objekt på en extreme, men det här alternativet kan begränsa skalbarheten i din lösning. Du kan tilldela en unik partitionsnyckel för varje objekt på andra yttersta. Det här alternativet är mycket skalbar, men det förhindrar att du använder transaktioner mellan dokument via lagrade procedurer och utlösare. En perfekt partitionsnyckel kan du använda effektiva frågor och har tillräckligt med kardinalitet för att se till att din lösning är skalbar. 
* **Inga lagrings- och flaskhalsar**. Det är viktigt att välja en egenskap som gör att skrivningar sprids över olika distinkta värden. Begäranden till samma partitionsnyckel får inte överskrida det etablerade dataflödet som allokerats till en partition och blir rate-limited. Det är därför viktigt att välja en partitionsnyckel som inte resulterar i ”hotspots” i ditt program. Eftersom alla data för en enda partitionsnyckel måste lagras inom en partition, bör du undvika partitionsnycklar som har stora mängder data för samma värde. 

Nu ska vi titta på några verkliga scenarier och bra partitionsnycklar för var och en:
* Om du implementerar en profil serverdel användaren den *användar-ID* är ett bra alternativ för en partitionsnyckel.
* Om du lagrar IoT-data, till exempel enhetens tillstånd, en *enhets-ID* är ett bra alternativ för en partitionsnyckel.
* Om du använder Azure Cosmos DB för loggning av time series-data i *värdnamn* eller *process-ID* är ett bra alternativ för en partitionsnyckel.
* Om du har en arkitektur med flera innehavare, den *klient-ID* är ett bra alternativ för en partitionsnyckel.

I vissa fall, t.ex. IoT- och -profiler som använder, Partitionsnyckeln kan vara samma som din *ID* (dokumentnyckeln). I andra, som time series-data, du kan ha en partitionsnyckel som skiljer sig från den *ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionering och loggning/time series-data
En av de vanliga användningsområden i Azure Cosmos DB är loggning och telemetri. Det är viktigt att välja en bra partitionsnyckel i det här scenariot eftersom du kan behöva att läsa/skriva stora mängder data. Val för en partitionsnyckel beror på dina priser för Läs-och-och och typer av frågor som du förväntar dig att köra. Här följer några tips om hur du väljer en bra partitionsnyckel:

* Om ditt användningsområde innebär en liten andel skrivningar som ackumuleras över en längre tid och du behöver att fråga efter intervall för tidsstämplar med andra filter, kan du använda en sammanslagning av tidsstämpeln. Till exempel är en bra metod att använda datum som en partitionsnyckel. Med den här metoden kan du fråga över alla data för ett givet datum från en enda partition. 
* Om din arbetsbelastning är skrivintensiv, vilket är vanligt i det här scenariot, kan du använda en partitionsnyckel som inte baseras på tidsstämpel. Därmed kan Azure Cosmos DB kan distribuera och skala skrivningar jämnt över olika partitioner. Här en *värdnamn*, *process-ID*, *aktivitets-ID*, eller en annan egenskap med hög kardinalitet är ett bra val. 
* En annan metod är en hybridlösning, där du har flera behållare, en för varje dag/månad, och Partitionsnyckeln är en mer detaljerad egenskap som *värdnamn*. Den här metoden har förmånen som du kan ange olika dataflöde för varje behållare eller en uppsättning behållare baserat på tidsfönstret och behov skalning och prestanda. En behållare för den aktuella månaden kan exempelvis ha etablerats med ett högre dataflöde, eftersom den fungerar läsningar och skrivningar. Föregående månader kan etableras med ett lägre dataflöde, eftersom de endast fungerar läsningar.

### <a name="partitioning-and-multitenancy"></a>Partitionering och flera innehavare
Om du implementerar en multitenant-App med Azure Cosmos DB, det finns två populära Designer att tänka på: *en partitionsnyckel per klient* och *behållare per klient*. Här är för- och nackdelar för var och en:

* **En partitionsnyckel per klient**. I den här modellen är klienter samordnade inom en enskild behållare. Frågor och infogningar för en enda klient kan utföras mot en enda partition. Du kan också implementera transaktionella logic över alla objekt som tillhör en klient. Eftersom flera klienter delar en behållare kan använda du bättre lagring och etablerat dataflöde genom poolning resurser för alla klienter inom en enskild behållare i stället för att etablera för varje klient. Nackdelen är att du inte behöver isolering av prestandan per klient. Öka dataflödet att garantera prestanda gäller för hela behållaren med innehavarna som jämfört med riktade ökar för en enskild klientorganisation.
* **Behållare per klient**. Varje klientorganisation har en egen behållare i den här modellen, och du kan reservera dataflöde med garanterade prestanda per klient. Den här modellen är mer kostnadseffektivt för program med flera klienter med ett fåtal klienter.

Du kan också använda en hybridlösning som colocates små klienter tillsammans och isolerar större klienter till sina egna behållare.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har lagt vi till en översikt över koncepten och bästa praxis för skalning och partitionering i Azure Cosmos DB. 

* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).



