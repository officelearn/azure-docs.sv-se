---
title: 'C#Självstudie: indexera flera data källor'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du importerar data från flera data källor till ett enda Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 69b18cdd4d0bb8e3d13bbacd5d21764004308786
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795646"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-cognitive-search-index"></a>C#Självstudie: kombinera data från flera data källor i ett Azure Kognitiv sökning-index

Azure Kognitiv sökning kan importera, analysera och indexera data från flera data källor till ett enda kombinerat Sök index. Detta stöder situationer där strukturerade data sammanställs med mindre strukturerade eller udda text data från andra källor, t. ex. text-, HTML-eller JSON-dokument.

I den här självstudien beskrivs hur du kan indexera hotell data från en Azure Cosmos DB data källa och slå samman med information om hotell rums information från Azure Blob Storage dokument. Resultatet blir ett kombinerat hotell Sök index som innehåller komplexa data typer.

I den här C#självstudien använder vi .NET SDK för Azure Kognitiv sökning och Azure Portal för att utföra följande uppgifter:

> [!div class="checklist"]
> * Ladda upp exempel data och skapa data källor
> * Identifiera dokument nyckeln
> * Definiera och skapa indexet
> * Indexera hotell data från Azure Cosmos DB
> * Slå samman hotell rums data från Blob Storage

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster, verktyg och data används i den här snabb starten. 

