---
title: Skapa ett kunskaps lager med hjälp av REST-Azure Search
description: Skapa en Azure Search kunskaps lager för att spara innehåll från kognitiv Sök pipelinen med hjälp av REST API och Postman.
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: laobri
ms.openlocfilehash: ae0694c4c79527ef3b64ad68d32ef3bce0150462
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703575"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Skapa en Azure Search kunskaps lager med hjälp av REST

Kunskap Store är en funktion i Azure Search som sparar utdata från en AI-pipeline för senare analys eller annan efterföljande bearbetning. En AI-fördefinierad pipeline tar emot bildfiler eller ostrukturerade textfiler, indexerar dem med hjälp av Azure Search, tillämpar AI-anrikninger från Cognitive Services (till exempel bild analys och naturlig språk bearbetning) och sparar sedan resultaten i ett kunskaps lager i Azure lagrings. Du kan sedan använda verktyg som Power BI eller Storage Explorer för att utforska kunskaps lagret.

I den här artikeln ska du använda REST API-gränssnittet för att mata in, indexera och tillämpa AI-berikare i en uppsättning Hotell recensioner. Hotell granskningarna importeras till Azure Blob Storage och resultaten sparas som ett kunskaps lager i Azure Table Storage.

När du har skapat kunskaps lagret kan du lära dig mer om att komma åt det här kunskaps lagret med hjälp av [Storage Explorer](knowledge-store-view-storage-explorer.md) eller [Power BI](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1 – skapa tjänster

+ [Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

+ [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data och kunskaps lagret. Ditt lagrings konto måste använda samma plats (till exempel västra USA) för din Azure Search-tjänst. *Konto typen* måste vara *StorageV2 (generell användning v2)* (standard) eller *Storage (generell användning v1)* .

+ Rekommenderat: [Postman Desktop-appen](https://www.getpostman.com/) för att skicka begär anden till Azure Search. Du kan använda REST API med valfritt verktyg som kan arbeta med HTTP-förfrågningar och-svar. Postman är ett bra alternativ för att utforska REST-API: er och används i den här artikeln. Dessutom innehåller [käll koden](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) för den här artikeln en Postman-samling med begär Anden. 

## <a name="2---store-the-data"></a>2 – lagra data

Läs in CSV-filen för hotell granskningar i Azure Blob Storage så att den kan nås av en Azure Search-indexerare och matas genom AI-anrikningen.

### <a name="create-an-azure-blob-container-with-the-data"></a>Skapa en Azure Blob-behållare med data

1. [Ladda ned hotell gransknings data som sparats i en CSV-fil (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Dessa data härstammar från Kaggle.com och innehåller kundfeedback om hotell.
1. [Logga](https://portal.azure.com)in på Azure Portal och navigera till ditt Azure Storage-konto.
1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Skapa behållaren genom att klicka på **blobbar**i det vänstra navigerings fältet för ditt lagrings konto och klicka sedan på **+ container** i kommando fältet.
1. För det nya behållar **namnet**anger `hotel-reviews`du.
1. Välj valfri **offentlig åtkomst nivå**. Vi använde standardvärdet.
1. Skapa Azure Blob-behållaren genom att klicka på **OK** .
1. Öppna den nya `hotels-review` behållaren, klicka på **Ladda upp**och välj den **HotelReviews-Free. csv** -fil som du laddade ned i det första steget.

    ![Överför data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp Hotell recensioner")

1. Klicka på **överför** för att importera CSV-filen till Azure Blob Storage. Den nya behållaren kommer att visas.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

## <a name="3---configure-postman"></a>3 – Konfigurera PostMan

Ladda ned [käll koden för Postman-samlingen](https://github.com/Azure-Samples/azure-search-postman-samples/knowledge-store/KnowledgeStore.postman_collection.json) och importera den till Postman med hjälp av **fil, importera.** .. Växla till fliken **samlingar** och klicka på knappen **...** och välj **Redigera**. 

![Postman-app som visar navigering](media/knowledge-store-create-rest/postman-edit-menu.png "navigera till redigerings menyn i Postman")

Gå till fliken **variabler** i dialog rutan för redigering. 

Med fliken **variabler** kan du lägga till värden som Postman växlar i varje gång det påträffar dem inom dubbla klammerparenteser. Postman ersätter exempelvis symbolen `{{admin-key}}` med det "aktuella värdet" för `admin-key`. Postman kommer att göra den här ersättningen i URL: er, sidhuvuden, begär ande texten och så vidare. 

Du hittar värdet för `admin-key` på fliken **nycklar** i search service. Du måste ändra `search-service-name` och `storage-account-name` till de värden som du valde i [steg 1](#1---create-services). Ange `storage-connection-string` från värdet på fliken **åtkomst nycklar** för lagrings kontot. De andra värdena du kan lämna oförändrade.

![Fliken Postman app-variabler](media/knowledge-store-create-rest/postman-variables-window.png "Boxman-fönstret variabler")


| Variabel    | Var du får den |
|-------------|-----------------|
| `admin-key` | Search Service, fliken **nycklar**              |
| `api-version` | Lämna som "2019-05-06-för hands version" |
| `datasource-name` | Lämna som "Hotell-recensioner-DS" | 
| `indexer-name` | Lämna som "Hotell – recensioner-IXR" | 
| `index-name` | Lämna som "Hotell – recensioner-IX" | 
| `search-service-name` | Search Service, huvud namn. URL: en är `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Lämna som "Hotell-recensioner-SS" | 
| `storage-account-name` | Lagrings konto, huvud namn | 
| `storage-connection-string` | Lagrings konto, fliken **åtkomst nycklar** , **KEY1** - **anslutningssträng** | 
| `storage-container-name` | Lämna som "Hotell – recensioner" | 

### <a name="review-the-request-collection-in-postman"></a>Granska Request-samlingen i Postman

När du skapar ett kunskaps lager måste du skicka fyra HTTP-förfrågningar: 

1. En skicka-begäran för att skapa indexet. Det här indexet innehåller de data som används och returnerades av Azure Search.
1. En POST-begäran för att skapa data källan. Den här data källan ansluter ditt Azure Search beteende till data och kunskaps lagerets lagrings konto. 
1. En skicka begäran om att skapa färdigheter. Färdigheter anger hur många som ska användas för dina data och kunskaps lagrets struktur.
1. En skicka-begäran för att skapa indexeraren. Att köra indexeraren läser data, tillämpar färdigheter och lagrar resultaten. Du måste köra den här begäran senast.

[Käll koden](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) innehåller en Postman-samling med dessa fyra förfrågningar. Om du vill utfärda begär Anden växlar du till begärans flik i Postman och lägger till `api-key`-och `Content-Type`-begärandehuvuden. Ange värdet för `api-key` till `{{admin-key}}`. Ange värdet `Content-type` till `application/json`. 

> [!div class="mx-imgBorder"]
> ![Screenshot som visar Postman-gränssnittet för huvuden @ no__t-1

> [!Note]
> Du måste ange `api-key`-och `Content-type`-huvuden i alla dina begär Anden. Om en variabel identifieras av Postman, kommer den att återges i orange text, precis som med `{{admin-key}}` i skärm bilden. Om variabeln är felstavad kommer den att återges i röd text.
>

## <a name="4---create-an-azure-search-index"></a>4 – skapa ett Azure Search-index

Du måste skapa ett Azure Search-index för att representera data som du är intresse rad av att söka i, filtrera och göra förbättringar. Du skapar indexet genom att utfärda en skicka begäran till `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman ersätter symboler som omges av dubbla klammerparenteser, till exempel `{{search-service-name}}`, `{{index-name}}` och `{{api-version}}` med de värden som anges i [steg 3](#3---configure-postman). Om du använder ett annat verktyg för att utfärda REST-kommandon måste du ersätta dessa variabler själv.

Ange strukturen för Azure Search indexet i bröd texten i begäran. I Postman efter att du har angett `api-key`-och `Content-type`-huvudena, växlar du till **text** rutan i begäran. Du bör se följande JSON, men om inte väljer du **RAW** och **JSON (Application/JSON)** och klistrar in följande kod som brödtext:

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

Du ser att denna index definition är en kombination av data som du vill presentera för användaren (namnet på hotellet, granska innehåll, datum osv.), sökmetadata och AI-förbättringar (sentiment, tangentkombinationer och språk).

Tryck på knappen **Skicka** för att skicka en begäran om placering. Du bör få status meddelandet `201 - Created`. Om du får en annan status visas ett JSON-svar med ett fel meddelande i fönstret **brödtext** . 

## <a name="5---create-the-datasource"></a>5 – skapa data källan

Nu måste du ansluta Azure Search till de hotell data som du sparade i [steg 2](#2---store-the-data). Att skapa data källan görs med ett inlägg i `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Återigen måste du ange `api-key`-och `Content-Type`-huvudena som du har angett tidigare. 

Öppna begäran "skapa DataSource" i Postman. Växla till fönstret **brödtext** , som bör ha följande kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Tryck på knappen **Skicka** för att utfärda post-begäran. 

## <a name="6---create-the-skillset"></a>6 – skapa färdigheter 

Nästa steg är att ange färdigheter, som anger både de förbättringar som ska tillämpas och det kunskaps lager där resultatet ska lagras. Öppna fliken "skapa färdigheter" i Postman. Den här begäran skickar en placering till `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Ange `api-key`-och `Content-type`-huvuden som du har gjort tidigare. 

Det finns två stora objekt på översta nivån: `"skills"` och `"knowledgeStore"`. Varje objekt i `"skills"`-objektet är en anriknings tjänst. Varje anriknings tjänst har `"inputs"` och `"outputs"`. Observera att `LanguageDetectionSkill` har en utmatnings `targetName` av `"Language"`. Värdet för den här noden används av de flesta andra färdigheter som inmatade med källan som `document/Language`. Den här funktionen för att använda utdata från en nod som indata till en annan är ännu tydligare i `ShaperSkill`, som anger hur data ska flöda till kunskaps lagrets tabeller.

@No__t-0-objektet ansluts till lagrings kontot via variabeln `{{storage-connection-string}}` Postman. Sedan innehåller den en uppsättning mappningar mellan det förbättrade dokument och de tabeller och kolumner som kommer att vara tillgängliga i själva kunskaps lagret. 

Om du vill generera färdigheter ska du placera begäran genom att trycka på knappen **Skicka** i Postman.

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

## <a name="7---create-the-indexer"></a>7 – skapa indexeraren

Det sista steget är att skapa indexeraren, som faktiskt läser data och aktiverar färdigheter. I Postman växlar du till begäran om att "skapa indexerare" och granskar bröd texten. Som du kan se refererar definitionen av indexeraren till flera andra resurser som du redan har skapat – data källan, indexet och färdigheter. 

Objektet `"parameters/configuration"` styr hur indexeraren matar in data. I det här fallet finns indata i ett enda dokument med en rubrik rad och kommaavgränsade värden. Dokument nyckeln är en unik identifierare för dokumentet, som innan kodning är URL: en för käll dokumentet. Slutligen mappas färdigheter som språk kod, sentiment och nyckel fraser till lämpliga platser i dokumentet. Observera att även om det finns ett enda värde för `Language`, används `Sentiment` för varje element i matrisen med `pages`. `Keyphrases` är i sig en matris och används även för varje element i `pages`-matrisen.

När du har angett `api-key`-och `Content-type`-huvuden och bekräftat att bröd texten i begäran liknar käll koden som följer, trycker du på **Skicka** i Postman. Postman kommer att skicka begäran till `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Search skapar och kör indexeraren. 

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

## <a name="8---run-the-indexer"></a>8 – kör indexeraren 

I Azure Portal går du till Search Service **Översikt** och väljer fliken **indexerare** . Klicka på den **hotell-Review-IXR** som du skapade i föregående steg. Om indexeraren inte redan har körts trycker du på knappen **Kör** . Indexerings aktiviteten kan orsaka varningar som rör språk igenkänning när data innehåller vissa granskningar skrivna på språk som ännu inte stöds av kognitiva färdigheter. 

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data med kognitiva tjänster och projicerat resultaten i ett kunskaps lager, kan du använda Storage Explorer eller Power BI för att utforska din omfattande data uppsättning.

Information om hur du utforskar det här kunskaps lagret med hjälp av Storage Explorer finns i följande genom gång.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)

Information om hur du ansluter det här kunskaps lagret till Power BI finns i följande genom gång.

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)

Om du vill upprepa den här övningen eller testa en annan AI-förhands granskning tar du bort indexet *hotell reidxrs-* . Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll.
