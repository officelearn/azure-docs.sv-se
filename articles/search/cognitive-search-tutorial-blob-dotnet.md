---
title: C#-självstudie med AI på Azure-blobbar
titleSuffix: Azure Cognitive Search
description: Stega genom ett exempel på text extrahering och naturlig språk bearbetning över innehåll i blob-lagring med C# och Azure Kognitiv sökning .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 57cb68726adf8818f9ef0c8804be9c388ea39ff5
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872289"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Självstudie: AI-genererat sökbart innehåll från Azure-blobbar med .NET SDK

Om du har ostrukturerad text eller avbildningar i Azure Blob Storage kan en [AI-pipeline](cognitive-search-concept-intro.md) utvinna information och skapa nytt innehåll som är användbart för full texts ökning eller kunskaps utvinnings scenarier. I den här C#-självstudien använder du OCR (optisk tecken läsning) på bilder och utför bearbetning av naturligt språk för att skapa nya fält som du kan använda i frågor, ansikts och filter.

I den här självstudien används C# och [.NET SDK](https://aka.ms/search-sdk) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Börja med programfiler och avbildningar i Azure Blob Storage.
> * Definiera en pipeline för att lägga till OCR, text extrahering, språk identifiering, enhets-och nyckel fras igenkänning.
> * Definiera ett index för att lagra utdata (RAW-innehåll, plus pipeline-genererade namn-värdepar).
> * Kör pipelinen för att starta omvandlingar och analys och för att skapa och läsa in indexet.
> * Utforska resultat med fullständig texts ökning och en omfattande frågesyntax.

Om du inte har någon Azure-prenumeration kan du öppna ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-files"></a>Hämta filer

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Ladda ned** i det övre vänstra hörnet för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **extrahera alla**. Det finns 14 filer av olika typer. Du använder 7 för den här övningen.

Du kan också ladda ned käll koden för den här självstudien. Käll koden finns i självstudien – mappen AI-anrikning i [Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) -lagringsplatsen.

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, Cognitive Services på Server delen för AI-anrikning och Azure Blob Storage för att tillhandahålla data. Den här självstudien finns kvar under den kostnads fria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänsterna du behöver skapa är Sök och lagring.

