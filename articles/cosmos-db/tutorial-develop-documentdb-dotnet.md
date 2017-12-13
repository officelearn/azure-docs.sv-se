---
title: 'Azure Cosmos DB: Utveckla med SQL-API: et i .NET | Microsoft Docs'
description: "Lär dig att utveckla med Azure Cosmos DB SQL-API med hjälp av .NET"
services: cosmos-db
documentationcenter: 
author: rafats
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 9209d815cadcb3abfacdc765c503851ba63863bc
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cosmosdb-develop-with-the-sql-api-in-net"></a>Azure CosmosDB: Utveckla med SQL-API: et i .NET

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här kursen visar hur du skapar ett Azure DB som Cosmos-konto med Azure-portalen och sedan skapa ett dokument databas och samling med en [partitionsnyckel](documentdb-partition-data.md#partition-keys) med hjälp av den [SQL .NET API](documentdb-introduction.md). Genom att definiera en partitionsnyckel när du skapar en samling kan är ditt program beredd att skala utan problem när dina data växer. 

Den här kursen ingår följande uppgifter med hjälp av den [SQL .NET API](documentdb-sdk-dotnet.md):

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa en databas och samling med en partitionsnyckel
> * Skapa JSON-dokument
> * Uppdatera ett dokument
> * Fråga partitionerade samlingar
> * Köra lagrade procedurer
> * Ta bort ett dokument
> * Ta bort en databas

## <a name="prerequisites"></a>Krav
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Börja med att skapa ett Azure DB som Cosmos-konto i Azure-portalen.

> [!TIP]
> * Redan har ett Azure DB som Cosmos-konto? I så fall, gå vidare till [konfigurera Visual Studio-lösning](#SetupVS)
> * Om du använder Azure Cosmos DB-emulatorn, följer du stegen i [Azure Cosmos DB emulatorn](local-emulator.md) konfigurera emulatorn och gå vidare till [ställa in din Visual Studio-lösning](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Konfigurera din Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I den **nytt projekt** markerar **mallar** / **Visual C#** / **Konsolapp (.NET Framework)**namnge projektet och klicka sedan på **OK**.
   ![Skärmdump som visar fönstret Nytt projekt](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**
    
    ![Skärmdump som visar den högerklickade menyn för projektet](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. I den **NuGet** klickar du på **Bläddra**, och skriv **documentdb** i sökrutan.
<!---stopped here--->
6. Leta reda på **Microsoft.Azure.DocumentDB** i resultatet och klicka på **Installera**.
   Paket-ID för klientbiblioteket för Azure Cosmos DB är [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Skärmbild som visar NuGet-menyn för att hitta Azure Cosmos DB klient-SDK](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.

## <a id="Connect"></a>Lägg till referenser till ditt projekt
Stegen i den här självstudiekursen innehåller SQL API kodstycken som krävs för att skapa och uppdatera Azure Cosmos DB resurser i projektet.

Lägg först till dessa referenser till programmet.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Anslut appen

Lägg till nedanstående två konstanter och dina *klienten* variabeln i ditt program.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Sedan head tillbaka till den [Azure-portalen](https://portal.azure.com) att hämta dina slutpunkts-URL och primärnyckel. Slutpunkts-URL:en och den primära nyckeln behövs för att programmet ska veta vart ditt program ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning.

Navigera till ditt Azure DB som Cosmos-konto i Azure-portalen klickar du på **nycklar**, och klicka sedan på **skrivskyddad nycklar**.

Kopiera URI: N från portalen och via `<your endpoint URL>` i filen program.cs. Kopiera den PRIMÄRNYCKELN från portalen och klistra in den över `<your primary key>`. Ta bort den `<` och `>` från värden.

![Skärmbild av Azure portal som används i NoSQL-självstudiekursen för att skapa ett C#-konsolprogram. Visar ett Azure DB som Cosmos-konto med nycklar markerad i bladet Azure DB som Cosmos-konto och värdena URI och PRIMÄRNYCKEL markerade i bladet nycklar](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Skapa en instans av DocumentClient

Nu skapa en ny instans av den **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Skapa en databas

Skapa sedan en Azure-Cosmos-DB [databasen](documentdb-resources.md#databases) med hjälp av den [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metod eller [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metod för den  **DocumentClient** klass från den [SQL .NET SDK](documentdb-sdk-dotnet.md). En databas är en logisk behållare för JSON-dokumentlagring, partitionerad över samlingarna.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Besluta om en partitionsnyckel 

Samlingar är behållare för att lagra dokument. De logiska resurser och kan [omfattar en eller flera fysiska partitioner](partition-data.md). En [partitionsnyckel](documentdb-partition-data.md) är en egenskap (eller sökväg) i dokument som används för att distribuera din data mellan servrar eller partitioner. Alla dokument med samma partitionsnyckel lagras i samma partition. 

Fastställa en partitionsnyckel är ett viktigt beslut att fatta innan du skapar en samling. Partitionsnycklar är en egenskap (sökväg) i dokument som kan användas med Azure Cosmos DB för att distribuera din data mellan flera servrar eller partitioner. Cosmos DB hashar nyckelvärdet partition och använder hashformaterats resultatet för att avgöra den partition där du vill spara dokumentet. Alla dokument med samma partitionsnyckel lagras i samma partition och partitionsnycklar kan inte ändras när en samling har skapats. 

Den här självstudiekursen kommer vi till Partitionsnyckeln `/deviceId` så att de alla data för en enskild enhet lagras i en enda partition. Du vill välja en partitionsnyckel som har ett stort antal värden, som används på om samma frekvens så Cosmos DB kan läsa belastningsutjämna när dina data växer och uppnå fullt genomflöde i mängden. 

Mer information om partitionering finns [partitionera och skala i Azure Cosmos DB?](partition-data.md) 

## <a id="CreateColl"></a>Skapa en samling 

Nu när vi vet våra partitionsnyckel `/deviceId`, kan du skapa en [samling](documentdb-resources.md#collections) med hjälp av den [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metoden eller [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metod för den **DocumentClient** klass. En samling är en behållare för JSON-dokument och alla associerade JavaScript-programlogik. 

> [!WARNING]
> Skapa en samling har priser effekter, som du reserverar genomströmning för programmet för att kommunicera med Azure Cosmos DB. Mer information finns våra [sida med priser](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Den här metoden gör en REST-API-anrop för att Azure Cosmos DB och den tjänst som tillhandahåller ett antal partitioner baserat på det begärda genomflödet. Du kan ändra genomströmningen i en samling som prestandan utvecklas med hjälp av SDK eller [Azure-portalen](set-throughput.md).

## <a id="CreateDoc"></a>Skapa JSON-dokument
Vi infoga vissa JSON-dokument i Azure Cosmos DB. Du kan skapa [dokument](documentdb-resources.md#documents) med metoden [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) för klassen **dokumentklient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Det här exemplet-klassen innehåller en enhet som läser och ett anrop till CreateDocumentAsync att infoga en ny enhet som läses in en samling.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>Läsa data

Nu ska vi läsa dokumentet av sin partitionsnyckel och Id med metoden ReadDocumentAsync. Observera att läsningar inkluderar ett PartitionKey-värde (som motsvarar den `x-ms-documentdb-partitionkey` huvudet i begäran i REST-API).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Uppdatera data

Nu ska vi uppdatera vissa data med hjälp av metoden ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Ta bort data

Nu kan du ta bort ett dokument med partitionsnyckel och id med hjälp av metoden DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Fråga partitionerade samlingar

När du begär data i partitionerade samlingar dirigerar Azure Cosmos DB automatiskt frågan till partitioner som motsvarar de värdena för partitionen som angetts i filtret (om det finns några). Den här frågan är till exempel dirigeras till bara den partition som innehåller Partitionsnyckeln ”XMS 0001”.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Följande fråga har inte ett filter på partitionsnyckel (DeviceId) och är fanned till alla partitioner där den körs mot den partitionen index. Observera att du måste ange EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` i REST-API) har SDK, för att köra en fråga i partitioner.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Parallell frågekörning
Azure Cosmos DB SQL SDK 1.9.0 och högre support parallell körning frågealternativ, så att du kan utföra låg latens frågor mot partitionerade samlingar, även när de behöver touch ett stort antal partitioner. Till exempel är följande fråga konfigurerad för att köras parallellt över partitioner.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Du kan hantera parallell frågekörning genom att justera följande parametrar:

* Genom att ange `MaxDegreeOfParallelism`, du kan styra i vilken grad av parallellitet i d.v.s., det maximala antalet samtidiga anslutningar till den samling partitioner. Om du anger detta 1, grad av parallellitet hanteras av SDK. Om den `MaxDegreeOfParallelism` är inte angiven eller ange värdet 0, vilket är standardvärdet, finns en nätverksanslutning till den samling partitioner.
* Genom att ange `MaxBufferedItemCount`, du kan handlar av frågan latens och klientsidan minnesanvändning. Om du utelämnar den här parametern eller Ställ in till -1, antal objekt som buffras under parallell frågekörning hanteras av SDK.

Få samma tillstånd i mängden returnerar parallella frågan resultat i samma ordning som seriella körning. När du utför fråga cross-partition som innehåller sortering (ORDER BY och/eller TOP), SQL-SDK skickar fråga parallellt över partitioner och sammanfogar delvis sorterade resulterar i klientsidan inga globalt beställda resultat.

## <a name="execute-stored-procedures"></a>Köra lagrade procedurer
Slutligen kan du köra atomiska transaktioner mot dokument med samma enhets-ID, t.ex. Om du underhålla mängdfunktioner eller det aktuella tillståndet för en enhet i ett enskilt dokument genom att lägga till följande kod i projektet.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

Och det! de är huvudkomponenterna i ett Azure DB som Cosmos-program som använder en partitionsnyckel att effektivt skala Datadistribution över partitioner.  

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda den här appen, tar du bort alla resurser som skapats av den här kursen i Azure-portalen med följande steg:

1. I den vänstra menyn i Azure-portalen klickar du på **resursgrupper** och klicka sedan på det unika namnet för den resurs du skapat. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande: 

> [!div class="checklist"]
> * Skapa ett Azure DB som Cosmos-konto
> * Skapa en databas och samling med en partitionsnyckel
> * Skapade JSON-dokument
> * Uppdatera ett dokument
> * Efterfrågade partitionerade samlingar
> * Körde en lagrad procedur
> * Ta bort ett dokument
> * Ta bort en databas

Du kan nu gå vidare till nästa kurs och importera ytterligare data till Cosmos-DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
