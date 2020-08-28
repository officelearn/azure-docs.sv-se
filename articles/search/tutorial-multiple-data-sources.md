---
title: C#-självstudie som indexerar flera Azure-datakällor
titleSuffix: Azure Cognitive Search
description: Lär dig hur du importerar data från flera data källor till ett enda Azure Kognitiv sökning-index med hjälp av indexerare. Den här självstudien och exempel koden finns i C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d3dd75d246c1f74253a9ce910e50b05402065464
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998466"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Självstudie: index från flera data källor med hjälp av .NET SDK

Azure Kognitiv sökning kan importera, analysera och indexera data från flera data källor till ett enda konsoliderings Sök index. Detta stöder situationer där strukturerade data sammanställs med mindre strukturerade eller udda text data från andra källor, t. ex. text-, HTML-eller JSON-dokument.

I den här självstudien beskrivs hur du kan indexera hotell data från en Azure Cosmos DB data källa och slå samman med information om hotell rums information från Azure Blob Storage dokument. Resultatet blir ett kombinerat hotell Sök index som innehåller komplexa data typer.

I den här självstudien används C# och [.NET SDK](/dotnet/api/overview/azure/search). I den här självstudien utför du följande uppgifter:

> [!div class="checklist"]
> * Ladda upp exempel data och skapa data källor
> * Identifiera dokument nyckeln
> * Definiera och skapa indexet
> * Indexera hotell data från Azure Cosmos DB
> * Slå samman hotell rums data från Blob Storage

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-files"></a>Hämta filer

Käll koden för den här självstudien finns i GitHub-lagringsplatsen [Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) i mappen med [flera data källor](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) .

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, Azure Cosmos DB för en data uppsättning och Azure Blob Storage för den andra data uppsättningen. 

Skapa om möjligt alla tjänster i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan dina tjänster finnas i vilken region som helst.

I det här exemplet används två små uppsättningar med data som beskriver sju fiktiva hotell. En uppsättning beskriver själva hotellen och kommer att läsas in i en Azure Cosmos DB databas. Den andra uppsättningen innehåller information om hotell rummet och tillhandahålls som sju separata JSON-filer som ska överföras till Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Börja med Cosmos DB

