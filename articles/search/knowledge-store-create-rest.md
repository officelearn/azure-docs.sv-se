---
title: Skapa ett kunskaps lager med REST
titleSuffix: Azure Cognitive Search
description: Använd REST API och Postman för att skapa en Azure Kognitiv sökning kunskaps lager för att spara omfattande innehåll från en AI-pipeline.
author: lobrien
manager: nitinme
ms.author: laobri
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 24b97374b032640afafde775e90f6db735d63c46
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790020"
---
# <a name="create-an-azure-cognitive-search-knowledge-store-by-using-rest"></a>Skapa en Azure Kognitiv sökning kunskaps lager med hjälp av REST

Kunskaps lagrings funktionen i Azure Kognitiv sökning sparar utdata från en AI-pipeline för senare analys eller annan efterföljande bearbetning. En AI-fördefinierad pipeline tar emot bildfiler eller ostrukturerade textfiler, indexerar dem med hjälp av Azure Kognitiv sökning, använder AI-anrikninger från Azure Cognitive Services (till exempel bild analys och naturlig språk bearbetning) och sparar sedan resultatet i en kunskaps lager i Azure Storage. Du kan använda verktyg som Power BI eller Storage Explorer i Azure Portal för att utforska kunskaps lagret.

I den här artikeln använder du REST API-gränssnittet för att mata in, indexera och tillämpa AI-berikare i en uppsättning Hotell recensioner. Hotell granskningarna importeras till Azure Blob Storage. Resultaten sparas som ett kunskaps lager i Azure Table Storage.

När du har skapat kunskaps lagret kan du lära dig mer om hur du kommer åt kunskaps lagret med hjälp av [Storage Explorer](knowledge-store-view-storage-explorer.md) eller [Power BI](knowledge-store-connect-power-bi.md).

## <a name="create-services"></a>Skapa tjänster

Skapa följande tjänster:

