---
title: 'Självstudiekurs: C# och AI över Azure-blobbar'
titleSuffix: Azure Cognitive Search
description: Gå igenom ett exempel på textextrahering och bearbetning av naturligt språk över innehåll i Blob-lagring med C# och Azure Cognitive Search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 169a33d12e98235dcb4e4f317dbb8d91eb7446a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851133"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Självstudiekurs: Använd C# och AI för att generera sökbart innehåll från Azure-blobbar

Om du har ostrukturerad text eller avbildningar i Azure Blob-lagring kan en [AI-anrikningspipeline](cognitive-search-concept-intro.md) extrahera information och skapa nytt innehåll som är användbart för fulltextsökning eller kunskapsutvinningsscenarier. I den här C#-självstudien använder du OCR (Optical Character Recognition) på bilder och utför bearbetning av naturligt språk för att skapa nya fält som du kan använda i frågor, aspekter och filter.

Den här självstudien använder C# och [.NET SDK](https://aka.ms/search-sdk) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Börja med programfiler och avbildningar i Azure Blob-lagring.
> * Definiera en pipeline för att lägga till OCR, textextrahering, språkidentifiering, entitet och nyckelfrasigenkänning.
> * Definiera ett index för att lagra utdata (råinnehåll, plus pipelinegenererade namnvärdespar).
> * Kör pipelinen för att starta omvandlingar och analyser och för att skapa och läsa in indexet.
> * Utforska resultat med hjälp av fulltextsökning och en omfattande frågesyntax.

