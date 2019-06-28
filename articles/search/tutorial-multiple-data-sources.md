---
title: 'Självstudier: Indexera flera datakällor – Azure Search'
description: Lär dig hur du importerar data från flera källor till ett enda Azure Search-index.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 4186c422836771de4f8a283616d77214b91bfc02
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462704"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Självstudie: Kombinera data från flera datakällor i en Azure Search-index

Azure Search kan du importera, analysera och indexera data från flera källor till en enda kombinerade search-index. Det ger stöd för situationer där strukturerade data sammanställs med mindre strukturerat eller t.o.m. oformaterad textdata från andra källor, som text, HTML, eller JSON-dokument.

Den här självstudien beskrivs hur du indexera Hotelldata från en Azure Cosmos DB-datakälla och sammanfoga som med information om hotell utrymme hämtas från Azure Blob Storage-dokument. Resultatet blir en kombinerad hotell search-index som innehåller komplexa datatyper.

Den här självstudien används C#, .NET SDK för Azure Search och Azure-portalen för att utföra följande uppgifter:

> [!div class="checklist"]
> * Ladda upp exempeldata och skapa datakällor
> * Identifiera dokumentnyckeln
> * Definiera och skapa indexet
> * Index Hotelldata från CosmosDb
> * Slå samman hotell rummet data från blob storage

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data som används i den här snabbstarten. 

