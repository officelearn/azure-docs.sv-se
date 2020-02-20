---
title: 'Självstudie: skapa en färdigheter i C# med .net'
titleSuffix: Azure Cognitive Search
description: Steg till exempel kod som visar data extrahering, naturligt språk och bild-AI-bearbetning i en Azure Kognitiv sökning anriknings indexerings pipeline.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efd4a9333b5fb02c18b2f6a6d0f8ce58bfb8f220
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472391"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-c-and-the-net-sdk"></a>Självstudie: skapa en pipeline för AI-anrikning med hjälp C# av och .NET SDK

I den här självstudien får du lära dig Mechanics programmerings data i Azure Kognitiv sökning med *kognitiva kunskaper*. Färdigheter backas upp av NLP (Natural Language Processing) och bild analys funktioner i Cognitive Services. Genom färdigheter komposition och konfiguration kan du extrahera text-och text representationer av en bild eller skannad dokument fil. Du kan också identifiera språk, entiteter, nyckel fraser och mycket annat. Slut resultatet är omfattande ytterligare innehåll i ett sökindex som skapats av en AI-baserad indexerings pipeline.

I den här självstudien använder du .NET SDK för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en indexeringspipeline som berikar källdata på väg till ett index
> * Använd inbyggda kunskaper: optisk tecken läsning, text sammanslagning, språk identifiering, text delning, enhets igenkänning, extrahering av nyckel fraser
> * Lära dig att sammanlänka kunskaper genom att mappa indata till utdata i en kompetens
> * Köra begäranden och granska resultatet
> * Återställa index och indexerare för ytterligare utveckling

Output är ett fullständigt text sökbart index i Azure Kognitiv sökning. Du kan förbättra indexet med andra standardfunktioner som [synonymer](search-synonyms.md), [bedömningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analysverktyg](search-analyzers.md) och [filter](search-filters.md).

Den här självstudien körs på den kostnads fria tjänsten, men antalet kostnads fria transaktioner är begränsat till 20 dokument per dag. Om du vill köra den här kursen mer än en gång på samma dag tar du bort indexeraren för att återställa räknaren.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du koppla en fakturerbar Cognitive Services-resurs. Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/) användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://go.microsoft.com/fwlink/?linkid=2042400).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data används i den här självstudien. 

