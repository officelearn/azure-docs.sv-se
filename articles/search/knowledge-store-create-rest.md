---
title: Skapa ett kunskapsarkiv (förhandsgranskning) med REST
titleSuffix: Azure Cognitive Search
description: Använd REST API och Postman för att skapa ett Azure Cognitive Search-kunskapsarkiv för beständiga enrichments från en AI-anrikningspipeline. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 478a7e03b432006b429c96e03307fd8e494c88ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472323"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Skapa ett kunskapslager med REST och Postman

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

Ett kunskapsarkiv innehåller utdata från en Azure Cognitive Search-anrikningspipeline för senare analys eller annan nedströmsbearbetning. En AI-berikad pipeline accepterar bildfiler eller ostrukturerade textfiler, indexerar dem med hjälp av Azure Cognitive Search, tillämpar AI-berikande från Cognitive Services (till exempel bildanalys och bearbetning av naturligt språk) och sparar sedan resultaten till en kunskapsarkivet i Azure Storage. Du kan använda verktyg som Power BI eller Storage Explorer i Azure-portalen för att utforska kunskapsarkivet.

I den här artikeln använder du REST API-gränssnittet för att mata in, indexera och tillämpa AI-enrichments på en uppsättning hotellrecensioner. Hotellrecensionerna importeras till Azure Blob-lagring. Resultaten sparas som ett kunskapslager i Azure Table storage.