1. Logga in på [Azure Portal](https://portal.azure.com)och navigera sedan till översikts sidan för Azure Cosmos DB konto.

1. Välj **datautforskaren** och välj sedan **ny databas**.

   ![Skapa en ny databas](media/tutorial-multiple-data-sources/cosmos-newdb.png "Skapa en ny databas")

1. Ange namnet **hotell-rum-DB**. Acceptera standardvärden för återstående inställningar.

   ![Konfigurera databas](media/tutorial-multiple-data-sources/cosmos-dbname.png "Konfigurera databas")

1. Skapa en ny behållare. Använd den befintliga databasen som du nyss skapade. Ange **hotell** för behållar namnet och Använd **/HotelId** för partitionsnyckel.

   ![Lägga till containern](media/tutorial-multiple-data-sources/cosmos-add-container.png "Lägga till containern")

1. Välj **objekt** under **hotell**och klicka sedan på **överför objekt** i kommando fältet. Gå till och välj filen **cosmosdb/HotelsDataSubset_CosmosDb.jspå** i projektmappen.

   ![Överför till Azure Cosmos DB samling](media/tutorial-multiple-data-sources/cosmos-upload.png "Överför till Cosmos DB samling")

1. Använd knappen Uppdatera för att uppdatera vyn av objekten i Hotels-samlingen. Sju nya databas dokument visas.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Logga in på [Azure Portal](https://portal.azure.com), navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md) med namnet **hotell-rum** där du kan lagra JSON-filer för hotell rummet. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden.

   ![Skapa en blobcontainer](media/tutorial-multiple-data-sources/blob-add-container.png "Skapa en blobcontainer")

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet. Navigera till mappen som innehåller exempelfilerna. Markera alla och klicka sedan på **överför**.

   ![Överföra filer](media/tutorial-multiple-data-sources/blob-upload.png "Överföra filer")

När uppladdningen är klar ska filerna visas i listan för data containern.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Hämta en Admin API-nyckel och URL för Azure Kognitiv sökning

Om du vill interagera med din Azure Kognitiv sökning-tjänst behöver du tjänst-URL: en och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratör och fråge nycklar](media/search-get-started-nodejs/service-name-and-keys.png)

En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-your-environment"></a>2 – Konfigurera din miljö

1. Starta Visual Studio 2019 och välj **NuGet Package Manager** i **verktyg** -menyn och **Hantera NuGet-paket för lösning...**. 

1. På fliken **Bläddra** letar du reda på och installerar **Microsoft. Azure. search** (version 9.0.1 eller senare). Du måste klicka på ytterligare dialog rutor för att slutföra installationen.

    ![Använda NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Sök efter **Microsoft.Extensions.Configuration.Js** NuGet-paketet och installera det också.

1. Öppna lösnings filen **AzureSearchMultipleDataSources. SLN**.

1. I Solution Explorer redigerar du **appsettings.jspå** filen för att lägga till anslutnings information.  

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

De första två posterna använder URL-adressen och administratörs nycklarna för Azure Kognitiv sökning-tjänsten. En slut punkt `https://mydemo.search.windows.net` , till exempel tjänst namnet som ska tillhandahållas `mydemo` .

I nästa poster anges konto namn och information om anslutnings strängen för Azure-Blob Storage och Azure Cosmos DB data källor.

## <a name="3---map-key-fields"></a>3 – mappa nyckel fält

Sammanfogning av innehåll kräver att båda data strömmarna är riktade mot samma dokument i Sök indexet. 

I Azure Kognitiv sökning kan nyckel fältet unikt identifiera varje dokument. Varje Sök index måste ha exakt ett nyckel fält av typen `Edm.String` . Nyckel fältet måste finnas för varje dokument i en data källa som läggs till i indexet. (I själva verket är det det enda obligatoriska fältet.)

När du indexerar data från flera data källor ser du till att varje inkommande rad eller ett dokument innehåller en gemensam dokument nyckel för att koppla data från två fysiskt distinkta käll dokument till ett nytt sökdokument i det kombinerade indexet. 

Det krävs ofta viss planering för att identifiera en meningsfull dokument nyckel för ditt index och se till att den finns i båda data källorna. I den här demon finns `HotelId` nyckeln för varje hotell i Cosmos DB också i rummen JSON-blobbar i Blob Storage.

Azure Kognitiv sökning-indexerare kan använda fält mappningar för att byta namn på och till och med omformatera data fält under indexerings processen, så att källdata kan dirigeras till rätt index fält. I Cosmos DB anropas exempelvis hotell-ID: t **`HotelId`** . Men i JSON-BLOB-filerna för hotell rummen heter hotell-ID: t **`Id`** . Programmet hanterar detta genom att mappa **`Id`** fältet från blobarna till **`HotelId`** nyckel fältet i indexet.

> [!NOTE]
> I de flesta fall kan automatiskt genererade dokument nycklar, till exempel de som skapats som standard av vissa indexerare, inte göra dokument nycklar för kombinerade index. I allmänhet ska du använda ett meningsfullt, unikt nyckel värde som redan finns i eller som enkelt kan läggas till i data källorna.

## <a name="4---explore-the-code"></a>4 – utforska koden

När data-och konfigurations inställningarna är på plats bör exempel programmet i **AzureSearchMultipleDataSources. SLN** vara redo att bygga och köra.

Den här enkla/.NET-distribution.-konsolen i C# utför följande uppgifter:

* Skapar ett nytt index baserat på data strukturen i C#-klassen (som även hänvisar till adress-och rums klasserna).
* Skapar en ny data källa och en indexerare som mappar Azure Cosmos DB data till index fält. Detta är båda objekten i Azure Kognitiv sökning.
* Kör indexeraren för att läsa in hotell data från Cosmos DB.
* Skapar en andra data källa och en indexerare som mappar JSON-BLOB-data till index fält.
* Kör den andra indexeraren för att läsa in data i rum från Blob Storage.

 Innan du kör programmet ska du ta en minut för att undersöka koden och definitionerna index och Indexer för det här exemplet. Den relevanta koden finns i två filer:

  + **Hotel.cs** innehåller det schema som definierar indexet
  + **Program.cs** innehåller funktioner som skapar Azure kognitiv sökning-index, data källor och indexerare och läser in de kombinerade resultaten i indexet.

### <a name="create-an-index"></a>Skapa ett index

Det här exempel programmet använder .NET SDK för att definiera och skapa ett Azure Kognitiv sökning-index. Det drar nytta av klassen [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) för att generera en index struktur från en C#-data modell klass.

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

I **program.cs** -filen definieras indexet med ett namn och en fält samling som genereras av `FieldBuilder.BuildForType<Hotel>()` metoden, och sedan skapas följande:

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

JSON-blobbar innehåller ett nyckel fält med namnet **`Id`** i stället för **`HotelId`** . Koden använder `FieldMapping` klassen för att instruera indexeraren att dirigera **`Id`** fältvärdet till **`HotelId`** dokument nyckeln i indexet.

Blob Storage-indexerare kan använda parametrar som identifierar vilket tolknings läge som ska användas. Tolknings läget skiljer sig för blobbar som representerar ett enda dokument eller flera dokument inom samma blob. I det här exemplet representerar varje BLOB ett enda index dokument, så koden använder- `IndexingParameters.ParseJson()` parametern.

Mer information om indexerare som analyserar parametrar för JSON-blobar finns i [index JSON-blobbar](search-howto-index-json-blobs.md). Mer information om hur du anger dessa parametrar med .NET SDK finns i klassen [IndexerParametersExtension](/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

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

## <a name="5---search"></a>5-Sök

Du kan utforska det ifyllda Sök indexet när programmet har körts, med hjälp av [**Sök Utforskaren**](search-explorer.md) i portalen.

I Azure Portal öppnar du **översikts** sidan Sök tjänst och letar upp **hotell-rum-exempel** index i listan **index** .

  ![Lista över Azure Kognitiv sökning-index](media/tutorial-multiple-data-sources/index-list.png "Lista över Azure Kognitiv sökning-index")

Klicka på hotell-rum-exempel index i listan. Ett Sök Utforskaren-gränssnitt visas för indexet. Ange en fråga för en term som "lyxen". Du bör se minst ett dokument i resultatet och det här dokumentet ska innehålla en lista över rums objekt i matrisen för rummen.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempel koden för den här självstudien kontrollerar om det finns befintliga objekt och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare och data källor.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med konceptet för att mata in data från flera källor tar vi en närmare titt på indexerings konfigurationen, från och med Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurera en Azure Cosmos DB indexerare](search-howto-index-cosmosdb.md)