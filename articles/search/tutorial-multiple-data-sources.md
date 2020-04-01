---
title: 'C# Självstudiekurs: Indexera flera datakällor'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du importerar data från flera datakällor till ett enda Azure Cognitive Search-index med hjälp av indexerare. Den här självstudien och exempelkoden finns i C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271487"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Självstudiekurs: Indexera data från flera datakällor i C #

Azure Cognitive Search kan importera, analysera och indexera data från flera datakällor till ett enda konsoliderat sökindex. Detta stöder situationer där strukturerade data aggregeras med mindre strukturerade eller till och med oformaterade textdata från andra källor, till exempel text-, HTML- eller JSON-dokument.

Den här självstudien beskriver hur du indexerar hotelldata från en Azure Cosmos DB-datakälla och sammanfogar det med hotellrumsinformation som hämtats från Azure Blob Storage-dokument. Resultatet blir ett kombinerat hotellsökindex som innehåller komplexa datatyper.

Den här självstudien använder C# och [.NET SDK](https://aka.ms/search-sdk). I den här självstudien ska du utföra följande uppgifter:

> [!div class="checklist"]
> * Ladda upp exempeldata och skapa datakällor
> * Identifiera dokumentnyckeln
> * Definiera och skapa indexet
> * Indexera hotelldata från Azure Cosmos DB
> * Koppla rumsdata från blob-lagring

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnadsfria tjänsten för den här självstudien. En kostnadsfri söktjänst begränsar dig till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Innan du börjar, se till att du har utrymme på din tjänst för att acceptera de nya resurserna.

## <a name="download-files"></a>Hämta filer

Källkoden för den här självstudien finns i GitHub-arkivet för [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) i mappen [med flera datakällor.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)

## <a name="1---create-services"></a>1 - Skapa tjänster

Den här självstudien använder Azure Cognitive Search för indexering och frågor, Azure Cosmos DB för en datauppsättning och Azure Blob-lagring för den andra datauppsättningen. 

Om möjligt kan du skapa alla tjänster i samma region och resursgrupp för närhet och hanterbarhet. I praktiken kan dina tjänster vara i vilken region som helst.

Det här exemplet använder två små datauppsättningar som beskriver sju fiktiva hotell. En uppsättning beskriver hotellen själva och läses in i en Azure Cosmos DB-databas. Den andra uppsättningen innehåller hotellrumsinformation och tillhandahålls som sju separata JSON-filer som ska överföras till Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Börja med Cosmos DB