- [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

- [Skapa ett Azure Cosmos DB-konto](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) för att lagra exempel på hotell data.

- [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel JSON-BLOB-data.

- [Installera Visual Studio](https://visualstudio.microsoft.com/) för att använda som IDE.

### <a name="install-the-project-from-github"></a>Installera projektet från GitHub

1. Leta upp exempel lagrings platsen på GitHub: [Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Välj **klona eller ladda ned** och gör din privata lokala kopia av lagrings platsen.
1. Öppna Visual Studio och installera Microsoft Azure Kognitiv sökning NuGet-paketet, om det inte redan har installerats. I menyn **verktyg** väljer du **NuGet Package Manager** och hanterar sedan **NuGet-paket för lösning...** . På fliken **Bläddra** letar du reda på och installerar **Microsoft. Azure. search** (version 9.0.1 eller senare). Du måste klicka på ytterligare dialog rutor för att slutföra installationen.

    ![Använda NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Använd Visual Studio, navigera till din lokala lagrings plats och öppna lösnings filen **AzureSearchMultipleDataSources. SLN**.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

Om du vill interagera med din Azure Kognitiv sökning-tjänst behöver du tjänst-URL och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**får du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende per begäran mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Förbereda exempel Azure Cosmos DB data

I det här exemplet används två små uppsättningar med data som beskriver sju fiktiva hotell. En uppsättning beskriver själva hotellen och kommer att läsas in i en Azure Cosmos DB databas. Den andra uppsättningen innehåller information om hotell rummet och tillhandahålls som sju separata JSON-filer som ska överföras till Azure Blob Storage.

1. [Logga](https://portal.azure.com)in på Azure Portal och navigera sedan till översikts sidan för Azure Cosmos DB konto.

1. Klicka på Lägg till behållare i meny raden. Ange "Skapa ny databas" och Använd namnet **hotell-rum-DB**. Ange **hotell** för samlings namnet och **/HotelId** för partitionsnyckel. Skapa databasen och behållaren genom att klicka på **OK** .

   ![Lägg till Azure Cosmos DB container](media/tutorial-multiple-data-sources/cosmos-add-container.png "Lägg till en Azure Cosmos DB behållare")

1. Gå till Cosmos DB Datautforskaren och välj elementet **objekt** under behållaren **hotell** i **hotell-rum-DB-** databasen. Klicka sedan på **överför objekt** i kommando fältet.

   ![Överför till Azure Cosmos DB samling](media/tutorial-multiple-data-sources/cosmos-upload.png "Överför till Cosmos DB samling")

1. I överförings panelen klickar du på knappen mapp och navigerar sedan till filen **cosmosdb/HotelsDataSubset_CosmosDb. JSON** i projektmappen. Starta överföringen genom att klicka på **OK** .

   ![Välj fil att ladda upp](media/tutorial-multiple-data-sources/cosmos-upload2.png "Välj fil att ladda upp")

1. Använd knappen Uppdatera för att uppdatera vyn av objekten i Hotels-samlingen. Sju nya databas dokument visas.

## <a name="prepare-sample-blob-data"></a>Förbered BLOB-data för exempel

1. [Logga](https://portal.azure.com)in på Azure Portal, navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) med namnet **hotell-rum** där du kan lagra JSON-filer för hotell rummet. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden.

   ![Skapa en BLOB-behållare](media/tutorial-multiple-data-sources/blob-add-container.png "Skapa en blobcontainer")

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet.

   ![Ladda upp i kommando fältet](media/search-semi-structured-data/upload-command-bar.png "Ladda upp i kommando fältet")

1. Navigera till mappen som innehåller exempelfilerna. Markera alla och klicka sedan på **överför**.

   ![Överföra filer](media/tutorial-multiple-data-sources/blob-upload.png "Överföra filer")

När uppladdningen är klar ska filerna visas i listan för data containern.

## <a name="set-up-connections"></a>Konfigurera anslutningar

Anslutnings information för Sök tjänsten och data källorna anges i filen **appSettings. JSON** i lösningen. 

1. Öppna filen **AzureSearchMultipleDataSources. SLN** i Visual Studio.

1. Redigera filen **appSettings. JSON** i Solution Explorer.  

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

De första två posterna använder URL-adressen och administratörs nycklarna för Azure Kognitiv sökning-tjänsten. En slut punkt av `https://mydemo.search.windows.net`, till exempel det tjänst namn som ska tillhandahållas `mydemo`.

I nästa poster anges konto namn och information om anslutnings strängen för Azure-Blob Storage och Azure Cosmos DB data källor.

### <a name="identify-the-document-key"></a>Identifiera dokument nyckeln

I Azure Kognitiv sökning kan nyckel fältet unikt identifiera varje dokument i indexet. Varje Sök index måste ha exakt ett nyckel fält av typen `Edm.String`. Nyckel fältet måste finnas för varje dokument i en data källa som läggs till i indexet. (I själva verket är det det enda obligatoriska fältet.)

När du indexerar data från flera data källor måste varje nyckel värde för data källan mappas till samma nyckel fält i det kombinerade indexet. Det krävs ofta viss planering för att identifiera en meningsfull dokument nyckel för ditt index och se till att den finns i alla data källor.

Azure Kognitiv sökning-indexerare kan använda fält mappningar för att byta namn på och till och med omformatera data fält under indexerings processen, så att källdata kan dirigeras till rätt index fält.

I vårt exempel Azure Cosmos DB data kallas hotell-ID: t **HotelId**. Men i JSON-BLOB-filerna för hotell rummen heter hotell **-ID: t**. Programmet hanterar detta genom att mappa **ID-** fältet från blobarna till nyckel fältet **HotelId** i indexet.

> [!NOTE]
> I de flesta fall genererar automatiskt dokument nycklar, till exempel de som skapats som standard av vissa indexerare, inte dokument nycklar för kombinerade index. I allmänhet ska du använda ett meningsfullt, unikt nyckel värde som redan finns i eller som enkelt kan läggas till i data källorna.

## <a name="understand-the-code"></a>Förstå koden

När data-och konfigurations inställningarna är på plats bör exempel programmet i **AzureSearchMultipleDataSources. SLN** vara redo att bygga och köra.

Den här C#enkla/.NET-distribution.-konsolen utför följande uppgifter:
* Skapar ett nytt Azure Kognitiv sökning-index baserat på data strukturen i C# hotell klassen (som även hänvisar till adress-och rums klasserna).
* Skapar en Azure Cosmos DB data källa och en indexerare som mappar Azure Cosmos DB data till index fält.
* Kör Azure Cosmos DB indexeraren för att läsa in hotell data.
* Skapar en Azure Blob Storage-datakälla och en indexerare som mappar JSON BLOB-data till index fält.
* Kör Azure Blob Storage-indexeraren för att läsa in data från rummen.

 Innan du kör programmet ska du ta en minut för att undersöka koden och definitionerna index och Indexer för det här exemplet. Den relevanta koden finns i två filer:

  + **Hotel.cs** innehåller det schema som definierar indexet
  + **Program.cs** innehåller funktioner som skapar Azure kognitiv sökning-index, data källor och indexerare och läser in de kombinerade resultaten i indexet.

### <a name="define-the-index"></a>Definiera indexet

Det här exempel programmet använder .NET SDK för att definiera och skapa ett Azure Kognitiv sökning-index. Det drar nytta av klassen [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) för att generera en index struktur från en C# data modell klass.

Data modellen definieras av hotell klassen, som också innehåller referenser till adress-och rums klasserna. FieldBuilder går igenom flera klass definitioner för att generera en komplex data struktur för indexet. Metadata-Taggar används för att definiera attributen för varje fält, till exempel om det är sökbart eller sorterbart.

Följande kodfragment från **Hotel.cs** -filen visar hur ett enskilt fält och en referens till en annan data modell klass kan anges.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

I **program.cs** -filen definieras indexet med ett namn och en fält samling som genereras av metoden `FieldBuilder.BuildForType<Hotel>()` och sedan skapas enligt följande:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Skapa Azure Cosmos DB data källa och indexerare

I huvud programmet ingår logik för att skapa Azure Cosmos DB data källa för hotell data.

Först sammanfogas Azure Cosmos DB databas namnet till anslutnings strängen. Sedan definierar den datakällobjektet, inklusive inställningar som är speciella för Azure Cosmos DB källor, till exempel egenskapen [useChangeDetection].

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

När data källan har skapats konfigurerar programmet en Azure Cosmos DB indexerare med namnet **hotell-rum-Cosmos-Indexer**.

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
Programmet tar bort alla befintliga indexerare med samma namn innan du skapar det nya, om du vill köra det här exemplet mer än en gång.

Det här exemplet definierar ett schema för indexeraren så att det körs en gång per dag. Du kan ta bort egenskapen schema från det här anropet om du inte vill att indexeraren automatiskt ska köras igen i framtiden.

### <a name="index-azure-cosmos-db-data"></a>Index Azure Cosmos DB data

När data källan och indexeraren har skapats är koden som kör indexeraren kort:

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

När Azure Cosmos DB indexeraren har körts innehåller Sök indexet en fullständig uppsättning exempel på hotell-dokument. Fältet rum för varje hotell är dock en tom matris, eftersom Azure Cosmos DB data källan inte innehöll någon rums information. Därefter tar programmet emot från Blob Storage för att läsa in och slå samman rums data.

### <a name="create-blob-storage-data-source-and-indexer"></a>Skapa data källa och indexerare för Blob Storage

För att få information om rummet ställer programmet först in en Blob Storage-datakälla för att referera till en uppsättning enskilda JSON-BLOB-filer.

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

När data källan har skapats konfigurerar programmet en BLOB-indexerare med namnet **hotell-rum-BLOB-Indexer**.

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

JSON-blobbar innehåller ett nyckel fält med namnet **ID** i stället för **HotelId**. Koden använder klassen `FieldMapping` för att instruera indexeraren att dirigera **ID-** fältets värde till dokument nyckeln **HotelId** i indexet.

Blob Storage-indexerare kan använda parametrar som identifierar vilket tolknings läge som ska användas. Tolknings läget skiljer sig för blobbar som representerar ett enda dokument eller flera dokument inom samma blob. I det här exemplet representerar varje BLOB ett enda index dokument, så koden använder parametern `IndexingParameters.ParseJson()`.

Mer information om indexerare som analyserar parametrar för JSON-blobar finns i [index JSON-blobbar](search-howto-index-json-blobs.md). Mer information om hur du anger dessa parametrar med .NET SDK finns i klassen [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

Programmet tar bort alla befintliga indexerare med samma namn innan du skapar det nya, om du vill köra det här exemplet mer än en gång.

Det här exemplet definierar ett schema för indexeraren så att det körs en gång per dag. Du kan ta bort egenskapen schema från det här anropet om du inte vill att indexeraren automatiskt ska köras igen i framtiden.

### <a name="index-blob-data"></a>Indexera BLOB-data

När Blob Storage-datakällan och indexeraren har skapats är koden som kör indexeraren enkel:

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

Eftersom indexet redan har fyllts med hotell data från Azure Cosmos DB databasen, uppdaterar BLOB-indexeraren befintliga dokument i indexet och lägger till rums information.

> [!NOTE]
> Om du har samma icke-nyckel fält i båda data källorna och data i dessa fält inte matchar, kommer indexet att innehålla värdena från den som indexeraren kördes senast. I vårt exempel innehåller båda data källorna ett **HotelName** -fält. Om data i det här fältet skiljer sig åt för dokument med samma nyckel värde, kommer **HotelName** -data från data källan som indexerats senast att vara det värde som lagras i indexet.

## <a name="search-your-json-files"></a>Söka i JSON-filer

Du kan utforska det ifyllda Sök indexet när programmet har körts, med hjälp av [**Sök Utforskaren**](search-explorer.md) i portalen.

I Azure Portal öppnar du **översikts** sidan Sök tjänst och letar upp **hotell-rum-exempel** index i listan **index** .

  ![Lista över Azure Kognitiv sökning-index](media/tutorial-multiple-data-sources/index-list.png "Lista över Azure Kognitiv sökning-index")

Klicka på hotell-rum-exempel index i listan. Ett Sök Utforskaren-gränssnitt visas för indexet. Ange en fråga för en term som "lyxen". Du bör se minst ett dokument i resultatet och det här dokumentet ska innehålla en lista över rums objekt i matrisen för rummen.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa efter en själv studie kurs är att ta bort resurs gruppen som innehåller Azure Kognitiv sökning-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen finns resurs gruppens namn på sidan Översikt i Azure Kognitiv sökning-tjänsten.

## <a name="next-steps"></a>Nästa steg

Det finns flera metoder och flera alternativ för att indexera JSON-blobbar. Om dina källdata innehåller JSON-innehåll kan du granska de här alternativen för att se vad som passar bäst för ditt scenario.

> [!div class="nextstepaction"]
> [Så här indexerar du JSON-blobbar med Azure Kognitiv sökning BLOB-indexeraren](search-howto-index-json-blobs.md)

Du kanske vill utöka strukturerade index data från en data källa med kognitivt fördelade data från ostrukturerade blobbar eller full text innehåll. Följande självstudie visar hur du använder Cognitive Services tillsammans med Azure Kognitiv sökning med hjälp av .NET SDK.

> [!div class="nextstepaction"]
> [Anropa API:er för Cognitive Services i en Azure Kognitiv sökning indexerings pipeline](cognitive-search-tutorial-blob-dotnet.md)
