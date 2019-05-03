---
title: Hur du kommer igång med Knowledge Store (förhandsversion) – Azure Search
description: Läs om stegen för att skicka avancerad och dokument som skapats i AI indexering pipelines i Azure Search i ett knowledge lager i Azure storage-kontot. Därifrån kan du visa, omformar och använda avancerad och dokument i Azure Search och i andra program.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 2a904cfb049af413887798c8aab449561bc2b73f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026972"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Hur du kommer igång med Knowledge Store

[Knowledge Store](knowledge-store-concept-intro.md) är en ny förhandsgranskningsfunktion i Azure Search som sparar AI enrichments som skapats i en pipeline för fulltextindexering för knowledge utvinning i andra appar. Du kan också använda sparade enrichments för att förstå och förfina en pipeline för fulltextindexering av Azure Search.

En knowledge store definieras av en kompetens. För vanliga scenarier för Azure Search-fulltextsökning, är syftet med en kompetens att tillhandahålla AI enrichments för att göra innehåll mer sökbara. Knowledge store scenarier kan skapa rollen för en kompetens och fylla i flera datastrukturer för knowledge utvinning.

I den här övningen börjar du med exempeldata, tjänster och verktyg för att lära dig det grundläggande arbetsflödet för att skapa och använda din första knowledge store, med betoning på kompetens definition.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster, verktyg och data som används i den här snabbstarten. 

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen. 