1. Logga in på [Azure-portalen](https://portal.azure.com)och navigera sedan på sidan Översikt över Azure Cosmos DB-konto.

1. Välj **Data Explorer** och välj sedan Ny **databas**.

   ![Skapa en ny databas](media/tutorial-multiple-data-sources/cosmos-newdb.png "Skapa en ny databas")

1. Ange namnet **hotel-rooms-db**. Acceptera standardvärden för de återstående inställningarna.

   ![Konfigurera databas](media/tutorial-multiple-data-sources/cosmos-dbname.png "Konfigurera databas")

1. Skapa en ny behållare. Använd den befintliga databasen som du just skapade. Ange **hotell** för behållarnamnet och använd **/HotelId** för partitionsnyckeln.

   ![Lägga till containern](media/tutorial-multiple-data-sources/cosmos-add-container.png "Lägga till containern")

1. Välj **Objekt** under **hotell**och klicka sedan på Ladda **upp objekt** i kommandofältet. Navigera till och välj sedan filen **cosmosdb/HotelsDataSubset_CosmosDb.json** i projektmappen.

   ![Ladda upp till Azure Cosmos DB-samling](media/tutorial-multiple-data-sources/cosmos-upload.png "Ladda upp till Cosmos DB-samling")

1. Använd knappen Uppdatera för att uppdatera vyn över objekten i hotellsamlingen. Du bör se sju nya databasdokument i listan.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Logga in på [Azure-portalen,](https://portal.azure.com)navigera till ditt Azure-lagringskonto, klicka på **Blobbar**och klicka sedan på **+ Behållare**.

1. [Skapa en blob container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) med namnet **hotellrum** för att lagra exempelhotellrum JSON filer. Du kan ange den offentliga åtkomstnivån till något av dess giltiga värden.

   ![Skapa en blobcontainer](media/tutorial-multiple-data-sources/blob-add-container.png "Skapa en blobcontainer")

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommandofältet. Navigera till mappen som innehåller exempelfilerna. Markera dem alla och klicka sedan på **Ladda upp**.

   ![Överföra filer](media/tutorial-multiple-data-sources/blob-upload.png "Överföra filer")

När överföringen är klar ska filerna visas i listan för databehållaren.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure Cognitive Search, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnadsfria nivån för att slutföra den här genomgången. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Skaffa en api-nyckel och URL för Azure Cognitive Search

För att interagera med din Azure Cognitive Search-tjänst behöver du tjänst-URL:en och en åtkomstnyckel. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

   Hämta frågenyckeln också. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratörs- och frågenycklar](media/search-get-started-nodejs/service-name-and-keys.png)

En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-your-environment"></a>2 - Konfigurera din miljö

1. Starta Visual Studio 2019 **Tools** och välj **NuGet Package Manager** och **hantera NuGet-paket för lösning...**. 

1. Leta reda på och installera **Microsoft.Azure.Search** (version 9.0.1 eller senare) på fliken **Bläddra.** Du måste klicka igenom ytterligare dialogrutor för att slutföra installationen.

    ![Använda NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Sök efter **paketet Microsoft.Extensions.Configuration.Json** NuGet och installera det också.

1. Öppna lösningsfilen **AzureSearchMultipleDataSources.sln**.

1. I Solution Explorer redigerar du filen **appsettings.json** för att lägga till anslutningsinformation.  

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

De två första posterna använder URL-och administratörsnycklarna för din Azure Cognitive Search-tjänst. Med en slutpunkt för `https://mydemo.search.windows.net`till exempel är `mydemo`tjänstnamnet som ska anges .

Nästa poster anger kontonamn och anslutningsstränginformation för Azure Blob Storage och Azure Cosmos DB-datakällor.

## <a name="3---map-key-fields"></a>3 - Mappa nyckelfält

Sammanslagning av innehåll kräver att båda dataströmmarna inriktar sig på samma dokument i sökindexet. 

I Azure Cognitive Search identifierar nyckelfältet unikt varje dokument. Varje sökindex måste ha exakt `Edm.String`ett nyckelfält av typen . Det nyckelfältet måste finnas för varje dokument i en datakälla som läggs till i indexet. (I själva verket är det det enda obligatoriska fältet.)

När du indexerar data från flera datakällor kontrollerar du att varje inkommande rad eller ett inkommande dokument innehåller en gemensam dokumentnyckel för att koppla data från två fysiskt distinkta källdokument till ett nytt sökdokument i det kombinerade indexet. 

Det krävs ofta viss planering på framsidan för att identifiera en meningsfull dokumentnyckel för indexet och se till att den finns i båda datakällorna. I denna `HotelId` demo, nyckeln för varje hotell i Cosmos DB finns också i rummen JSON blobbar i Blob lagring.

Azure Cognitive Search-indexerare kan använda fältmappningar för att byta namn på och till och med formatera om datafält under indexeringsprocessen, så att källdata kan dirigeras till rätt indexfält. I Cosmos DB kallas **`HotelId`** till exempel hotellidentifieraren . Men i JSON blob filer för hotellrum, är **`Id`** hotellets identifierare heter . Detta hanteras genom att **`Id`** fältet mappas från **`HotelId`** blobbar till nyckelfältet i indexet.

> [!NOTE]
> I de flesta fall gör automatiskt genererade dokumentnycklar, till exempel de som skapats som standard av vissa indexerare, inte bra dokumentnycklar för kombinerade index. I allmänhet kommer du att vilja använda ett meningsfullt, unikt nyckelvärde som redan finns i, eller kan enkelt läggas till, dina datakällor.

## <a name="4---explore-the-code"></a>4 - Utforska koden

När data- och konfigurationsinställningarna är på plats bör exempelprogrammet i **AzureSearchMultipleDataSources.sln** vara redo att skapa och köras.

Den här enkla C#/.NET-konsolappen utför följande uppgifter:

* Skapar ett nytt index baserat på datastrukturen i klassen C# Hotel (som också refererar till adress- och rumsklasserna).
* Skapar en ny datakälla och en indexerare som mappar Azure Cosmos DB-data till indexfält. Dessa är båda objekten i Azure Cognitive Search.
* Kör indexeraren för att läsa in hotelldata från Cosmos DB.
* Skapar en andra datakälla och en indexerare som mappar JSON-blobdata till indexfält.
* Kör den andra indexeraren för att läsa in rumsdata från Blob-lagring.

 Innan du kör programmet, ta en minut att studera koden och index och indexerare definitioner för detta exempel. Den relevanta koden finns i två filer:

  + **Hotel.cs** innehåller schemat som definierar indexet
  + **Program.cs** innehåller funktioner som skapar Azure Cognitive Search-index, datakällor och indexerare och läser in de kombinerade resultaten i indexet.

### <a name="create-an-index"></a>Skapa ett index

Det här exempelprogrammet använder .NET SDK för att definiera och skapa ett Azure Cognitive Search-index. Den utnyttjar [klassen FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) för att generera en indexstruktur från en C#-datamodellklass.

Datamodellen definieras av hotellklassen, som också innehåller referenser till adress- och rumsklasserna. FieldBuilder borrar nedåt genom flera klassdefinitioner för att generera en komplex datastruktur för indexet. Metadatataggar används för att definiera attributen för varje fält, till exempel om det är sökbart eller sorterbart.

Följande utdrag från **Hotel.cs** filen visar hur ett enda fält och en referens till en annan datamodellklass kan anges.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

I **Program.cs-filen** definieras indexet med ett namn och en fältsamling `FieldBuilder.BuildForType<Hotel>()` som genereras av metoden och skapas sedan på följande sätt:

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

Nästa huvudprogrammet innehåller logik för att skapa Azure Cosmos DB datakälla för hotelldata.

Först sammanfogas Azure Cosmos DB-databasnamnet till anslutningssträngen. Sedan definierar datakällobjektet, inklusive inställningar som är specifika för Azure Cosmos DB-källor, till exempel egenskapen [useChangeDetection].

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

När datakällan har skapats konfigureras en Azure Cosmos DB-indexerare med namnet **hotel-rooms-cosmos-indexer .**

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
Alla befintliga indexerare med samma namn tas bort innan du skapar det nya, om du vill köra det här exemplet mer än en gång.

Det här exemplet definierar ett schema för indexeraren så att det körs en gång per dag. Du kan ta bort schemaegenskapen från det här anropet om du inte vill att indexeraren ska köras automatiskt igen i framtiden.

### <a name="index-azure-cosmos-db-data"></a>Index Azure Cosmos DB-data

När datakällan och indexeraren har skapats är koden som kör indexeraren kort:

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

Det här exemplet innehåller ett enkelt try-catch-block för att rapportera eventuella fel som kan uppstå under körningen.

När Azure Cosmos DB-indexeraren har körts innehåller sökindexet en fullständig uppsättning exempel på hotelldokument. Men rumsfältet för varje hotell kommer att vara en tom matris, eftersom Azure Cosmos DB-datakällan inte innehöll några rumsinformation. Därefter kommer programmet att hämta från Blob-lagring för att läsa in och slå samman rumsdata.

### <a name="create-blob-storage-data-source-and-indexer"></a>Skapa Blob storage datakälla och indexerare

För att få rumsinformationen ställer programmet först in en Blob storage-datakälla för att referera till en uppsättning enskilda JSON-blobfiler.

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

När datakällan har skapats konfigureras en blob-indexerare med namnet **hotel-rooms-blob-indexerare**.

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

JSON-blobbar innehåller ett **`Id`** nyckelfält **`HotelId`** med namnet i stället för . Koden använder `FieldMapping` klassen för att tala om **`Id`** för indexeraren att rikta fältvärdet till **`HotelId`** dokumentnyckeln i indexet.

Blob-lagringsindexerare kan använda parametrar som identifierar det tolkningsläge som ska användas. Tolkningsläget skiljer sig åt för blobbar som representerar ett enda dokument eller flera dokument inom samma blob. I det här exemplet representerar varje blob ett `IndexingParameters.ParseJson()` enda indexdokument, så koden använder parametern.

Mer information om indexeringsparametrar för JSON-blobbar finns i [Index JSON-blobbar](search-howto-index-json-blobs.md). Mer information om hur du anger dessa parametrar med hjälp av .NET SDK finns i klassen [IndexerParametersExtension.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions)

Alla befintliga indexerare med samma namn tas bort innan du skapar det nya, om du vill köra det här exemplet mer än en gång.

Det här exemplet definierar ett schema för indexeraren så att det körs en gång per dag. Du kan ta bort schemaegenskapen från det här anropet om du inte vill att indexeraren ska köras automatiskt igen i framtiden.

### <a name="index-blob-data"></a>Index blob data

När Blob-lagringsdatakällan och indexeraren har skapats är koden som kör indexeraren enkel:

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

Eftersom indexet redan har fyllts i med hotelldata från Azure Cosmos DB-databasen uppdaterar blob-indexeraren befintliga dokument i indexet och lägger till rumsinformationen.

> [!NOTE]
> Om du har samma icke-nyckelfält i båda dina datakällor och data i dessa fält inte matchar, innehåller indexet värdena från den indexerare som senast kördes. I vårt exempel innehåller båda datakällorna ett **HotelName-fält.** Om data i det här fältet av någon anledning är olika, för dokument med samma nyckelvärde, kommer **HotelName-data** från datakällan som indexerades senast att vara det värde som lagras i indexet.

## <a name="5---search"></a>5 - Sök

Du kan utforska det ifyllda sökindexet när programmet har körts med hjälp av [**sökutforskaren**](search-explorer.md) i portalen.

Öppna **sidan** Översikt för söktjänsten i **Azure-portalen** och hitta exempelindexet för hotellrum i **indexlistan.**

  ![Lista över Azure Cognitive Search-index](media/tutorial-multiple-data-sources/index-list.png "Lista över Azure Cognitive Search-index")

Klicka på hotell-rums-prov index i listan. Du kommer att se ett Search Explorer-gränssnitt för indexet. Ange en fråga för en term som "Lyx". Du bör se minst ett dokument i resultatet, och det här dokumentet bör visa en lista över rumsobjekt i rumsmatrisen.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella utvecklingsstadierna är den mest praktiska metoden för designiteration att ta bort objekten från Azure Cognitive Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempelkoden för den här självstudien söker efter befintliga objekt och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare och datakällor.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken Alla resurser eller Resursgrupper i fönstret för vänsternavigering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med konceptet att inta data från flera källor, låt oss ta en närmare titt på indexer konfiguration, börjar med Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurera en Azure Cosmos DB-indexerare](search-howto-index-cosmosdb.md)