---
title: 'Azure Cosmos DB: Utveckla med SQL API i .NET | Microsoft Docs'
description: Lär dig utveckla med Azure Cosmos DB:s SQL API med .NET
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: a6ed74de159593003e8a18daefce2eb9a5945481
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: Utveckla med SQL API i .NET

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

Den här självstudien visar hur du skapar ett Azure Cosmos DB-konto med Azure Portal och sedan skapar en dokumentdatabas och samling med en [partitionsnyckel](sql-api-partition-data.md#partition-keys) med hjälp av [SQL .NET API:t](sql-api-introduction.md). Genom att definiera en partitionsnyckel när du skapar en samling, är ditt program förberett att skala utan problem allteftersom dina data växer.

Den här självstudien går igenom följande uppgifter med [SQL .NET API:t](sql-api-sdk-dotnet.md):

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa en databas och samling med en partitionsnyckel
> * Skapa JSON-dokument
> * Uppdatera ett dokument
> * Fråga partitionerade samlingar
> * Kör lagrade procedurer
> * Ta bort ett dokument
> * Ta bort en databas

## <a name="prerequisites"></a>Nödvändiga komponenter
Se till att du har följande innan du börjar:

* Åtkomst till ett Azure Cosmos DB-konto

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

  Du kan också använda din egen Azure-prenumeration genom att registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Därifrån kan du [Skapa ett Azure Cosmos DB-konto](create-sql-api-dotnet.md#create-a-database-account).

* Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.


> [!TIP]
> * Om du väljer att använda Azure Cosmos DB-emulatorn följer du stegen i [Azure Cosmos DB-emulator](local-emulator.md) för att konfigurera emulatorn
>
>


## <a id="SetupVS"></a>Konfigurera din Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I dialogen **Nytt projekt**, väljer du **Mallar** / **Visual C#** / **Konsolapp (.NET Framework)**. Namnge ditt projekt och klicka sedan på **OK**.
   ![Skärmdump som visar fönstret Nytt projekt](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**

    ![Skärmdump som visar den högerklickade menyn för projektet](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. På fliken **NuGet**, klickar du på **Bläddra** och skriver in **documentdb** i sökrutan.
<!---stopped here--->
6. Leta reda på **Microsoft.Azure.DocumentDB** i resultatet och klicka på **Installera**.
   Paket-ID:t för Azure Cosmos DB-klientbiblioteket är [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Skärmbild av NuGet-menyn där du hittar Azure Cosmos DB-klient-SDK:n](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.

## <a id="Connect"></a>Lägg till referenser till ditt projekt
De kvarvarande stegen i den här självstudiekursen ger dig de SQL API-kodstycken som krävs för att skapa och uppdatera Azure Cosmos DB-resurser i ditt projekt.

Först lägger du till de här referenserna till ditt program.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Anslut din app

Därefter lägger du till de här två konstanterna och din *klient*-variabel i ditt program.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Gå sedan tillbaka till [Azure Portal](https://portal.azure.com) för att hämta din slutpunkts-URL och primärnyckel. Slutpunkts-URL:en och den primära nyckeln behövs för att programmet ska veta vart ditt program ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning.

Gå till ditt Azure Cosmos DB-konto i Azure Portal. I den vänstra menyn väljer du **Nycklar** och sedan **Läs- och skrivnycklar**.

Kopiera URI:n från portalen och klistra in den över `<your endpoint URL>` i filen program.cs. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den över `<your primary key>`. Var noga med att ta bort `<` och `>` från dina värden.

![Skärmbild av Azure Portal som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp. Visar ett Azure Cosmos DB-konto där NYCKLAR är markerat i Azure Cosmos DB-kontoavsnittet och där värdena URI och PRIMÄRNYCKEL är markerade i Nycklar-avsnittet](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Skapa en instans av DocumentClient

Nu skapar du en ny instans av **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Skapa en databas

Därefter skapar du en Azure Cosmos DB-[databas](sql-api-resources.md#databases) med metoderna [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) eller [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) i klassen **DocumentClient** från [SQL .NET-SDK:n](sql-api-sdk-dotnet.md). En databas är en logisk behållare för JSON-dokumentlagring, partitionerad över samlingarna.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Besluta om en partitionsnyckel

Samlingar är behållare för att lagra dokument. De är logiska resurser och kan [sträcka sig över en eller flera fysiska partitioner](partition-data.md). En [partitionsnyckel](sql-api-partition-data.md) är en egenskap (eller sökväg) i dina dokument som används för att distribuera dina data mellan servrarna eller partitionerna. Alla dokument med samma partitionsnyckel lagras på samma partition.

Det är viktigt att fastställa en partitionsnyckel innan du skapar en samling. Partitionsnycklar är en egenskap (eller sökväg) i dina dokument som kan användas av Azure Cosmos DB för att distribuera dina data mellan flera servrar eller partitioner. Cosmos DB hashar partitionsnyckelvärdet och använder det hashade resultatet för att fastställa på vilken partition som dokumentet ska lagras. Alla dokument med samma partitionsnyckel lagras på samma partition och partitionsnycklar kan inte ändras när en samling väl har skapats.

I den här självstudien måste du ange partitionsnyckeln till `/deviceId` så att alla data för en enskild enhet lagras på en enda partition. Du bör välja en partitionsnyckel som har ett stort antal värden, där varje värde används i ungefär samma frekvens för att försäkra dig om att Cosmos DB kan belastningsutjämna allteftersom dina data växer och uppnår fullt genomflöde av samlingen.

Mer information om partitionering finn i [Så här partitionerar och skalar du i Azure Cosmos DB.](partition-data.md)

## <a id="CreateColl"></a>Skapa en samling

Med partitionsnyckeln `/deviceId` kan du skapa en [samling](sql-api-resources.md#collections) med hjälp av metoderna [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) eller [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) i klassen **DocumentClient**. En samling är en behållare med JSON-dokument och associerad JavaScript-applogik.

> [!WARNING]
> Det finns priseffekter med att skapa en samling eftersom du reserverar genomströmning för programmet för att kommunicera med Azure Cosmos DB. Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/)
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

Den här metoden gör ett REST API-anrop till Azure Cosmos DB och tjänsten etablerar ett antal partitioner baserat på det begärda genomströmningen. Du kan ändra genomströmningen i en samling allteftersom dina prestandabehov utvecklas med hjälp av SDK:n eller [Azure Portal](set-throughput.md).

## <a id="CreateDoc"></a>Skapa JSON-dokument
Vi infogar några JSON-dokument i Azure Cosmos DB. Du kan skapa [dokument](sql-api-resources.md#documents) med metoden [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) för klassen **dokumentklient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Den här exempelklassen innehåller en enhet som läser och ett anrop till CreateDocumentAsync att infoga en ny enhet som läser i en samling.

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

Nu ska vi läsa dokumentet efter dess partitionsnyckel och ID med metoden ReadDocumentAsync. Observera att läsningar inkluderar ett PartitionKey-värde (som motsvarar `x-ms-documentdb-partitionkey`-begäranderubriken i REST API).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Uppdatera data

Nu ska vi uppdatera några data med hjälp av metoden ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  reading);
```

## <a name="delete-data"></a>Ta bort data

Nu ska vi ta bort ett dokument efter dess partitionsnyckel och ID med hjälp av metoden DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Fråga partitionerade samlingar

När du begär data i partitionerade samlingar dirigerar Azure Cosmos DB automatiskt frågan till de partitioner som motsvarar partitionsnyckelvärdena som angetts i filtret (om det finns några). Den här frågan dirigeras till exempel enbart till den partition som innehåller partitionsnyckeln XMS-0001.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

Följande fråga har inget filter på partitionsnyckeln (DeviceId) och är utspridd till alla partitioner där den körs mot partitionens index. Observera att du måste ange EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` i REST API:t) för att SDK:t ska köra en fråga över partitioner.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Parallell frågekörning
Azure Cosmos DB SQL SDK:er 1.9.0 och högre stöder alternativ för parallell frågekörning, vilket låter dig utföra frågor med låg svarstid mot partitionerade samlingar, även när de behöver vidröra ett stort antal partitioner. Följande fråga är till exempel konfigurerad för att köras parallellt över partitioner.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Du kan hantera parallell frågekörning genom att justera följande parametrar:

* Genom att ange `MaxDegreeOfParallelism`, kan du kontrollera graden av parallellitet d.v.s. det maximala antalet samtidiga nätverksanslutningar till samlingens partitioner. Om du anger parametern till -1 hanteras graden av parallellitet av SDK:n. Om `MaxDegreeOfParallelism` inte har angivits eller har angetts till 0, vilket är standardvärdet så finns det en enda nätverksanslutning till samlingens partitioner.
* Genom att ange `MaxBufferedItemCount`, kan du avväga frågesvarstider och minnesanvändning på klientsidan. Om du utelämnar den här parametern eller anger den till -1, hanteras antalet objekt som buffras under en parallell frågekörning av SDK:n.

Med samma status för samlingen, returnerar en parallell fråga resultat i samma ordning som vid seriell körning. När du utför en fråga över partitioner som inkluderar sortering (ORDER BY och/eller TOP), utfärdar SQL SDK:n frågan parallellt över partitionerna och sammanfogar delvis sorterade resulterar i klientsidan för att skapa globalt sorterade resultat.

## <a name="execute-stored-procedures"></a>Kör lagrade procedurer
Slutligen kan du köra atomiska transaktioner mot dokument med samma enhets-ID, t.ex. om du hanterar aggregat eller det senaste tillståndet för en enhet i ett enskilt dokument genom att lägga till följande kod till ditt projekt.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") },
    "XMS-001-FE24C");
```

Det var det! där har vi huvudkomponenterna i ett Azure Cosmos DB-program som använder en partitionsnyckel för att effektivt skala datadistribution över partitioner.  

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen, tar du bort alla resurser som skapades av självstudien i Azure Portal med följande steg:

1. I den vänstra menyn i Azure Portal, klickar du på **Resursgrupper** och därefter på det unika namnet på den resurs du skapade.
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Skapat ett Azure Cosmos DB-konto
> * Skapat en databas och en samling med en partitionsnyckel
> * Skapat JSON-dokument
> * Uppdaterat ett dokument
> * Frågat partitionerade samlingar
> * Kört en lagrad procedur
> * Tagit bort ett dokument
> * Tagit bort en databas

Du kan nu fortsätta till nästa självstudie och importera ytterligare data till ditt Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