+ [Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempeldata. Din kunskap store kommer att finnas i Azure storage.

+ [Skapa en resurs för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) på S0-nivån för broad-spectrum åtkomst till en fullständig uppsättning färdigheter som används i AI enrichments för betala per användning.

+ [Skrivbordsappen postman](https://www.getpostman.com/) för att skicka begäranden till Azure Search.

+ [Postman-samling](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/caselaw) med förberedda begäranden för att skapa en datakälla, index, kompetens och indexerare. Flera objektdefinitioner är för lång tid att inkludera i den här artikeln. Du måste få den här samlingen att se index och kompetens definitioner i sin helhet.

+ [Caselaw exempeldata](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) från den [Caselaw Access-projekt](https://case.law/bulk/download/) hämtningssidan för offentliga stora mängder Data. Mer specifikt använder den här övningen de första 10 dokument för den första versionen (Arkansas). Vi laddade upp ett exempel på 10-dokumentet till GitHub för den här övningen.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

    ![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst.

## <a name="prepare-sample-data"></a>Förbereda exempeldata

1. [Logga in på Azure-portalen](https://portal.azure.com)navigerar du till ditt Azure storage-konto, klickar du på **Blobar**, och klicka sedan på **+ behållare**.

1. [Skapa en blobbehållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempeldata. Du kan ange offentlig åtkomstnivå till någon av dess giltiga värden.

1. När behållaren har skapats kan du öppna den och välj **överför** i kommandofältet.

   ![Ladda upp i kommandofältet](media/search-semi-structured-data/upload-command-bar.png "överför i kommandofältet")

1. Navigera till den mapp som innehåller den **caselaw sample.json** exempelfilen. Markera filen och klicka sedan på **överför**.


## <a name="set-up-postman"></a>Konfigurera Postman

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget, se [utforska Azure Search REST API: er med Postman](search-fiddler.md).

+ Begärandemetod för varje anrop i den här genomgången är **POST**.
+ Följande: begärandehuvuden (2) ”Content-type” inställd på ”application/json”, ”api-key” inställd på din ”admin key” (administratörsnyckeln är platshållare för den primära nyckeln för search) respektive. 
+ Begärandetexten är där du placerar det faktiska innehållet i samtalet. 

  ![Halvstrukturerad sökning](media/search-semi-structured-data/postmanoverview.png)

Vi använder Postman för att göra fyra API-anrop till din söktjänst, skapa en datakälla, ett index, en kompetens och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och JSON-data. Söktjänsten gör anslutningen när du importerar data.

[Skapa en kompetens](#create-skillset) är fokuset för den här genomgången: Anger berikande steg och hur data sparas i ett Arkiv för kunskap.

URL-slutpunkten måste ange en api-version och varje anrop ska returnera en **201 Skapad**. Förhandsversion av api-versionen för att skapa en kompetens med stöd för knowledge store är `2019-05-06-Preview`.

Kör följande API-anrop från REST-klienten.

## <a name="create-a-data-source"></a>Skapa en datakälla

Den [skapa API för Data källan](https://docs.microsoft.com/rest/api/searchservice/create-data-source) skapar ett Azure Search-objekt som anger vilka data som ska indexeras.

Slutpunkten för anropet är `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Ersätt `[service name]` med namnet på söktjänsten. 

2. Begärandetexten måste innehålla anslutning sträng- och blob-behållare namnet på ditt lagringskonto för det här anropet. Anslutningen finns i Azure-portalen i ditt lagringskonto **åtkomstnycklar**. 

   Se till att ersätta strängen och blob-behållare anslutningsnamnet i brödtexten i begäran innan du utför anropet.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. Skicka begäran. Svaret bör vara **201** och svarstexten bör vara nästan identiska med nyttolasten för begäran som du angav.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Skapa ett index
    
Det andra anropet är [Create Index-API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), skapa ett Azure Search-index som lagrar alla sökbara data. Ett index anger alla fält, parametrar och attribut.

Du behöver inte nödvändigtvis ett index för knowledge utvinning, men en indexerare inte körs, såvida inte ett index har angetts. 

URL: en för det här anropet är `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Ersätt `[service name]` med namnet på söktjänsten.

2. Kopiera indexdefinitionen i Create Index-begäran i Postman-samlingen i begärandetexten. Indexdefinitionen är flera hundra rader, för lång tid att skriva ut här. 

   Yttre shell av ett index består av följande element. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. Den `fields` samlingen innehåller stora mängd indexdefinitionen. Den innehåller enkla fält [komplexa fält](search-howto-complex-data-types.md) med kapslade underordnade strukturer och samlingar.

   Granska fältdefinition för `casebody` på linjer 302-384. Observera att ett komplexa fält kan innehålla andra komplexa fält när det behövs hierarkisk garantier.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. Skicka begäran. 

   Svaret bör vara **201** och likna följande exempel som visar de första flera fält:

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Skapa en kompetens och kunskap butik

Den [skapa kompetens API](https://docs.microsoft.com/rest/api/searchservice/create-skillset) skapar ett Azure Search-objekt som anger vilka kognitiva kunskaper för att anropa, hur du kopplar kunskaper tillsammans och viktigast av allt i den här genomgången – så här anger du ett knowledge Arkiv.

Slutpunkten för anropet är `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Ersätt `[service name]` med namnet på söktjänsten.

2. Kopiera kompetens definitionen i Skapa kompetens begäran i Postman-samlingen i begärandetexten. Kompetens-definitionen är flera hundra rader, för lång tid att skriva ut här, men det är fokus i den här genomgången.

   Yttre shell på en kompetens består av följande element. Den `skills` samling definierar InMemory--enrichments men `knowledgeStore` definition anger hur utdata lagras. Den `cognitiveServices` definitionen är anslutningen till AI berikande motorerna.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Ställ först in `cognitiveServices` och `knowledgeStore` nyckel och anslutningssträngen. I det här exemplet finns de här strängarna efter kompetens definitionen, fram till slutet av begärandetexten.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "<your cognitive services resource name>",
        "key": "<your cognitive services key>"
    },
    "knowledgeStore": {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your storage account key>;EndpointSuffix=core.windows.net",
    ```

3. Granska samlingen kunskaper, i synnerhet formaren färdigheter på raderna 85 och 170, respektive. Formaren färdighet är viktig eftersom den monterar datastrukturer som du vill använda för knowledge utvinningsstrukturen. Dessa strukturer är i minnet enbart under kompetens körning, men när du flyttar till nästa steg, ser du hur du kan spara dessa utdata för en kunskap för ytterligare utforskning.

   Följande kodfragment är från rad 207. 

    ```json
    {
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
             ]
          }
     ]
   }
   . . .
   ```

3. Granska den `projections` element i `knowledgeStore`, från på rad 253. Projektioner ange information store-sammansättning. Projektioner har angetts i par tabeller-objekt, men för närvarande bara en tidpunkt. Som du ser i den första projektionen, `tables` har angetts men `objects` är inte. I andra är det motsatsen.

   Tabeller kommer att skapas i Table storage för varje tabell som du skapar i Azure storage, och varje objekt får en behållare i Blob storage.

   Objekt innehåller vanligtvis fullständig uttrycket för en berikande. Tabeller innehåller vanligtvis partiella enrichments i kombinationer som du ordnar för specifika ändamål. Det här exemplet visar en tabell i fall, men visas inte finns andra tabeller som entiteter, Domarna och yttranden.

    ```json
    "projections": [
    {
        "tables": [
            {
              "tableName": "Opinions",
              "generatedKeyName": "OpinionId",
              "source": "/document/Case/OpinionsSnippets/*"
            },
          . . . 
        ],
        "objects": []
    },
    {
        "tables": [],
        "objects": [
            {
                "storageContainer": "enrichedcases",
                "key": "/document/CaseFull/Id",
                "source": "/document/CaseFull"
            }
          ]
        }
      ]
    }
    ```

5. Skicka begäran. Svaret bör vara **201** och likna följande exempel som visar den första delen av svaret.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Skapa och köra en indexerare

Den [skapa Indexer API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) skapas och körs omedelbart en indexerare. Alla de definitioner som du har skapat sätts i rörelse med det här steget. Indexeraren körs direkt eftersom det inte finns i tjänsten. När det finns är ett INLÄGG anrop till en befintlig indexerare en uppdateringsåtgärd.

Slutpunkten för anropet är `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Ersätt `[service name]` med namnet på söktjänsten. 

2. Begärandetexten anger indexeraren namnet för det här anropet. En datakälla och index krävs av indexeraren. En kompetens är valfritt för en indexerare, men krävs för AI-funktioner.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. Skicka begäran. Svaret bör vara **201** och svarstexten bör vara nästan identiska med nyttolasten i begäran du angav (tas bort av utrymmesskäl).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Utforska knowledge store

Du kan börja utforska när det första dokumentet har importerats. Den här uppgiften ska använda [ **Lagringsutforskaren** ](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) i portalen.

Det är viktigt att tänka på att en knowledge store är helt frånkopplad från Azure Search. Azure Search-index och kunskap store båda innehåller datarepresentation och innehåll, men en del sätt därifrån. Använda indexet för fulltextsökning, filtrerad sökning och alla scenarier som stöds i Azure Search. Eller gå vidare med bara din kunskap store, bifoga andra verktyg för att analysera innehållet.

## <a name="takeaways"></a>Lärdomar

Du har nu skapat din första knowledge store i Azure storage och använde Storage Explorer för att visa enrichments. Det här är den grundläggande upplevelsen för att arbeta med lagrade enrichments. 

## <a name="next-steps"></a>Nästa steg

Formaren färdighet gör tunga jobb på att skapa detaljerade formulär som kan kombineras till nya figurer. Granska referenssidan för den här kunskap för information om hur de används i nästa steg.

> [!div class="nextstepaction"]
> [Formaren färdighet referens](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->