+ [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data. Kontrol lera att lagrings kontot finns i samma region som Azure Kognitiv sökning.

+ [Exempel data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) består av en liten fil uppsättning av olika typer. 

+ [Installera Visual Studio](https://visualstudio.microsoft.com/) för att använda som IDE.

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

Om du vill interagera med din Azure Kognitiv sökning-tjänst behöver du tjänst-URL: en och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**får du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-data"></a>Förbereda exempel data

Berikningspipelinen hämtar data från Azure-datakällor. Källdata måste härstamma från en typ av data källa som stöds för en [Azure kognitiv sökning-indexerare](search-indexer-overview.md). I den här övningen använder vi blogglagring för att demonstrera flera typer av innehåll.

1. [Logga](https://portal.azure.com)in på Azure Portal, navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempel data. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden. Den här självstudien förutsätter att behållar namnet är "Basic-demo-data-PR".

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet för att ladda upp [exempel data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Källfiler i Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. När exempelfilerna har lästs in hämtar du containerns namn och en anslutningssträng för Blob Storage. Du kan göra det genom att navigera till ditt lagrings konto i Azure Portal, välja **åtkomst nycklar**och sedan kopiera fältet **anslutnings sträng** .

   Anslutningssträngen ska vara en URL som ser ut ungefär så här:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Det finns andra sätt att ange anslutningssträngen, till exempel att ange en signatur för delad åtkomst. Om du vill veta mer om autentiseringsuppgifter för datakällor kan du läsa [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Indexera Azure Blob Storage).

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja med att öppna Visual Studio och skapa ett nytt konsol program som kan köras på .NET Core.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

[Azure kognitiv sökning .NET SDK](https://aka.ms/search-sdk) består av ett par klient bibliotek som gör att du kan hantera dina index, data källor, indexerare och färdighetsuppsättningar, samt överföra och hantera dokument och köra frågor, allt utan att du behöver hantera informationen om http och JSON. Dessa klient bibliotek är alla distribuerade som NuGet-paket.

För det här projektet måste du installera version 9 av `Microsoft.Azure.Search` NuGet-paketet och det senaste `Microsoft.Extensions.Configuration.Json` NuGet-paketet.

Installera `Microsoft.Azure.Search` NuGet-paketet med hjälp av Package Manager-konsolen i Visual Studio. Öppna Package Manager-konsolen genom att välja **verktyg** > **NuGet Package Manager** > **Package Manager-konsolen**. För att få kommandot att köras går du till [sidan Microsoft. Azure. search NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Search), väljer version 9 och kopierar Package Manager-kommandot. Kör det här kommandot i Package Manager-konsolen.

Om du vill installera `Microsoft.Extensions.Configuration.Json` NuGet-paketet i Visual Studio väljer du **verktyg** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning...** . Välj Bläddra och Sök efter `Microsoft.Extensions.Configuration.Json` NuGet-paketet. När du har hittat det väljer du paketet, väljer ditt projekt, bekräftar att versionen är den senaste stabila versionen och väljer sedan installera.

## <a name="add-azure-cognitive-search-service-information"></a>Lägg till information om Azure Kognitiv sökning-tjänsten

För att du ska kunna ansluta till din Azure Kognitiv sökning-tjänst måste du lägga till Sök tjänst informationen i projektet. Högerklicka på projektet i Solution Explorer och välj **Lägg till** > **nytt objekt.** ... Ge filen namnet `appsettings.json` och välj **Lägg till**. 

Den här filen måste inkluderas i din utmatnings katalog. Det gör du genom att högerklicka på `appsettings.json` och sedan välja **Egenskaper**. Ändra värdet för **Kopiera till utdatakatalogen** för att **Kopiera om**det är nyare.

Kopiera nedanstående JSON till din nya JSON-fil.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Lägg till din Sök tjänst och information om Blob Storage-kontot.

Du kan hämta information om Sök tjänsten från sidan Sök konto i Azure Portal. Konto namnet kommer att finnas på huvud sidan och nycklarna kan hittas genom att välja **nycklar**.

Du kan hämta BLOB-anslutningssträngen genom att gå till ditt lagrings konto i Azure Portal, välja **åtkomst nycklar**och sedan kopiera fältet **anslutnings sträng** .

## <a name="add-namespaces"></a>Lägg till namn områden

I den här självstudien används många olika typer från olika namn områden. För att kunna använda dessa typer lägger du till följande i `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Skapa en klient

Skapa en instans av klassen `SearchServiceClient`.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` skapar en ny `SearchServiceClient` med värden som lagras i programmets konfigurations fil (appSettings. JSON).

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
> 

## <a name="create-a-data-source"></a>Skapa en datakälla

Skapa en ny `DataSource`-instans genom att anropa `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` kräver att du anger namnet på data källan, anslutnings strängen och namnet på en BLOB-behållare.

Även om den inte används i den här självstudien definieras även en princip för mjuk borttagning som används för att identifiera borttagna blobar baserat på värdet för en kolumn för mjuk borttagning. Följande princip betraktar en blob som ska tas bort om den har en egenskap för metadata som `IsDeleted` med värdet `true`.

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

Nu när du har initierat `DataSource`-objektet skapar du data källan. `SearchServiceClient` har en `DataSources`-egenskap. Den här egenskapen innehåller alla metoder som du behöver för att skapa, Visa, uppdatera eller ta bort Azure Kognitiv sökning data källor.

För att begäran ska lyckas returnerar metoden den data källa som skapades. Om det uppstår ett problem med begäran, till exempel en ogiltig parameter, kommer metoden att utlösa ett undantag.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Eftersom det här är din första förfrågan kontrollerar du Azure Portal för att bekräfta att data källan har skapats i Azure Kognitiv sökning. På söktjänstens instrumentpanelsida verifierar du att panelen Datakällor har ett nytt objekt. Du kan behöva vänta några minuter medan portalsidan uppdateras.

  ![Panelen data källor i portalen](./media/cognitive-search-tutorial-blob/data-source-tile.png "Panelen data källor i portalen")

## <a name="create-a-skillset"></a>Skapa en kunskapsuppsättning

I det här avsnittet definierar du en uppsättning med anriknings steg som du vill använda för dina data. Varje anriknings steg kallas för en *färdighet* och en uppsättning av anriknings steg en *färdigheter*. I den här självstudien används [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) för färdigheter:

+ [Optisk tecken läsning](cognitive-search-skill-ocr.md) för att identifiera skriven och handskriven text i bildfiler.

+ [Text sammanslagning](cognitive-search-skill-textmerger.md) för att konsolidera text från en samling fält till ett enda fält.

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Text delning](cognitive-search-skill-textsplit.md) för att dela upp stor mängd innehåll i mindre segment innan du anropar nyckel frasens extraherings kunskap och enhets igenkännings kompetensen. Extrahering av nyckel fraser och entitets igenkänning accepterar indata på högst 50 000 tecken. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Enhets igenkänning](cognitive-search-skill-entity-recognition.md) för extrahering av namn på organisationer från innehåll i BLOB-behållaren.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna.

Vid den första bearbetningen knäckar Azure Kognitiv sökning varje dokument för att läsa innehåll från olika fil format. Text som hittas från källfilen placeras i ett genererat ```content```-fält, ett för varje dokument. Därför bör du ange inmatad information för ```"/document/content"``` för att använda den här texten. 

Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-kunskaper

**OCR** -kompetensen extraherar text från bilder. Den här kunskapen förutsätter att det finns ett normalized_images fält. Om du vill generera det här fältet senare i självstudien ställer vi in ```"imageAction"``` konfigurationen i indexerings definitionen på ```"generateNormalizedImages"```.

```csharp
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
```

### <a name="merge-skill"></a>Sammanfoga kunskaper

I det här avsnittet ska du skapa en **sammanfogad** färdighet som sammanfogar dokumentets innehålls fält med den text som har producerats av OCR-kunskaper.

```csharp
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
```

### <a name="language-detection-skill"></a>Kunskap om språk identifiering

**Språkidentifiering** -kunskapen identifierar språket i inmatad text och rapporterar en enda språkkod för varje dokument som skickas på begäran. Vi ska använda utdata från **språkidentifiering** -kompetensen som en del av indata för **text delnings** färdigheten.

```csharp
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
```

### <a name="text-split-skill"></a>Text delnings kunskaper

Den **delade** kunskapen nedan delar upp text efter sidor och begränsar sid längden till 4 000 tecken som mäts av `String.Length`. Algoritmen försöker dela upp texten i segment som har högst `maximumPageLength` storlek. I det här fallet gör algoritmen det bästa sättet att dela upp meningen på en mening, så storleken på segmentet kan vara något mindre än `maximumPageLength`.

```csharp
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
```

### <a name="entity-recognition-skill"></a>Kompetens för enhets igenkänning

Den här `EntityRecognitionSkill`-instansen är inställd på att identifiera kategori typ `organization`. **Enhets igenkännings** kompetensen kan också identifiera kategori typer `person` och `location`.

Observera att fältet "context" är inställt på att ```"/document/pages/*"``` med en asterisk, vilket innebär att ett anriknings steg anropas för varje sida under ```"/document/pages"```.

```csharp
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
```

### <a name="key-phrase-extraction-skill"></a>Extraherings färdighet för nyckel fraser

Precis som `EntityRecognitionSkill`-instansen som precis skapades, anropas **extrahering av diskussionsämne** -kompetensen för varje sida i dokumentet.

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>Skapa och skapa färdigheter

Skapa `Skillset` med de kunskaper som du har skapat.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Skapa färdigheter i din Sök tjänst.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Skapa ett index

I det här avsnittet definierar du indexschemat genom att ange vilka fält som ska ingå i det sökbara indexet och sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | `id`       | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Skapa DemoIndex-klass

Fält för det här indexet definieras med en modell klass. Varje egenskap i modellklassen har attribut som avgör motsvarande indexfälts sökrelaterade beteende. 

Vi ska lägga till modell klassen i en ny C# fil. Högerklicka på projektet och välj **Lägg till** > **nytt objekt...** , välj "klass" och namnge filen `DemoIndex.cs`och välj sedan **Lägg till**.

Se till att ange att du vill använda typer från `Microsoft.Azure.Search` och `Microsoft.Azure.Search.Models` namn områden.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Lägg till modell klass definitionen nedan för att `DemoIndex.cs` och inkludera den i samma namn område där du skapar indexet.

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
```

Nu när du har definierat en modell klass kan du i `Program.cs` skapa en index definition på ett ganska enkelt sätt. Namnet på det här indexet blir "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Under testningen kanske du upptäcker att du försöker skapa indexet mer än en gång. Därför bör du kontrol lera om det index som du håller på att skapa redan finns innan du försöker skapa det.

```csharp
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

Mer information om hur du definierar ett index finns i [skapa index (Azure Kognitiv sökning REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Skapa en indexerare, mappa fält och köra transformeringar

Hittills har du skapat en datakälla, en kunskapsuppsättning och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. Om du vill sammanfoga dem i en indexerare måste du definiera fältmappningar.

+ FieldMappings bearbetas före färdigheter och mappar käll fälten från data källan till mål fälten i ett index. Om fält namn och typer är desamma i båda ändar, krävs ingen mappning.

+ OutputFieldMappings bearbetas efter färdigheter, refererar till sourceFieldNames som inte finns förrän dokument sprickor eller berikning skapar dem. TargetFieldName är ett fält i ett index.

Förutom att koppla upp indata till utdata kan du också använda fält mappningar för att förenkla data strukturer. Mer information finns i [så här mappar du berikade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

```csharp
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
    // Handle exception
}
```

Det kan ta en stund att skapa indexeraren. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. Vissa kunskaper, som bildanalys, är tidskrävande.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet.

### <a name="explore-creating-the-indexer"></a>Utforska hur du skapar indexeraren

Koden anger ```"maxFailedItems"``` till-1, vilket instruerar indexerings motorn att ignorera fel vid data import. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också att ```"dataToExtract"``` är inställt på ```"contentAndMetadata"```. Den här instruktionen anger att indexeraren automatiskt ska extrahera innehållet från olika filformat samt metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. ```"imageAction"``` har angetts till ```"generateNormalizedImages"``` konfiguration, kombinerat med OCR-kunskaper och text sammanfognings kunskap, så instruerar indexeraren att extrahera text från bilderna (till exempel ordet "Stop" från ett trafik stopp) och bädda in det som en del av innehålls fältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

## <a name="check-indexer-status"></a>Kontrollera status för indexerare

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Om du vill ta reda på om indexeraren fortfarande körs använder du metoden `GetStatus`.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo` representerar en indexerare aktuella status och körnings historik.

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga (till exempel inga textindata från JPEG-filerna).
 
## <a name="query-your-index"></a>Skicka frågor mot ditt index

När indexeringen är färdig kan du köra frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Kognitiv sökning de översta 50 resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Anvisningar finns i [så här visar du sid resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

Som ett verifieringssteg ska du fråga indexet för alla fält.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` skapar en ny `SearchIndexClient` med värden som lagras i programmets konfigurations fil (appSettings. JSON). Observera att API-nyckeln för search service-frågan används och inte administratörs nyckeln.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Utdata är indexeringsschema med namn, typ och attribut för varje fält.

Skicka en ny fråga för `"*"` för att returnera hela innehållet i ett enda fält som `organizations`.

```csharp
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

Upprepa för ytterligare fält: innehåll, languageCode, diskussions fraser och organisationer i den här övningen. Du kan returnera flera fält via `$select` med hjälp av en kommaavgränsad lista.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design iterationer att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Den här självstudien tog hand om att söka efter befintliga indexerare och index och ta bort dem om de redan funnits, så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare och färdighetsuppsättningar.

När koden utvecklas kanske du vill begränsa en strategi för återskapning. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

## <a name="takeaways"></a>Lärdomar

Den här självstudien demonstrerade de grundläggande stegen för att skapa en omfattande indexerings pipeline genom att skapa komponent delar: en data källa, färdigheter, index och indexerare.

[Inbyggda kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med färdigheter-definitionen och Mechanics för länkning av färdigheter tillsammans genom indata och utdata. Du har också lärt dig att `outputFieldMappings` i Indexer-definitionen krävs för att dirigera berikade värden från pipelinen till ett sökbart index i en Azure Kognitiv sökning-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. Du har också lärt dig att kontrollera indexerarstatus, och vilka objekt du ska ta bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa efter en själv studie kurs är att ta bort resurs gruppen som innehåller Azure Kognitiv sökning service och Azure Blob Service. Förutsatt att du placerade båda tjänsterna i samma grupp, tar du bort resursgruppen för att permanent ta bort allt i den, inklusive tjänsterna och eventuellt lagrat innehåll som du skapade i den här självstudien. På portalen visas resursgruppens namn på översiktssidan för varje tjänst.

## <a name="next-steps"></a>Nästa steg

Anpassa eller utöka pipelinen med anpassade kunskaper. När du skapar en anpassad kunskap och lägger till den i en kunskapsuppsättning kan du publicera text eller bildanalys som du skriver själv.

> [!div class="nextstepaction"]
> [Exempel: skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)