När du har skapat kunskapsarkivet kan du läsa om hur du kommer åt kunskapsarkivet med hjälp av [Storage Explorer](knowledge-store-view-storage-explorer.md) eller [Power BI](knowledge-store-connect-power-bi.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!TIP]
> Vi rekommenderar [Postman desktop app](https://www.getpostman.com/) för den här artikeln. [Källkoden](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) för den här artikeln innehåller en Postman-samling som innehåller alla begäranden. 

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

Den här snabbstarten använder Azure Cognitive Search, Azure Blob Storage och [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. 

Eftersom arbetsbelastningen är så liten utnyttjas Cognitive Services bakom kulisserna för att tillhandahålla kostnadsfri bearbetning för upp till 20 transaktioner dagligen. Eftersom datauppsättningen är så liten kan du hoppa över att skapa eller koppla en Cognitive Services-resurs.

1. [Ladda ner HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dessa data är hotell granskningsdata sparas i en CSV-fil (kommer från Kaggle.com) och innehåller 19 bitar av kundfeedback om ett enda hotell. 

1. [Skapa ett Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under din aktuella prenumeration. Du använder Azure-lagring för både råinnehåll som ska importeras och kunskapsarkivet som är slutresultatet.

   Välj kontotypen **StorageV2 (allmänt V2).**

1. Öppna sidorna Blob-tjänster och skapa en behållare med namnet *hotel-reviews*.

1. Klicka på **Överför**.

    ![Ladda upp data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp hotellrecensionerna")

1. Välj **den HotelReviews-Free.csv-fil** som du laddade ner i det första steget.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

1. Du är nästan klar med den här resursen, men innan du lämnar dessa sidor använder du en länk i det vänstra navigeringsfönstret för att öppna sidan **Snabbtangenter.** Hämta en anslutningssträng för att hämta data från Blob-lagring. En anslutningssträng liknar följande exempel:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Växla till Azure Cognitive Search fortfarande i portalen. [Skapa en ny tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnadsfri tjänst för den här övningen.

## <a name="configure-postman"></a>Konfigurera Postman

Installera och konfigurera Postman.

### <a name="download-and-install-postman"></a>Ladda ner och installera Postman

1. Ladda ner [källkoden för Postman-samlingen](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Välj **Filimport** > **Import** om du vill importera källkoden till Postman.
1. Välj fliken **Samlingar** och välj sedan knappen **...** (ellips).
1. Välj **Redigera**. 
   
   ![Postman-appen som visar navigering](media/knowledge-store-create-rest/postman-edit-menu.png "Gå till Redigera-menyn i Postman")
1. Välj fliken **Variabler** i dialogrutan **Redigera.** 

På fliken **Variabler** kan du lägga till värden som Postman växlar in varje gång den stöter på en viss variabel inuti dubbla klammerparenteser. Brevbäraren ersätter till `{{admin-key}}` exempel symbolen med det `admin-key`aktuella värde som du anger för . Brevbäraren gör ersättningen i webbadresser, rubriker, förfrågadstexten och så vidare. 

Om du vill `admin-key`hämta värdet för går du till Azure `search-service-name` Cognitive `storage-account-name` Search-tjänsten och [Create services](#create-services-and-load-data)väljer fliken **Nycklar.** Ange `storage-connection-string` med hjälp av värdet på fliken **Åtkomstnycklar** för lagringskontot. Du kan lämna standardvärdena för de andra värdena.

![Fliken Postman-appvariabler](media/knowledge-store-create-rest/postman-variables-window.png "Fönstret Postmans variabler")


| Variabel    | Hämta det på |
|-------------|-----------------|
| `admin-key` | På sidan **Nycklar** i azure cognitive search-tjänsten.  |
| `api-version` | Lämna som **2019-05-06-Preview**. |
| `datasource-name` | Lämna som **hotell-recensioner-ds**. | 
| `indexer-name` | Lämna som **hotell-recensioner-ixr**. | 
| `index-name` | Lämna som **hotell-recensioner-ix**. | 
| `search-service-name` | Namnet på Azure Cognitive Search-tjänsten. Webbadressen `https://{{search-service-name}}.search.windows.net`är . | 
| `skillset-name` | Lämna som **hotell-recensioner-ss**. | 
| `storage-account-name` | Namnet på lagringskontot. | 
| `storage-connection-string` | Välj **nyckel1** > **Anslutningssträng**på fliken **Accesstangenter** i lagringskontot . | 
| `storage-container-name` | Lämna som **hotell-recensioner**. | 

### <a name="review-the-request-collection-in-postman"></a>Granska begäran samlingen i Postman

När du skapar ett kunskapsarkiv måste du utfärda fyra HTTP-begäranden: 

- **PUT-begäran om att skapa indexet:** Det här indexet innehåller de data som Azure Cognitive Search använder och returnerar.
- **POST-begäran om att skapa datakällan:** Den här datakällan ansluter ditt Azure Cognitive Search-beteende till data- och kunskapsarkivets lagringskonto. 
- **PUT begäran om att skapa kompetens:** Kompetensen anger de enrichments som tillämpas på dina data och strukturen i kunskapsarkivet.
- **PUT-begäran om att skapa indexeraren**: Kör indexeraren läser data, tillämpar kompetensuppsättningen och lagrar resultaten. Du måste köra den här begäran senast.

[Källkoden](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) innehåller en Postman-samling som har de fyra begärandena. Om du vill utfärda begäranden väljer du fliken för begäran i Postman. Lägg sedan `api-key` `Content-Type` till och begär rubriker. Ange värdet `api-key` på `{{admin-key}}`. Ange värdet `Content-type` `application/json`till . 

![Skärmbild som visar Postmans gränssnitt för rubriker](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Du måste `api-key` `Content-type` ange och rubriker i alla dina begäranden. Om Postman känner igen en variabel visas variabeln i orange text, som med `{{admin-key}}` i föregående skärmbild. Om variabeln är felstavad visas den i röd text.
>

## <a name="create-an-azure-cognitive-search-index"></a>Skapa ett Azure Cognitive Search-index

Skapa ett Azure Cognitive Search-index för att representera de data som du är intresserad av att söka, filtrera och tillämpa förbättringar på. Skapa indexet genom att utfärda `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`en PUT-begäran till . Brevbäraren ersätter symboler som omges av `{{search-service-name}}`dubbla `{{index-name}}`klammerparenteser (till exempel , och `{{api-version}}`) med de värden som du anger i [Konfigurera postman](#configure-postman). Om du använder ett annat verktyg för att utfärda REST-kommandon måste du ersätta dessa variabler själv.

Ange strukturen för ditt Azure Cognitive Search-index i brödtexten för begäran. I Postman går du `api-key` `Content-type` till **brödtextfönstret** för begäran när du har ställt in och rubriker. Du skulle se följande JSON. Om du inte gör det väljer du **Raw** > **JSON (application/json)** och klistrar sedan in följande kod som brödtext:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Den här indexdefinitionen är en kombination av data som du vill presentera för användaren (namnet på hotellet, granska innehåll, datum), sökmetadata och AI-förbättringsdata (sentiment, nyckelfraser och språk).

Välj **Skicka** om du vill utfärda PUT-begäran. Du bör se `201 - Created`status . Om du ser en annan status i **brödtextfönstret** letar du efter ett JSON-svar som innehåller ett felmeddelande. 

## <a name="create-the-datasource"></a>Skapa datakällan

Anslut sedan Azure Cognitive Search till hotelldata som du har lagrat i Blob-lagring. Om du vill skapa datakällan `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`skickar du en POST-begäran till . Du måste `api-key` ange `Content-Type` rubrikerna och rubrikerna som diskuterats tidigare. 

Gå till **begäran Skapa datakälla** i Postman och sedan till **brödtextfönstret.** Du bör se följande kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Välj **Skicka** om du vill utfärda POST-begäran. 

## <a name="create-the-skillset"></a>Skapa kompetensen 

Nästa steg är att ange kompetensen, som anger både de förbättringar som ska tillämpas och kunskapsarkivet där resultaten ska lagras. Välj fliken Skapa kunskaper **i** Postman. Den här begäran `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`skickar en PUT till . Ange `api-key` rubriker `Content-type` och rubriker som du gjorde tidigare. 

Det finns två stora objekt `skills` `knowledgeStore`på den översta nivån: och . Varje objekt `skills` i objektet är en berikningstjänst. Varje berikningstjänst har `inputs` och `outputs`. Har `LanguageDetectionSkill` en `targetName` utdata från `Language`. Värdet för den här noden används av de flesta andra kunskaper som indata. Källan är `document/Language`. Möjligheten att använda utdata från en nod som indata till en annan är ännu tydligare i `ShaperSkill`, som anger hur data flödar in i tabellerna i kunskapsarkivet.

Objektet `knowledge_store` ansluter till lagringskontot `{{storage-connection-string}}` via Postman-variabeln. `knowledge_store`innehåller en uppsättning mappningar mellan det förbättrade dokumentet och tabeller och kolumner i kunskapsarkivet. 

Om du vill generera kompetensen väljer du **knappen Skicka** i Postman för att placera begäran:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Skapa indexeraren

Det sista steget är att skapa indexeraren. Indexeraren läser data och aktiverar kompetensen. Välj begäran Skapa **indexerare** i Postman och granska sedan brödtexten. Definitionen av indexeraren refererar till flera andra resurser som du redan har skapat: datakällan, indexet och kompetensen. 

Objektet `parameters/configuration` styr hur indexeraren förtärr data. I det här fallet finns indata i ett enda dokument som har en rubrikrad och kommaavgränsade värden. Dokumentnyckeln är en unik identifierare för dokumentet. Innan kodningen kodas är dokumentnyckeln url:en till källdokumentet. Slutligen mappas kunskapsutdatavärdena, till exempel språkkod, sentiment och nyckelfraser, till deras platser i dokumentet. Även om det finns `Language`ett `Sentiment` enda värde för används `pages`det på varje element i matrisen . `Keyphrases`är en matris som också tillämpas på `pages` varje element i matrisen.

När du `api-key` har `Content-type` angett rubrikerna och bekräftar att brödtexten för begäran liknar följande källkod väljer du **Skicka** i Brevbärare. Brevbäraren skickar en `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`PUT-begäran till . Azure Cognitive Search skapar och kör indexeraren. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Köra indexeraren 

Gå till översiktssidan **för** Azure Cognitive Search i Azure Cognitive Search-tjänsten. Välj fliken **Indexerare** och välj sedan **hotels-reviews-ixr**. Om indexeraren inte redan har körts väljer du **Kör**. Indexeringsuppgiften kan ge upphov till vissa varningar som rör språkigenkänning. Data innehåller några recensioner som är skrivna på språk som ännu inte stöds av kognitiva färdigheter. 

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data med hjälp av Cognitive Services och projicerat resultaten till ett kunskapslager kan du använda Storage Explorer eller Power BI för att utforska din berikade datauppsättning.

Mer information om hur du utforskar det här kunskapsarkivet med hjälp av Storage Explorer finns i den här genomgången:

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)

Mer information om hur du ansluter det här kunskapsarkivet till Power BI finns i den här genomgången:

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)

Om du vill upprepa den här övningen eller prova en annan GENOMGÅNG AV AI-anrikning tar du bort **indexeraren för hotel-reviews-idxr.** Om du tar bort indexeraren återställs den kostnadsfria dagliga transaktionsräknaren till noll.