Om du inte har en Azure-prenumeration öppnar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure-lagring](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnadsfria tjänsten för den här självstudien. En kostnadsfri söktjänst begränsar dig till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Innan du börjar, se till att du har utrymme på din tjänst för att acceptera de nya resurserna.

## <a name="download-files"></a>Hämta filer

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Hämta** längst upp till vänster för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **Extrahera alla**. Det finns 14 filer av olika slag. Använd dem alla för den här guiden.

## <a name="1---create-services"></a>1 - Skapa tjänster

Den här självstudien använder Azure Cognitive Search för indexering och frågor, Cognitive Services på backend för AI-anrikning och Azure Blob-lagring för att tillhandahålla data. Den här självstudien förblir under den kostnadsfria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänster du behöver skapa är sökning och lagring.

Om möjligt, skapa både i samma region och resursgrupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i alla regioner.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga in på Azure-portalen](https://portal.azure.com/) och klicka på **+ Skapa resurs**.

1. Sök efter *lagringskonto* och välj Microsofts erbjudande om lagringskonto.

   ![Skapa lagringskonto](media/cognitive-search-tutorial-blob/storage-account.png "Skapa lagringskonto")

1. På fliken Grunderna krävs följande objekt. Acceptera standardvärdena för allt annat.

   + **Resursgrupp**. Välj en befintlig eller skapa en ny, men använd samma grupp för alla tjänster så att du kan hantera dem kollektivt.

   + **Namn på lagringskonto**. Om du tror att du har flera resurser av samma typ använder du namnet för att skilja efter typ och region, till exempel *blobstoragewestus*. 

   + **Plats**. Om möjligt väljer du samma plats som används för Azure Cognitive Search och Cognitive Services. En enda plats annullerar bandbreddsavgifter.

   + **Konto Typ**. Välj standard, *StorageV2 (allmänt ändamål v2)*.

1. Klicka på **Granska + Skapa** om du vill skapa tjänsten.

1. När den har skapats klickar du på **Gå till resursen** för att öppna sidan Översikt.

1. Klicka på **Blobbar-tjänsten.**

1. Klicka på **+ Behållare** om du vill skapa en behållare och namnge den *basic-demo-data-pr*.

1. Välj *basic-demo-data-pr* och klicka sedan på **Ladda upp** för att öppna mappen där du sparade nedladdningsfilerna. Markera alla fjorton filer och klicka på **OK** för att ladda upp.

   ![Ladda upp exempelfiler](media/cognitive-search-quickstart-blob/sample-data.png "Ladda upp exempelfiler")

1. Innan du lämnar Azure Storage får du en anslutningssträng så att du kan formulera en anslutning i Azure Cognitive Search. 

   1. Bläddra tillbaka till översiktssidan för ditt lagringskonto (vi använde *blobstragewestus* som exempel). 
   
   1. I det vänstra navigeringsfönstret väljer du **Access-nycklar** och kopierar en av anslutningssträngarna. 

   Anslutningssträngen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutningssträngen i Anteckningar. Du behöver det senare när du konfigurerar datakällanslutningen.

### <a name="cognitive-services"></a>Cognitive Services

AI-anrikning backas upp av Cognitive Services, inklusive textanalys och datorseende för naturligt språk och bildbehandling. Om ditt mål var att slutföra en verklig prototyp eller projekt, skulle du vid denna punkt tillhandahålla Cognitive Services (i samma region som Azure Cognitive Search) så att du kan koppla den till indexeringsåtgärder.

För den här övningen kan du dock hoppa över resursetablering eftersom Azure Cognitive Search kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnadsfria transaktioner per indexeringskörning. Eftersom den här självstudien använder 7 transaktioner är den kostnadsfria allokeringen tillräcklig. För större projekt bör du planera att etablera Cognitive Services på S0-nivån för betalning per steg. Mer information finns i [Bifoga Kognitiva tjänster](cognitive-search-attach-cognitive-services.md).

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

Börja med att öppna Visual Studio och skapa ett nytt Console App-projekt som kan köras på .NET Core.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

[Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) består av några klientbibliotek som gör att du kan hantera dina index, datakällor, indexerare och skillsets, samt ladda upp och hantera dokument och köra frågor, allt utan att behöva hantera information om HTTP och JSON. Dessa klientbibliotek distribueras alla som NuGet-paket.

För det här projektet installerar du `Microsoft.Azure.Search` version 9 eller senare av NuGet-paketet.

1. Öppna Package Manager-konsolen. Välj **Verktyg** > **NuGet Package Manager** > **Package Manager Console**. 

1. Navigera till [paketsidan för Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Välj den senaste versionen (9 eller senare).

1. Kopiera kommandot Pakethanteraren.

1. Gå tillbaka till Package Manager-konsolen och kör kommandot som du kopierade i föregående steg.

Installera sedan det `Microsoft.Extensions.Configuration.Json` senaste NuGet-paketet.

1. Välj **Verktyg** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning...**. 

1. Klicka på **Bläddra** `Microsoft.Extensions.Configuration.Json` och sök efter NuGet-paketet. 

1. Välj paketet, välj ditt projekt, bekräfta att versionen är den senaste stabila versionen och klicka sedan på **Installera**.

### <a name="add-service-connection-information"></a>Lägga till information om tjänstanslutning

1. Högerklicka på projektet i Lösningsutforskaren och välj **Lägg till** > **nytt objekt...** . 

1. Namnge filen `appsettings.json` och välj **Lägg till**. 

1. Inkludera den här filen i utdatakatalogen.
    1. Högerklicka på `appsettings.json` och välj **Egenskaper**. 
    1. Ändra värdet för **Kopiera till utdatakatalog** till **Kopiera om nyare**.

1. Kopiera nedanstående JSON till din nya JSON-fil.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Lägg till söktjänsten och blob-lagringskontoinformationen. Kom ihåg att du kan hämta den här informationen från de steg för tillhandahållande av tjänster som anges i föregående avsnitt.

### <a name="add-namespaces"></a>Lägga till namnområden

Lägg `Program.cs`till följande namnområden i .

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Skapa en klient

Skapa en förekomst av `SearchServiceClient` klassen under `Main`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`skapar en `SearchServiceClient` ny med hjälp av värden som lagras i programmets config-fil (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> `SearchServiceClient`-klassen hanterar anslutningar till din söktjänst. För att undvika att behöva öppna för många anslutningar bör du försöka dela en enskild instans av `SearchServiceClient` i ditt program om möjligt. Dess metoder är trådsäkra för att möjliggöra den typen av delning.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Lägg till funktion för att avsluta programmet under fel

Den här självstudien är avsedd att hjälpa dig att förstå varje steg i indexeringspipelinen. Om det finns ett kritiskt problem som hindrar programmet från att skapa datakällan, kompetensen, index eller indexeraren kommer programmet att mata ut felmeddelandet och avsluta så att problemet kan förstås och åtgärdas.

Lägg `ExitProgram` `Main` till för att hantera scenarier som kräver att programmet avslutas.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - Skapa pipelinen

I Azure Cognitive Search sker AI-bearbetning under indexering (eller datainmatning). Den här delen av genomgången skapar fyra objekt: datakälla, indexdefinition, kompetens, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

`SearchServiceClient` har en `DataSources`-egenskap. Den här egenskapen innehåller alla metoder du behöver för att skapa, lista, uppdatera eller ta bort Azure Cognitive Search-datakällor.

Skapa en `DataSource` ny `serviceClient.DataSources.CreateOrUpdate(dataSource)`instans genom att anropa . `DataSource.AzureBlobStorage`kräver att du anger datakällnamn, anslutningssträng och blob-behållarnamn.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

För en lyckad begäran returnerar metoden datakällan som skapades. Om det finns ett problem med begäran, till exempel en ogiltig parameter, kommer metoden att utlösa ett undantag.

Lägg nu till `Main` en `CreateOrUpdateDataSource` rad för att anropa den funktion som du just har lagt till.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Skapa och kör lösningen. Eftersom det här är din första begäran kontrollerar du Azure-portalen för att bekräfta att datakällan skapades i Azure Cognitive Search. På söktjänstens instrumentpanelsida verifierar du att panelen Datakällor har ett nytt objekt. Du kan behöva vänta några minuter medan portalsidan uppdateras.

  ![Panelen Datakällor i portalen](./media/cognitive-search-tutorial-blob/data-source-tile.png "Panelen Datakällor i portalen")

### <a name="step-2-create-a-skillset"></a>Steg 2: Skapa en kompetens

I det här avsnittet definierar du en uppsättning anrikningssteg som du vill använda för dina data. Varje berikningssteg kallas en *färdighet* och uppsättningen av berikning steg en *kompetens*. Den här självstudien använder [inbyggda kognitiva färdigheter](cognitive-search-predefined-skills.md) för kompetensen:

+ [Optisk teckenigenkänning](cognitive-search-skill-ocr.md) om du vill känna igen utskriven och handskriven text i bildfiler.

+ [Textsammanslagning](cognitive-search-skill-textmerger.md) för att konsolidera text från en samling fält till ett enda fält.

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Text Split](cognitive-search-skill-textsplit.md) för att dela upp stort innehåll i mindre segment innan du anropar nyckelfrasen extrahering skicklighet och entitet erkännande skicklighet. Extraktion av nyckelfraser och entitetsigenkänning accepterar indata på högst 50 000 tecken. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Entitetsigenkänning](cognitive-search-skill-entity-recognition.md) för att extrahera namnen på organisationer från innehåll i blob-behållaren.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna.

Under den första bearbetningen knäcker Azure Cognitive Search varje dokument för att läsa innehåll från olika filformat. Text som hittas från källfilen placeras i ett genererat ```content```-fält, ett för varje dokument. Ange därför indata ```"/document/content"``` som för att använda den här texten. 

Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-skicklighet

**OCR-färdigheten** extraherar text från bilder. Den här färdigheten förutsätter att det finns ett normalized_images fält. Om du vill generera det här fältet ```"imageAction"``` ställer vi senare i ```"generateNormalizedImages"```självstudien in konfigurationen i indexeringsdefinitionen till .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Slå samman färdighet

I det här avsnittet **Merge** ska du skapa en sammanslagningsfärdighet som sammanfogar dokumentinnehållsfältet med texten som producerades av OCR-färdigheten.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Språkidentifieringsfärdighet

**Språkidentifieringsfärdigheten** identifierar språket i indatatexten och rapporterar en enda språkkod för varje dokument som skickas på begäran. Vi använder utdata från **språkidentifieringsfärdigheten** som en del av indata till **textdelningsfärdigheten.**

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Text delad färdighet

Under **Split-färdigheten** delas text med sidorna och sidlängden begränsas till 4 000 tecken mätt med `String.Length`. Algoritmen kommer att försöka dela upp texten `maximumPageLength` i segment som är som mest i storlek. I det här fallet gör algoritmen sitt bästa för att bryta meningen på en meningsgräns, så storleken på segmentet kan vara något mindre än `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Färdighet för entitetsigenkän

Den `EntityRecognitionSkill` här instansen är `organization`inställd på att identifiera kategorityp . **Entitetsigenkänningsfärdigheten** kan också känna igen kategorityper `person` och `location`.

Observera att fältet "kontext" ```"/document/pages/*"``` är inställt på med en asterisk, vilket ```"/document/pages"```innebär att anrikningssteget anropas för varje sida under .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Nyckelfrasutsering skicklighet

Precis `EntityRecognitionSkill` som den instans som just skapades anropas **nyckelfrasutextraktionsfärdigheten** för varje sida i dokumentet.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Bygga och skapa kompetensen

Skapa `Skillset` med hjälp av de kunskaper du skapat.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Lägg till följande `Main`rader i .

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Steg 3: Skapa ett index

I det här avsnittet definierar du indexschemat genom att ange vilka fält som ska ingå i det sökbara indexet och sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | `id`       | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Skapa demoindex-klass

Fälten för det här indexet definieras med hjälp av en modellklass. Varje egenskap i modellklassen har attribut som avgör motsvarande indexfälts sökrelaterade beteende. 

Vi lägger till modellklassen i en ny C#-fil. Högerklicka på projektet och välj **Lägg till** > **nytt objekt...**, `DemoIndex.cs`välj "Klass" och namnge filen och välj sedan **Lägg till**.

Se till att ange att du `Microsoft.Azure.Search` vill `Microsoft.Azure.Search.Models` använda typer från och namnområden.

Lägg till nedanstående `DemoIndex.cs` modellklassdefinition i och inkludera den i samma namnområde där du ska skapa indexet.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
``` -->

Nu när du har definierat en `Program.cs` modellklass kan du ganska enkelt skapa en indexdefinition igen när du har definierat en modellklass. Namnet på det här `demoindex`indexet kommer att vara . Om det redan finns ett index med det namnet tas det bort.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Under testningen kanske du upptäcker att du försöker skapa indexet mer än en gång. På grund av detta kontrollerar du om det index som du ska skapa redan finns innan du försöker skapa det.

Lägg till följande `Main`rader i .

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```
 -->

Mer information om hur du definierar ett index finns i [Skapa index (AZURE Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: Skapa och kör en indexerare

Hittills har du skapat en datakälla, en kunskapsuppsättning och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. Om du vill sammanfoga dem i en indexerare måste du definiera fältmappningar.

+ FältetMappningar bearbetas före kompetensuppsättningen, mappningskällorfält från datakällan till målfälten i ett index. Om fältnamn och typer är desamma i båda ändar krävs ingen mappning.

+ UtdataFieldMappings bearbetas efter kompetensuppsättningen, refererar till sourceFieldNames som inte finns förrän dokumentsprickor eller anrikning skapar dem. TargetFieldName är ett fält i ett index.

Förutom att koppla indata till utdata kan du också använda fältmappningar för att förenkla datastrukturer. Mer information finns i [Så här mappar du utökade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Lägg till följande `Main`rader i .

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Räkna med att skapa indexeraren kommer att ta lite tid att slutföra. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. Vissa kunskaper, som bildanalys, är tidskrävande.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet.

### <a name="explore-creating-the-indexer"></a>Utforska skapa indexeraren

Koden anger ```"maxFailedItems"``` till -1, vilket instruerar indexeringsmotorn att ignorera fel vid dataimport. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Också märker ```"dataToExtract"``` är ```"contentAndMetadata"```inställd på . Den här instruktionen anger att indexeraren automatiskt ska extrahera innehållet från olika filformat samt metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. Uppsättningen ```"imageAction"``` till ```"generateNormalizedImages"``` konfiguration, i kombination med OCR-färdighet och textsammanfogningsfärdighet, talar om för indexeraren att extrahera text från bilderna (till exempel ordet "stopp" från en trafikstoppskylt) och bädda in det som en del av innehållsfältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Bildskärmsindexering

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Om du vill ta reda på om `GetStatus` indexeraren fortfarande körs använder du metoden.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo`representerar en indexerares aktuella status och körningshistorik.

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga (till exempel inga textindata från JPEG-filerna).

Lägg till följande `Main`rader i .

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Sök

När indexeringen är klar kan du köra frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Cognitive Search de 50 bästa resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Instruktioner finns [i Så här sidresultat i Azure Cognitive Search](search-pagination-page-layout.md).

Som ett verifieringssteg ska du fråga indexet för alla fält.

Lägg till följande `Main`rader i .

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient`skapar en `SearchIndexClient` ny med hjälp av värden som lagras i programmets config-fil (appsettings.json). Observera att API-nyckeln för söktjänstfrågor används och inte administratörsnyckeln.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Lägg till följande `Main`kod i . Den första try-catch returnerar indexdefinitionen, med namn, typ och attribut för varje fält. Den andra är en parameteriserad fråga, där `Select` anger vilka fält `organizations`som ska inkluderas i resultaten, till exempel . En söksträng `"*"` med returnerar allt innehåll i ett enskilt fält.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Upprepa för ytterligare fält: innehåll, språkKod, keyPhrases och organisationer i den här övningen. Du kan returnera flera fält via egenskapen [Välj](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) med hjälp av en kommaavgränsad lista.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella utvecklingsstadierna är den mest praktiska metoden för designiteration att ta bort objekten från Azure Cognitive Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempelkoden för den här självstudien söker efter befintliga objekt och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare, datakällor och skillsets.

## <a name="takeaways"></a>Lärdomar

Den här självstudien visade de grundläggande stegen för att skapa en berikad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskaper, index och indexerare.

[Inbyggda färdigheter](cognitive-search-predefined-skills.md) infördes, tillsammans med skillset definition och mekanik kedja färdigheter tillsammans genom ingångar och utgångar. Du lärde dig `outputFieldMappings` också att i indexeringsdefinitionen krävs för routning av utökade värden från pipelinen till ett sökbart index på en Azure Cognitive Search-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. Du har också lärt dig att kontrollera indexerarstatus, och vilka objekt du ska ta bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken Alla resurser eller Resursgrupper i fönstret för vänsternavigering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en AI-anrikningspipeline, låt oss ta en närmare titt på kompetensdefinitioner och individuella färdigheter.

> [!div class="nextstepaction"]
> [Hur man skapar en kompetens](cognitive-search-defining-skillset.md)