- Skapa en [Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

- Skapa ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data och kunskaps lagret. Ditt lagrings konto måste använda samma plats (till exempel västra USA) för din Azure Kognitiv sökning-tjänst. Värdet för **konto typen** måste vara **StorageV2 (generell användning v2)** (standard) eller **Storage (generell användning v1)** .

- Rekommenderat: Hämta [appen Postman Desktop](https://www.getpostman.com/) för att skicka förfrågningar till Azure kognitiv sökning. Du kan använda REST API med ett verktyg som kan arbeta med HTTP-förfrågningar och-svar. Postman är ett bra alternativ för att utforska REST-API: er. Vi använder Postman i den här artikeln. [Käll koden](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) för den här artikeln innehåller också en Postman-samling med begär Anden. 

## <a name="store-the-data"></a>Lagra data

Läs in CSV-filen för hotell granskningar i Azure Blob Storage så att den kan nås av en Azure Kognitiv sökning-indexerare och matas genom AI-anrikningen.

### <a name="create-a-blob-container-by-using-the-data"></a>Skapa en BLOB-behållare med hjälp av data

1. Ladda ned [hotell gransknings data](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) som sparats i en CSV-fil (HotelReviews_Free. csv). Dessa data härstammar från Kaggle.com och innehåller kundfeedback om hotell.
1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt Azure Storage-konto.
1. Skapa en [BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Om du vill skapa behållaren går du till den vänstra menyn för ditt lagrings konto, väljer **blobbar**och väljer sedan **container**.
1. Ange **hotell granskning**för det nya behållar **namnet**.
1. För **offentlig åtkomst nivå**väljer du ett värde. Vi använde standardvärdet.
1. Välj **OK** för att skapa BLOB-behållaren.
1. Öppna behållaren ny **hotell-granska** , Välj **Ladda upp**och välj sedan den HotelReviews-Free. csv-fil som du laddade ned i det första steget.

    ![Överför data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp Hotell recensioner")

1. Välj **överför** för att importera CSV-filen till Azure Blob Storage. Den nya behållaren visas:

    ![Skapa BLOB-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa BLOB-behållaren")

## <a name="configure-postman"></a>Konfigurera Postman

Installera och konfigurera Postman.

### <a name="download-and-install-postman"></a>Hämta och installera Postman

1. Hämta [käll koden för Postman-samlingen](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Välj **File** > **import** för att importera käll koden till Postman.
1. Välj fliken **samlingar** och välj sedan knappen **...** (ellips).
1. Välj **Redigera**. 
   
   ![Postman-app som visar navigering](media/knowledge-store-create-rest/postman-edit-menu.png "Gå till redigerings menyn i Postman")
1. I dialog rutan **Redigera** väljer du fliken **variabler** . 

På fliken **variabler** kan du lägga till värden som Postman växlar i varje gång det påträffar en speciell variabel inom dubbla klammerparenteser. Postman ersätter exempelvis symbolen `{{admin-key}}` med det aktuella värde som du angav för `admin-key`. Postman gör ersättningen i URL: er, sidhuvuden, begär ande texten och så vidare. 

Om du vill hämta värdet för `admin-key`går du till Azure Kognitiv sökning-tjänsten och väljer fliken **nycklar** . ändra `search-service-name` och `storage-account-name` till de värden som du valde i [skapa tjänster](#create-services). Ange `storage-connection-string` med hjälp av värdet på fliken **åtkomst nycklar** för lagrings kontot. Du kan lämna standardvärdena för de andra värdena.

![Fliken Postman-app-variabler](media/knowledge-store-create-rest/postman-variables-window.png "Fönstret för Postman-variabler")


| Variabel    | Var du får den |
|-------------|-----------------|
| `admin-key` | På sidan **nycklar** i Azure kognitiv sökning-tjänsten.  |
| `api-version` | Lämna som **2019-05-06-för hands version**. |
| `datasource-name` | Lämna som **Hotell – recensioner – DS**. | 
| `indexer-name` | Lämna som **Hotell – recensioner – IXR**. | 
| `index-name` | Lämna som **Hotell – recensioner – IX**. | 
| `search-service-name` | Namnet på Azure Kognitiv sökning-tjänsten. URL: en är `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Lämna som **Hotell – recensioner – SS**. | 
| `storage-account-name` | Namnet på lagringskontot. | 
| `storage-connection-string` | I lagrings kontot på fliken **åtkomst nycklar** väljer du **KEY1** > -**anslutningssträng**. | 
| `storage-container-name` | Lämna som **Hotell – recensioner**. | 

### <a name="review-the-request-collection-in-postman"></a>Granska Request-samlingen i Postman

När du skapar ett kunskaps lager måste du utfärda fyra HTTP-förfrågningar: 

- **Skicka begäran om att skapa indexet**: det här indexet innehåller de data som Azure kognitiv sökning använder och returnerar.
- **Post-begäran för att skapa data källan**: den här data källan ansluter ditt Azure kognitiv sökning-beteende till data och kunskaps lagrets lagrings konto. 
- **Skicka begäran om att skapa färdigheter**: färdigheter anger de anrikninger som används för dina data och kunskaps lagrets struktur.
- **Skicka begäran om att skapa indexeraren**: att köra indexeraren läser data, tillämpar färdigheter och lagrar resultaten. Du måste köra den här begäran senast.

[Käll koden](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) innehåller en Postman-samling som har fyra begär Anden. Om du vill utfärda förfrågningarna väljer du fliken för begäran i Postman. Lägg sedan till `api-key`-och `Content-Type`-begärandehuvuden. Ange värdet för `api-key` till `{{admin-key}}`. Ange värdet `Content-type` till `application/json`. 

![Skärm bild som visar Postman gränssnitt för rubriker](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Du måste ange `api-key`-och `Content-type`-huvuden i alla dina begär Anden. Om Postman identifierar en variabel, visas variabeln i orange text, precis som med `{{admin-key}}` i föregående skärm bild. Om variabeln är felstavad visas den i röd text.
>

## <a name="create-an-azure-cognitive-search-index"></a>Skapa ett Azure Kognitiv sökning-index

Skapa ett Azure Kognitiv sökning-index som representerar de data som du är intresse rad av vid sökning, filtrering och användning av förbättringar i. Skapa indexet genom att utfärda en skicka begäran till `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman ersätter symboler som omges av dubbla klammerparenteser (till exempel `{{search-service-name}}`, `{{index-name}}` och `{{api-version}}`) med de värden som du anger i [Konfigurera PostMan](#configure-postman). Om du använder ett annat verktyg för att utfärda REST-kommandon måste du ersätta dessa variabler själv.

Ange strukturen för ditt Azure Kognitiv sökning-index i bröd texten i begäran. När du har ställt in `api-key`-och `Content-type`-huvudena i Postman går du till **text** rutan i begäran. Du bör se följande JSON. Om du inte gör det väljer du **Raw** > **JSON (Application/JSON)** och klistrar sedan in följande kod som brödtext:

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

Den här index definitionen är en kombination av data som du vill presentera för användaren (namnet på hotellet, granska innehåll, datum), sökmetadata och AI-förbättringar (sentiment, diskussions fraser och språk).

Välj **Skicka** för att skicka begäran om placering. Du bör se status `201 - Created`. Om du ser en annan status i **text** rutan söker du efter ett JSON-svar som innehåller ett fel meddelande. 

## <a name="create-the-datasource"></a>Skapa data källan

Anslut sedan Azure Kognitiv sökning till de hotell data som du har lagrat i [lagra data](#store-the-data). Om du vill skapa data källan skickar du en POST-begäran till `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Du måste ange `api-key`-och `Content-Type`-huvuden enligt beskrivningen ovan. 

I Postman går du till begäran om att **skapa DataSource** och sedan till **text** rutan. Du bör se följande kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Välj **Skicka** för att skicka en post-begäran. 

## <a name="create-the-skillset"></a>Skapa färdigheter 

Nästa steg är att ange färdigheter, som anger både de förbättringar som ska tillämpas och det kunskaps lager där resultatet ska lagras. I Postman väljer du fliken **skapa färdigheter** . Den här begäran skickar en placering till `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Ange `api-key`-och `Content-type`-huvuden som du gjorde tidigare. 

Det finns två stora objekt på översta nivån: `skills` och `knowledgeStore`. Varje objekt i `skills`-objektet är en anriknings tjänst. Varje anriknings tjänst har `inputs` och `outputs`. @No__t_0 har `Language` för utdata `targetName`. Värdet för den här noden används av de flesta andra färdigheter som inmatade. Källan är `document/Language`. Möjligheten att använda utdata från en nod som indata till en annan är ännu tydligare i `ShaperSkill`, som anger hur data flödar till kunskaps lagrets tabeller.

`knowledge_store`-objektet ansluter till lagrings kontot via variabeln `{{storage-connection-string}}` Postman. `knowledge_store` innehåller en uppsättning mappningar mellan det förbättrade dokumentet och tabeller och kolumner i kunskaps lagret. 

Om du vill generera färdigheter väljer du knappen **Skicka** i Postman för att placera begäran:

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

Det sista steget är att skapa indexeraren. Indexeraren läser data och aktiverar färdigheter. I Postman väljer du begäran om att **skapa indexerare** och granskar sedan bröd texten. Definitionen av indexeraren avser flera andra resurser som du redan har skapat: data källan, indexet och färdigheter. 

Objektet `parameters/configuration` styr hur indexeraren matar in data. I det här fallet är indata i ett enda dokument som har en rubrik rad och kommaavgränsade värden. Dokument nyckeln är en unik identifierare för dokumentet. Före kodningen är dokument nyckeln URL: en för käll dokumentet. Slutligen är färdigheter-utdataparametrar, som språk kod, sentiment och nyckel fraser mappade till deras platser i dokumentet. Även om det finns ett enda värde för `Language`, används `Sentiment` för varje element i matrisen med `pages`. `Keyphrases` är en matris som också används för varje element i `pages`-matrisen.

När du har angett `api-key`-och `Content-type`-huvuden och bekräfta att bröd texten i begäran liknar följande källkod väljer du **Skicka** i Postman. Postman skickar en skicka-begäran till `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Kognitiv sökning skapar och kör indexeraren. 

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

I Azure Portal går du till **översikts** sidan för Azure kognitiv sökning-tjänsten. Välj fliken **indexerare** och välj sedan **Hotels-Reviews-IXR**. Om indexeraren inte redan har körts väljer du **Kör**. Indexerings aktiviteten kan generera vissa varningar som rör språk igenkänning. Data innehåller vissa granskningar som är skrivna på språk som ännu inte stöds av kognitiva färdigheter. 

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data genom att använda Cognitive Services och projicerat resultaten till ett kunskaps lager, kan du använda Storage Explorer eller Power BI för att utforska din omfattande data uppsättning.

Information om hur du utforskar det här kunskaps lagret med hjälp av Storage Explorer finns i den här genom gången:

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)

Information om hur du ansluter det här kunskaps lagret till Power BI finns i den här genom gången:

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)

Om du vill upprepa den här övningen eller testa en annan AI-förhands granskning tar du bort indexet **hotell reidxrs-** . Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren till noll.