Skapa om möjligt både i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i vilken region som helst.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga](https://portal.azure.com/) in på Azure Portal och klicka på **+ skapa resurs**.

1. Sök efter *lagrings konto* och välj Microsofts erbjudande för lagrings konto.

   ![Skapa lagrings konto](media/cognitive-search-tutorial-blob/storage-account.png "Skapa lagrings konto")

1. På fliken grundläggande måste följande objekt vara obligatoriska. Acceptera standardvärdena för allt annat.

   + **Resurs grupp**. Välj en befintlig eller skapa en ny, men Använd samma grupp för alla tjänster så att du kan hantera dem tillsammans.

   + **Lagrings konto namn**. Om du tror att du kan ha flera resurser av samma typ, använder du namnet på disambiguate efter typ och region, till exempel *blobstoragewestus*. 

   + **Plats**. Om möjligt väljer du samma plats som används för Azure Kognitiv sökning och Cognitive Services. Med en enda plats annulleras bandbredds avgifter.

   + **Typ av konto**. Välj standard, *StorageV2 (generell användning v2)*.

1. Klicka på **Granska + skapa** för att skapa tjänsten.

1. När den har skapats klickar **du på gå till resursen** för att öppna översikts sidan.

1. Klicka på **blobs** -tjänsten.

1. Klicka på **+ container** för att skapa en behållare och ge den namnet *kugg hjuls-search-demo*.

1. Välj *kugg hjuls-search-demo* och klicka sedan på **Ladda upp** för att öppna mappen där du sparade nedladdnings filerna. Välj alla fjorton filer och klicka på **OK** för att ladda upp.

   ![Ladda upp exempelfiler](media/cognitive-search-quickstart-blob/sample-data.png "Ladda upp exempelfiler")

1. Innan du lämnar Azure Storage får du en anslutnings sträng så att du kan formulera en anslutning i Azure Kognitiv sökning. 

   1. Gå tillbaka till sidan Översikt för ditt lagrings konto (vi använde *blobstoragewestus* som exempel). 
   
   1. I det vänstra navigerings fönstret väljer du **åtkomst nycklar** och kopierar en av anslutnings strängarna. 

   Anslutnings strängen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutnings strängen i anteckningar. Du behöver det senare när du konfigurerar anslutningen till data källan.

### <a name="cognitive-services"></a>Cognitive Services

AI-berikning backas upp av Cognitive Services, inklusive Textanalys och Visuellt innehåll för naturligt språk och bild bearbetning. Om målet var att slutföra en faktisk prototyp eller ett projekt, skulle du i den här punkten etablera Cognitive Services (i samma region som Azure Kognitiv sökning) så att du kan koppla den till indexerings åtgärder.

I den här övningen kan du hoppa över resurs etableringen eftersom Azure Kognitiv sökning kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnads fria transaktioner per indexerare. Eftersom den här självstudien använder 14 transaktioner är den kostnads fria fördelningen tillräckligt. För större projekt bör du planera för etablering Cognitive Services på S0-nivån betala per användning. Mer information finns i [bifoga Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Hämta en Admin API-nyckel och URL för Azure Kognitiv sökning

Om du vill interagera med din Azure Kognitiv sökning-tjänst behöver du tjänst-URL: en och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar** > **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratör och fråge nycklar](media/search-get-started-nodejs/service-name-and-keys.png)

En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-your-environment"></a>2 – Konfigurera din miljö

Börja med att öppna Visual Studio och skapa ett nytt konsol program som kan köras på .NET Core.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

[Azure kognitiv sökning .NET SDK](https://aka.ms/search-sdk) består av ett par klient bibliotek som gör att du kan hantera dina index, data källor, indexerare och färdighetsuppsättningar, samt överföra och hantera dokument och köra frågor, allt utan att du behöver hantera informationen om http och JSON. Dessa klient bibliotek är alla distribuerade som NuGet-paket.

För det här projektet installerar du `Microsoft.Azure.Search` version 9 eller senare av NuGet-paketet.

1. I en webbläsare går du till [sidan Microsoft. Azure. search NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Välj den senaste versionen (9 eller senare).

1. Kopiera Package Manager-kommandot.

1. Öppna Package Manager-konsolen. Välj **verktyg** > **NuGet Package Manager** > **Package**Manager-konsolen. 

1. Klistra in och kör kommandot som du kopierade i föregående steg.

Installera sedan det senaste `Microsoft.Extensions.Configuration.Json` NuGet-paketet.

1. Välj **verktyg** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning.**.. 

1. Klicka på **Bläddra** och Sök efter `Microsoft.Extensions.Configuration.Json` NuGet-paketet. 

1. Välj paketet, Välj ditt projekt, bekräfta att versionen är den senaste stabila versionen och klicka sedan på **Installera**.

### <a name="add-service-connection-information"></a>Lägg till information om tjänst anslutning

1. Högerklicka på ditt projekt i Solution Explorer och välj **Lägg till** > **nytt objekt.** ... 

1. Ge filen `appsettings.json` ett namn och välj **Lägg till**. 

1. Ta med den här filen i din utmatnings katalog.
    1. Högerklicka på `appsettings.json` och välj **Egenskaper**. 
    1. Ändra värdet för **Kopiera till utdatakatalogen** för att **Kopiera om**det är nyare.

1. Kopiera nedanstående JSON till din nya JSON-fil.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
Lägg till din Sök tjänst och information om Blob Storage-kontot. Kom ihåg att du kan hämta den här informationen från tjänst etablerings stegen som anges i föregående avsnitt.

För **SearchServiceName**anger du det korta tjänst namnet och inte den fullständiga URL: en.

### <a name="add-namespaces"></a>Lägg till namn områden

I `Program.cs`lägger du till följande namn rymder.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Skapa en klient

Skapa en instans av `SearchServiceClient` klassen under. `Main`

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`skapar en ny `SearchServiceClient` med värden som lagras i programmets konfigurations fil (appSettings. JSON).

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

### <a name="add-function-to-exit-the-program-during-failure"></a>Lägg till funktion för att avsluta programmet under ett haveri läge

Den här självstudien är avsedd att hjälpa dig att förstå varje steg i indexerings pipelinen. Om det finns ett allvarligt problem som hindrar programmet från att skapa data källan, färdigheter, index eller indexerare, kommer programmet att generera fel meddelandet och avsluta så att problemet kan tolkas och åtgärdas.

Lägg `ExitProgram` till `Main` i för att hantera scenarier som kräver att programmet avslutas.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 – skapa pipelinen

I Azure Kognitiv sökning sker AI-bearbetning under indexering (eller data inmatning). I den här delen av genom gången skapas fyra objekt: data källa, index definition, färdigheter, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

`SearchServiceClient` har en `DataSources`-egenskap. Den här egenskapen innehåller alla metoder som du behöver för att skapa, Visa, uppdatera eller ta bort Azure Kognitiv sökning data källor.

Skapa en ny `DataSource` instans genom att `serviceClient.DataSources.CreateOrUpdate(dataSource)`anropa. `DataSource.AzureBlobStorage`kräver att du anger namnet på data källan, anslutnings strängen och namnet på BLOB-behållaren.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
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

För att begäran ska lyckas returnerar metoden den data källa som skapades. Om det uppstår ett problem med begäran, till exempel en ogiltig parameter, kommer metoden att utlösa ett undantag.

Nu kan du lägga till `Main` en rad i `CreateOrUpdateDataSource` för att anropa den funktion som du just har lagt till.

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

Skapa och kör lösningen. Eftersom det här är din första förfrågan kontrollerar du Azure Portal för att bekräfta att data källan har skapats i Azure Kognitiv sökning. På söktjänstens instrumentpanelsida verifierar du att panelen Datakällor har ett nytt objekt. Du kan behöva vänta några minuter medan portalsidan uppdateras.

  ![Panelen data källor i portalen](./media/cognitive-search-tutorial-blob/data-source-tile.png "Panelen data källor i portalen")

### <a name="step-2-create-a-skillset"></a>Steg 2: skapa en färdigheter

I det här avsnittet definierar du en uppsättning med anriknings steg som du vill använda för dina data. Varje anriknings steg kallas för en *färdighet* och en uppsättning av anriknings steg en *färdigheter*. I den här självstudien används [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) för färdigheter:

+ [Optisk tecken läsning](cognitive-search-skill-ocr.md) för att identifiera skriven och handskriven text i bildfiler.

+ [Text sammanslagning](cognitive-search-skill-textmerger.md) för att konsolidera text från en samling fält till ett enda fält.

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Text delning](cognitive-search-skill-textsplit.md) för att dela upp stor mängd innehåll i mindre segment innan du anropar nyckel frasens extraherings kunskap och enhets igenkännings kompetensen. Extrahering av nyckel fraser och entitets igenkänning accepterar indata på högst 50 000 tecken. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Enhets igenkänning](cognitive-search-skill-entity-recognition.md) för extrahering av namn på organisationer från innehåll i BLOB-behållaren.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna.

Vid den första bearbetningen knäckar Azure Kognitiv sökning varje dokument för att läsa innehåll från olika fil format. Text som hittas från källfilen placeras i ett genererat ```content```-fält, ett för varje dokument. Därför måste du ange inmatade ```"/document/content"``` värden som Använd den här texten. 

Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-kunskaper

**OCR** -kompetensen extraherar text från bilder. Den här kunskapen förutsätter att det finns ett normalized_images fält. Om du vill generera det här fältet senare i självstudien ställer ```"imageAction"``` vi in konfigurationen i indexerings definitionen ```"generateNormalizedImages"```till.

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

### <a name="merge-skill"></a>Sammanfoga kunskaper

I det här avsnittet ska du skapa en **sammanfogad** färdighet som sammanfogar dokumentets innehålls fält med den text som har producerats av OCR-kunskaper.

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

### <a name="language-detection-skill"></a>Kunskap om språk identifiering

**Språkidentifiering** -kunskapen identifierar språket i inmatad text och rapporterar en enda språkkod för varje dokument som skickas på begäran. Vi ska använda utdata från **språkidentifiering** -kompetensen som en del av indata för **text delnings** färdigheten.

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

### <a name="text-split-skill"></a>Text delnings kunskaper

Den **delade** kunskapen nedan delar upp text efter sidor och begränsar sid längden till 4 000 tecken som mäts av `String.Length`. Algoritmen försöker dela upp texten i segment som har störst `maximumPageLength` storlek. I det här fallet gör algoritmen det bästa sättet att dela upp meningen på en mening, så storleken på segmentet kan vara något mindre än `maximumPageLength`.

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

### <a name="entity-recognition-skill"></a>Kompetens för enhets igenkänning

Den `EntityRecognitionSkill` här instansen är inställd på `organization`att identifiera kategori typ. **Entitetens igenkännings** förmåga kan också identifiera kategori `person` typer `location`och.

Observera att fältet "context" är inställt ```"/document/pages/*"``` på med en asterisk, vilket innebär att ett anriknings steg anropas för varje ```"/document/pages"```sida under.

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

### <a name="key-phrase-extraction-skill"></a>Extraherings färdighet för nyckel fraser

Precis som `EntityRecognitionSkill` den instans som precis skapades, anropas **extrahering av diskussionsämne** -kompetens för varje sida i dokumentet.

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

### <a name="build-and-create-the-skillset"></a>Skapa och skapa färdigheter

Skapa `Skillset` med hjälp av de kunskaper du har skapat.

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

Lägg till följande rader i `Main`.

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

### <a name="step-3-create-an-index"></a>Steg 3: skapa ett index

I det här avsnittet definierar du indexschemat genom att ange vilka fält som ska ingå i det sökbara indexet och sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | `id`       | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Skapa DemoIndex-klass

Fält för det här indexet definieras med en modell klass. Varje egenskap i modellklassen har attribut som avgör motsvarande indexfälts sökrelaterade beteende. 

Vi ska lägga till modell klassen i en ny C#-fil. Högerklicka på projektet och välj **Lägg till** > **nytt objekt...**, välj "klass" och ge filen `DemoIndex.cs`ett namn och välj sedan **Lägg till**.

Se till att ange att du vill använda typer från namn områdena `Microsoft.Azure.Search` och `Microsoft.Azure.Search.Models` .

Lägg till modell klass definitionen nedan `DemoIndex.cs` och inkludera den i samma namn område där du skapar indexet.

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

Nu när du har definierat en modell klass kan du skapa `Program.cs` en index definition på ett ganska enkelt sätt i igen. Namnet på det här indexet kommer `demoindex`att vara. Om det redan finns ett index med det namnet tas det bort.

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

Under testningen kanske du upptäcker att du försöker skapa indexet mer än en gång. Därför bör du kontrol lera om det index som du håller på att skapa redan finns innan du försöker skapa det.

Lägg till följande rader i `Main`.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Lägg till följande using-instruktion för att lösa disambiguate-referensen.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Mer information om hur du definierar ett index finns i [skapa index (Azure Kognitiv sökning REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: skapa och köra en indexerare

Hittills har du skapat en datakälla, en kunskapsuppsättning och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. Om du vill sammanfoga dem i en indexerare måste du definiera fältmappningar.

+ FieldMappings bearbetas före färdigheter och mappar käll fälten från data källan till mål fälten i ett index. Om fält namn och typer är desamma i båda ändar, krävs ingen mappning.

+ OutputFieldMappings bearbetas efter färdigheter, refererar till sourceFieldNames som inte finns förrän dokument sprickor eller berikning skapar dem. TargetFieldName är ett fält i ett index.

Förutom att koppla upp indata till utdata kan du också använda fält mappningar för att förenkla data strukturer. Mer information finns i [så här mappar du berikade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

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
Lägg till följande rader i `Main`.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Det kan ta en stund att skapa indexeraren. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. Vissa kunskaper, som bildanalys, är tidskrävande.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet.

### <a name="explore-creating-the-indexer"></a>Utforska hur du skapar indexeraren

Kod uppsättningen ```"maxFailedItems"``` till-1, som instruerar indexerings motorn att ignorera fel vid data import. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också att ```"dataToExtract"``` är inställt på ```"contentAndMetadata"```. Den här instruktionen anger att indexeraren automatiskt ska extrahera innehållet från olika filformat samt metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen ```"imageAction"``` är inställd ```"generateNormalizedImages"``` på konfiguration, kombinerat med OCR-kunskaper och text kopplings kunskap, och anger att indexeraren ska extrahera text från bilderna (till exempel ordet "Stop" från ett trafik stopp) och bädda in det som en del av innehålls fältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – övervaka indexering

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Använd `GetStatus` metoden för att ta reda på om indexeraren fortfarande körs.

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

`IndexerExecutionInfo`visar aktuell status och körnings historik för en indexerare.

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga (till exempel inga textindata från JPEG-filerna).

Lägg till följande rader i `Main`.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5-Sök

När indexeringen är färdig kan du köra frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Kognitiv sökning de översta 50 resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Anvisningar finns i [så här visar du sid resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

Som ett verifieringssteg ska du fråga indexet för alla fält.

Lägg till följande rader i `Main`.

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

`CreateSearchIndexClient`skapar en ny `SearchIndexClient` med värden som lagras i programmets konfigurations fil (appSettings. JSON). Observera att API-nyckeln för search service-frågan används och inte administratörs nyckeln.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Lägg till följande kod i `Main`. Den första try-catch returnerar index definitionen med namn, typ och attribut för varje fält. Den andra är en parametriserad fråga där `Select` anger vilka fält som ska inkluderas i resultatet, till exempel. `organizations` En Sök sträng med `"*"` returnerar alla innehåll i ett enda fält.

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

Upprepa för ytterligare fält: innehåll, languageCode, diskussions fraser och organisationer i den här övningen. Du kan returnera flera fält via [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) -egenskapen med hjälp av en kommaavgränsad lista.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempel koden för den här självstudien kontrollerar om det finns befintliga objekt och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare, data källor och färdighetsuppsättningar.

## <a name="takeaways"></a>Lärdomar

Den här självstudien demonstrerade de grundläggande stegen för att skapa en omfattande indexerings pipeline genom att skapa komponent delar: en data källa, färdigheter, index och indexerare.

[Inbyggda kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med färdigheter-definitionen och Mechanics för länkning av färdigheter tillsammans genom indata och utdata. Du har också lärt `outputFieldMappings` dig att i index definitions definitionen krävs för att dirigera berikade värden från pipelinen till ett sökbart index i en Azure kognitiv sökning-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. Du har också lärt dig att kontrollera indexerarstatus, och vilka objekt du ska ta bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en pipeline för AI-anrikning, tar vi en närmare titt på färdigheter-definitioner och enskilda kunskaper.

> [!div class="nextstepaction"]
> [Så här skapar du en färdigheter](cognitive-search-defining-skillset.md)