- [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen.

- [Skapa ett Azure Cosmos DB-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempeldata för hotell.

- [Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exemplet-JSON blob-data.

- [Installera Visual Studio](https://visualstudio.microsoft.com/) ska användas som IDE.

### <a name="install-the-project-from-github"></a>Installera projektet från GitHub

1. Leta upp lagringsplatsen för exemplet på GitHub: [azure search-dotnet-exempel](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Välj **klona eller ladda ned** och gör den privata lokala kopian av databasen.
1. Öppna Visual Studio och installera Microsoft Azure Search NuGet-paketet om du inte redan är installerat. I den **verktyg** menyn och välj **NuGet-Pakethanteraren** och sedan **hantera NuGet-paket för lösningen...** . Välj den **Bläddra** fliken och Skriv ”Azure Search” i sökrutan. Installera **Microsoft.Azure.Search** när den visas i listan (version 9.0.1, eller senare). Du måste klicka dig igenom ytterligare dialogrutor för att slutföra installationen.

    ![Med NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Med Visual Studio, navigera till din lokala lagringsplats och öppna lösningsfilen **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att interagera med Azure Search-tjänsten, behöver du tjänstens URL och en åtkomstnyckel. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende regelbundet per begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Förbereda exempeldata i Azure Cosmos DB

Det här exemplet används två små mängder data som beskriver sju fiktiva hotell. En uppsättning beskriver hotels sig själva och kommer att läsas in i en Azure Cosmos DB-databas. Den andra uppsättningen innehåller information om rummet hotell och tillhandahålls som sju separata JSON-filer som ska överföras till Azure Blob Storage.

1. [Logga in på Azure-portalen](https://portal.azure.com), och sedan gå kontoöversikten Azure Cosmos DB.

1. På menyraden klickar du på Lägg till behållare. Ange ”Skapa ny databas” och använda namnet **hotell-rum-db**. Ange **hotellrum** för namnet på samlingen och **/HotelId** för Partitionsnyckeln. Klicka på **OK** att skapa databasen och behållare.

   ![Lägg till Azure Cosmos DB-behållare](media/tutorial-multiple-data-sources/cosmos-add-container.png "Lägg till ett Azure Cosmos DB-behållare")

1. Gå till Cosmos DB Data Explorer och välj den **objekt** elementet under den **hotels** behållare i den **hotell-rum-db** databas. Klicka sedan på **ladda upp objekt** i kommandofältet.

   ![Ladda upp till Azure Cosmos DB-samling](media/tutorial-multiple-data-sources/cosmos-upload.png "ladda upp till Cosmos DB-samling")

1. Klicka på mappknappen på panelen ladda upp och gå sedan till filen **cosmosdb/HotelsDataSubset_CosmosDb.json** i projektmappen. Klicka på **OK** så startas guiden Överför.

   ![Välj fil att ladda upp](media/tutorial-multiple-data-sources/cosmos-upload2.png "Välj fil att ladda upp")

1. Använd knappen Uppdatera om du vill uppdatera vyn över hur objekten i samlingen hotell. Du bör se sju nya databasdokument som anges.

## <a name="prepare-sample-blob-data"></a>Förbered blob exempeldata

1. [Logga in på Azure-portalen](https://portal.azure.com)navigerar du till ditt Azure storage-konto, klickar du på **Blobar**, och klicka sedan på **+ behållare**.

1. [Skapa en blobbehållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) med namnet **hotellrum** att lagra JSON-filerna exempel hotell rummet. Du kan ange offentlig åtkomstnivå till någon av dess giltiga värden.

   ![Skapa en blobbehållare](media/tutorial-multiple-data-sources/blob-add-container.png "skapa en blobbehållare")

1. När behållaren har skapats kan du öppna den och välj **överför** i kommandofältet.

   ![Ladda upp i kommandofältet](media/search-semi-structured-data/upload-command-bar.png "överför i kommandofältet")

1. Navigera till mappen som innehåller exempelfilerna. Markera alla av dem och klicka sedan på **överför**.

   ![Ladda upp filer](media/tutorial-multiple-data-sources/blob-upload.png "ladda upp filer")

När överföringen är klar ska filerna visas i listan för databehållaren.

## <a name="set-up-connections"></a>Konfigurera anslutningar

Anslutningsinformationen för search-tjänsten och vilka datakällor som har angetts i den **appsettings.json** filen i lösningen. 

1. Visual Studio, öppna den **AzureSearchMultipleDataSources.sln** fil.

1. I Solution Explorer, redigera den **appsettings.json** fil.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "BlobStorageAccountName": "Put your Azure Storage account name here",
  "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
  "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
  "CosmosDBDatabaseName": "hotel-rooms-db"
}
```

De två första posterna använda tangenterna URL och administratör för Azure Search-tjänsten. Får en slutpunkt av `https://mydemo.search.windows.net`, till exempel namnet på tjänsten för att tillhandahålla är `mydemo`.

Nästa posterna Ange kontonamn och informationen i anslutningssträngen för Azure Blob Storage och Azure Cosmos DB-datakällor.

### <a name="identify-the-document-key"></a>Identifiera dokumentnyckeln

I Azure Search identifierar nyckelfältet varje dokument i indexet. Varje search-index måste ha exakt ett fält av typen `Edm.String`. Viktiga fältet måste vara tillgänglig för alla dokument i en datakälla som har lagts till i indexet. (I själva verket det är det enda obligatoriska fältet.)

Vid indexering av data från flera datakällor, kommer varje nyckel värdet för datakällan måste mappas till samma nyckelfältet i kombinerade indexet. Det kräver ofta vissa planering inför att identifiera en meningsfull dokumentnyckeln för ditt index och kontrollera att den finns i varje datakälla.

Azure Search-indexerare kan använda fältmappningar att byta namn på och även formatera om datafält under indexeringsprocessen, så att källdata kan dirigeras till rätt index-fältet.

Till exempel i våra exempeldata CosmosDB, hotell ID kallas **HotelId**. Men i JSON blob-filerna för hotellrum, hotell identifieraren heter **Id**. Programmet ska hantera detta genom att mappa den **Id** från blobbarna-och den **HotelId** fält i indexet.

> [!NOTE]
> I de flesta fall gör inte automatiskt genererade dokument nycklar, till exempel de som skapas som standard med några indexerare, bra dokumentet nycklar för kombinerade index. I allmänhet du vill använda ett beskrivande, unikt nyckelvärde som redan finns i eller enkelt kan läggas till dina datakällor.

## <a name="understand-the-code"></a>Förstå koden

När inställningar och konfiguration är på plats kan exemplet programmet i **AzureSearchMultipleDataSources.sln** bör vara redo att skapa och köra.

Det här enkla C#/.NET-konsolapp utför följande uppgifter:
* Skapar ett nytt Azure Search-index baserat på datastrukturen för den C# hotell klass (som även hänvisar till klasserna adress och rum).
* Skapar en Azure Cosmos DB-datakälla och en indexerare som mappar Azure Cosmos DB-data till indexet fält.
* Kör CosmosDB-indexeraren för att läsa in data för hotell.
* Skapar en Azure Blob Storage-datakälla och en indexerare som mappar JSOn-Blob-data till indexet fält.
* Kör Azure blob storage-indexeraren för att läsa in data om rum.

 Innan du kör programmet måste du ta en stund åt att studera koden och index och indexerare definitioner för det här exemplet. Den relevanta koden finns i två filer:

  + **Hotel.CS** innehåller ett schema som definierar indexet
  + **Program.CS** innehåller funktioner som skapar Azure Search-index, datakällor och indexerare och läsa in det kombinerade resultatet i indexet.

### <a name="define-the-index"></a>Definiera indexet

I det här exempelprogrammet använder .NET SDK för att definiera och skapa ett Azure Search-index. Det utnyttjar den [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) klassen för att generera ett index strukturen från en C# data modellklass.

Datamodellen definieras av klassen hotell, som också innehåller referenser till klasserna adress och utrymme. FieldBuilder flyttar ned via flera klassdefinitioner att generera en komplex datastruktur för indexet. Metadatataggarna används för att ange attribut för varje fält, till exempel om den är sökbara eller sorterbart.

Följande kodavsnitt från den **Hotel.cs** filen visar hur ett fält och en referens till en annan data modellklass kan anges.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

I den **Program.cs** filen, indexet definieras med ett namn och en fältsamling som genererats av den `FieldBuilder.BuildForType<Hotel>()` metoden och skapade sedan enligt följande:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Skapa Azure Cosmos DB-datakälla och indexerare

Bredvid innehåller huvudprogrammet logik för att skapa Azure Cosmos DB-datakälla för hotels-data.

Den sammanfogar först namnet på Azure Cosmos DB-databasen på anslutningssträngen. Den definierar datakällobjektet, inklusive inställningar som är specifika för Azure Cosmos DB-källor, till exempel [useChangeDetection]-egenskapen.

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

När datakällan har skapats kan programmet lägger upp ett Azure Cosmos DB-indexeraren med namnet **hotell-rooms-cosmos-indexeraren**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Alla befintliga indexerare med samma namn raderas innan du skapar en ny om du vill köra det här exemplet mer än en gång.

Det här exemplet definierar ett schema för indexeraren, så att den körs en gång per dag. Du kan ta bort egenskapen schemat från det här anropet om du inte vill att indexeraren för att köra automatiskt igen i framtiden.

### <a name="index-azure-cosmos-db-data"></a>Index Azure Cosmos DB-data

När du har skapat datakällan och indexeraren är den kod som körs indexeraren kort:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Det här exemplet innehåller ett enkelt trycatch-block för att rapportera eventuella fel som kan uppstå under körning.

När Azure Cosmos DB-indexeraren har körts innehåller sökindexet en fullständig uppsättning hotell exempeldokument. Fältet rum för varje hotell kommer dock vara tomma matriser, eftersom Azure Cosmos DB-datakälla innehåller ingen information om rummet. Därefter hämtar programmet från Blob storage för att läsa in och slå samman data om rum.

### <a name="create-blob-storage-data-source-and-indexer"></a>Skapa Blob storage-datakälla och indexerare

Om du vill hämta information om rummet ställer programmet först in en datakälla för Blob storage att referera till en uppsättning enskilda filer i JSON-blob.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

När datakällan har skapats kan programmet lägger upp en blob-indexeraren med namnet **hotell-rum-blob-indexeraren**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

JSON-blobar innehåller ett nyckelfält med namnet **Id** i stället för **HotelId**. Koden använder den `FieldMapping` klassen för att berätta för indexeraren att dirigera den **Id** fältet värde till den **HotelId** dokumentnyckeln i indexet.

BLOB storage-indexerare kan använda parametrarna som identifierar parsning läge som ska användas. Parsning läget skiljer sig för BLOB-objekt som representerar ett enskilt dokument eller flera dokument i samma blob. I det här exemplet representerar varje blob enda index dokument, så används den `IndexingParameters.ParseJson()` parametern.

Läs mer om indexerare parsning parametrar för JSON-blobar, [indexera JSON-blobbar](search-howto-index-json-blobs.md). Mer information om hur du anger dessa parametrar med .NET SDK finns i den [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) klass.

Alla befintliga indexerare med samma namn raderas innan du skapar en ny om du vill köra det här exemplet mer än en gång.

Det här exemplet definierar ett schema för indexeraren, så att den körs en gång per dag. Du kan ta bort egenskapen schemat från det här anropet om du inte vill att indexeraren för att köra automatiskt igen i framtiden.

### <a name="index-blob-data"></a>Indexera blob-data

När du har skapat datakällan för Blob storage och indexerare är den kod som körs indexeraren enkel:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Eftersom indexet har redan fyllts med Hotelldata från Azure Cosmos DB-databas, blob-indexeraren uppdaterar befintliga dokument i indexet och lägger till rummet information.

> [!NOTE]
> Om du har samma fält för icke-nyckeln i båda dina datakällor och data i dessa fält matchar inte, och sedan indexet innehåller värden från indexeraren senast kördes. I vårt exempel innehåller båda datakällorna en **HotelName** fält. Om du av något skäl data i det här fältet är olika för dokument med samma nyckelvärde, kommer **HotelName** data från datakällan som har indexerats senast blir det värde som lagras i indexet.

## <a name="search-your-json-files"></a>Söka i JSON-filer

Du kan utforska fylls i automatiskt search-index när programmet har körts med hjälp av den [ **Sökutforskaren** ](search-explorer.md) i portalen.

I Azure-portalen öppnar du söktjänsten **översikt** sidan och hitta den **hotell-rum-sample** index i den **index** lista.

  ![Lista över Azure Search-index](media/tutorial-multiple-data-sources/index-list.png "lista i Azure Search-index")

Klicka på hotell-rum-sample-index i listan. Du kan se ett Sökutforskaren gränssnitt för indexet. Ange en fråga för en term som ”Lyxig”. Du bör se minst ett dokument i resultatet och det här dokumentet ska visa en lista över rummet objekt i dess rum-matris.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. Resursgruppens namn finns på översiktssidan för Azure Search-tjänst i portalen.

## <a name="next-steps"></a>Nästa steg

Det finns flera metoder och flera olika sätt att indexera JSON-blobar. Om dina källdata innehåller JSON-innehåll kan granska du dessa alternativ för att se vad som fungerar bäst för ditt scenario.

> [!div class="nextstepaction"]
> [Indexera JSON-blobar med Azure Search Blob-indexeraren](search-howto-index-json-blobs.md)

Du kanske vill utöka strukturerade indexera data från en datakälla med cognitively avancerad och data från Ostrukturerade BLOB- och Fulltext-innehåll. Följande självstudie visar hur du använder Cognitive Services tillsammans med Azure Search med .NET SDK.

> [!div class="nextstepaction"]
> [Anropa API: er med Cognitive Services i ett Azure Search indexering av pipeline](cognitive-search-tutorial-blob-dotnet.md)
