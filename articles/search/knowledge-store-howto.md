---
title: Komma igång med Knowledge Store (för hands version) – Azure Search
description: Lär dig stegen för att skicka berikade dokument som skapats av AI indexerings pipelines i Azure Search till ett kunskaps lager på ditt Azure Storage-konto. Därifrån kan du Visa, ändra form på och använda berikade dokument i Azure Search och i andra program.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: d6cecdce9bc4f7fee9ec936ac73b6accc77084c4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648826"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Så här kommer du igång med kunskaps lager i Azure Search

> [!Note]
> Kunskaps lagret är i för hands version och är inte avsett för användning i produktion. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>
[Kunskaps lager](knowledge-store-concept-intro.md) sparar AI-berikade dokument som skapas vid indexering till ditt Azure Storage-konto för överordnad Data utvinning i andra appar. Du kan också använda sparade berikare för att förstå och förfina en Azure Search indexerings pipeline. 

Ett kunskaps lager definieras av en *färdigheter* och skapas av en *indexerare*. Det fysiska uttrycket för ett kunskaps lager anges genom *projektioner* som fastställer data strukturerna i lagringen. När du är klar med den här genom gången har du skapat alla dessa objekt och du vet hur de passar ihop. 

I den här övningen börjar du med exempel data, tjänster och verktyg för att lära dig det grundläggande arbets flödet för att skapa och använda ditt första kunskaps lager, med tonvikt på färdigheter-definition.

## <a name="prerequisites"></a>Förutsättningar

Kunskaps lagret finns i mitten av flera tjänster, med Azure Blob Storage och Azure Table Storage som tillhandahåller fysisk lagring och Azure Search och Cognitive Services för att skapa och uppdatera objekt. Den [grundläggande arkitekturen](knowledge-store-concept-intro.md) är en förutsättning för den här genom gången.

Följande tjänster och verktyg används i den här snabb starten. 

+ [Hämta Postman Desktop-appen](https://www.getpostman.com/)som används för att skicka HTTP-begäranden till Azure Search.

+ [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data och kunskaps lagret. Ditt kunskaps lager kommer att finnas i Azure Storage.

+ [Skapa en Cognitive Services resurs](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) på S0-nivån betala per användning för bred spektrum till gång till en fullständig uppsättning kunskaper som används i AI-anrikninger. Cognitive Services och Azure Searchs tjänsten måste vara i samma region.

+ [Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien. 

Exempel på JSON-dokument och en Postman-samlings fil krävs också. Instruktioner för att hitta och läsa in kompletterande filer finns i avsnittet [förbereda exempel data](#prepare-sample-data) .

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar** > **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

    ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. Du får tjänst namnet och API-nyckeln i varje HTTP-begäran i följande avsnitt.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Förbereda exempel data

Ett kunskaps lager innehåller resultatet av en anriknings pipeline. Indata består av "oanvändbara" data som slutligen blir "användbara" när de fortskrider genom pipelinen. Exempel på oanvändbara data kan vara bildfiler som måste analyseras för text-eller bild egenskaper, eller kompakta textfiler som kan analyseras för entiteter, nyckel fraser eller sentiment. 

I den här övningen används kompakta textfiler (juridisk information) som kommer från sidan [Caselaw Access Project](https://case.law/bulk/download/) Public bulk data Download. Vi har laddat upp ett 10-dokument-exempel till GitHub för den här övningen. 

I den här uppgiften skapar du en Azure Blob-behållare för de här dokumenten som ska användas som inmatade i pipelinen. 

1. Hämta och extrahera [Azure Search](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) data lagrings plats för att hämta [Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw)-datauppsättningen. 

1. [Logga](https://portal.azure.com)in på Azure Portal, navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempel data: 

   1. Namnge behållaren `caselaw-test`. 
   
   1. Ange den offentliga åtkomst nivån till något av dess giltiga värden.

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet.

   ![Ladda upp i kommando fältet](media/search-semi-structured-data/upload-command-bar.png "Ladda upp i kommando fältet")

1. Navigera till mappen som innehåller exempel filen **caselaw-Sample. JSON** . Markera filen och klicka sedan på **överför**.

1. När du är i Azure Storage hämtar du anslutnings strängen och behållar namnet.  Du behöver båda de här strängarna i [skapa data källa](#create-data-source):

   1. På sidan Översikt klickar du på **åtkomst nycklar** och kopierar en *anslutnings sträng*. Den börjar med `DefaultEndpointsProtocol=https;` och avslutas med. `EndpointSuffix=core.windows.net` Ditt konto namn och din nyckel är mellan. 

   1. Behållar namnet måste `caselaw-test` vara eller det namn som du har tilldelat.



## <a name="set-up-postman"></a>Konfigurera Postman

Postman är den klient app som du använder för att skicka begär Anden och JSON-dokument till Azure Search. Flera av de begär Anden kan formuleras med hjälp av bara informationen i den här artikeln. Två av de största begär Anden (skapa ett index som skapar en färdigheter) inkluderar dock utförlig JSON som är för stor för att bäddas in i en artikel. 

För att göra alla JSON-dokument och-förfrågningar fullständigt tillgängliga skapade vi en Postman-samlings fil. Att ladda ned och importera den här filen är din första uppgift när du konfigurerar klienten.

1. Ladda ned och zippa upp [Azure Search Postman samples](https://github.com/Azure-Samples/azure-search-postman-samples) -lagringsplatsen.

1. Starta Postman och importera Caselaw Postman-samlingen:

   1. Klicka på **Importera** > **importfiler filer** > **Välj filer**. 

   1. Navigera till mappen \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw

   1. Välj **Caselaw. postman_collection_v2. JSON**. Du bör se fyra **post** -förfrågningar i samlingen.

   ![Postman-samling för Caselaw-demo](media/knowledge-store-howto/postman-collection.png "Postman-samling för Caselaw-demo")
   

## <a name="create-an-index"></a>Skapa ett index
    
Den första begäran använder [API för Create index](https://docs.microsoft.com/rest/api/searchservice/create-data-source)och skapar ett Azure Search-index som lagrar alla sökbara data. Ett index anger alla fält, parametrar och attribut.

Du behöver inte nödvändigt vis ett index för kunskaps utvinning, men en indexerare körs inte om inte ett index har angetts. 

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview` Ersätt`YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din Sök tjänst i URL: en. 

1. I rubrik-avsnittet ersätter `<YOUR AZURE SEARCH ADMIN API-KEY>` du med en administratörs-API-nyckel för Azure Search.

1. I avsnittet brödtext är JSON-dokumentet ett index schema. Komprimerad för synlighet består det yttre gränssnittet i ett index av följande element. Fält samlingen motsvarar fält i data uppsättningen caselaw.

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

1. `fields` Expandera samlingen. Den innehåller en samling index definition, bestående av enkla fält, [komplexa fält](search-howto-complex-data-types.md) med kapslade under strukturer och samlingar.

   Ägna en stund åt att granska fält definitionen för det `casebody` komplexa fältet på raderna 302-384. Observera att ett komplext fält kan innehålla andra komplexa fält när hierarkiska representationer behövs. Hierarkiska strukturer kan modelleras i ett index, som du ser här och också som en projektion i en färdigheter, vilket skapar en kapslad data struktur i kunskaps lagret.

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

1. Klicka på **Skicka** för att köra begäran.  Du bör få en **status: 201 skapade** meddelandet som ett svar.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Skapa en datakälla

Den andra begäran använder [API: et för att skapa data källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source) för att ansluta till Azure Blob Storage. 

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview` Ersätt`YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din Sök tjänst i URL: en. 

1. I rubrik-avsnittet ersätter `<YOUR AZURE SEARCH ADMIN API-KEY>` du med en administratörs-API-nyckel för Azure Search.

1. I avsnittet brödtext innehåller JSON-dokumentet lagrings kontots anslutnings sträng och namn på BLOB-behållare. Du hittar anslutnings strängen i Azure Portal i lagrings kontots **åtkomst nycklar**. 

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

1. Klicka på **Skicka** för att köra begäran.  Du bör få en **status: 201 skapade** meddelandet som ett svar.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Skapa en färdigheter och ett kunskaps lager

Den tredje förfrågningen använder [create färdigheter-API: et](https://docs.microsoft.com/rest/api/searchservice/create-skillset)för att skapa ett Azure Search-objekt som anger vilka kognitiva kunskaper som ska anropas, hur man kedjar kunskaper tillsammans och viktigast för den här genom gången – hur man anger ett kunskaps lager.

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview` Ersätt`YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din Sök tjänst i URL: en. 

1. I rubrik-avsnittet ersätter `<YOUR AZURE SEARCH ADMIN API-KEY>` du med en administratörs-API-nyckel för Azure Search.

1. I avsnittet brödtext är JSON-dokumentet en färdigheter-definition. Komprimerad för synlighet, det yttre gränssnittet i en färdigheter består av följande element. Samlingen definierar berikade minnes brist, `knowledgeStore` men definitionen anger hur utdata lagras. `skills` `cognitiveServices` Definitionen är din anslutning till AI-anriknings motorer.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Expandera `cognitiveServices` och`knowledgeStore` så att du kan ange anslutnings information. I exemplet finns de här strängarna efter färdigheter-definitionen, mot slutet av begär ande texten. 

   För `cognitiveServices`etablerar du en resurs på S0-nivån, som finns i samma region som Azure Search. Du kan hämta cognitiveServices namn och nyckel från samma sida i Azure Portal. 
   
   För `knowledgeStore`kan du använda samma anslutnings sträng som används för caselaw-BLOB-behållaren.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Expandera kunskaps samlingen, särskilt formaren-kunskaper på raderna 85 respektive 179. Formaren-kompetensen är viktig eftersom den sätter samman de data strukturer som du vill använda för kunskaps utvinning. Under färdigheter-körningen är dessa strukturer endast i minnet, men när du går vidare till nästa steg får du se hur dessa utdata kan sparas i ett kunskaps lager för ytterligare utforskning.

   Följande kodfragment är från rad 217. 

    ```json
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
   . . .
   ```

1. Expandera `projections` element i `knowledgeStore`, från och med rad 262. Projektioner anger kunskaps lager kompositionen. Projektioner anges i tabeller-objekt-par, men för närvarande bara en i taget. Som du kan se i den första projektionen anges `tables` det, men `objects` är inte. I det andra är det tvärtom.

   I Azure Storage kommer tabeller att skapas i Table Storage för varje tabell som du skapar, och varje objekt får en behållare i Blob Storage.

   BLOB-objekt innehåller vanligt vis ett fullständigt uttryck för en anrikning. Tabeller innehåller vanligt vis delvis berikade delar, i kombinationer som du ordnar för ett särskilt syfte. I det här exemplet visas en Cases-tabell och en yttrande tabell, men visas inte andra tabeller som entiteter, jurister, domare och parter.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

1. Klicka på **Skicka** för att köra begäran. Svaret ska vara **201** och se ut ungefär som i följande exempel, som visar den första delen av svaret.

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

Den fjärde begäran använder [create Indexer-API: et](https://docs.microsoft.com/rest/api/searchservice/create-indexer)för att skapa en Azure Search indexerare. En indexerare är körnings motor för indexerings pipelinen. Alla definitioner som du har skapat hittills placeras i rörelse med det här steget.

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview` Ersätt`YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din Sök tjänst i URL: en. 

1. I rubrik-avsnittet ersätter `<YOUR AZURE SEARCH ADMIN API-KEY>` du med en administratörs-API-nyckel för Azure Search.

1. I avsnittet brödtext anger JSON-dokumentet namnet på indexeraren. En data källa och ett index krävs av indexeraren. En färdigheter är valfri för en indexerare, men krävs för AI-anrikning.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    ```

1. Expandera outputFieldMappings. Till skillnad från fieldMappings, som används för Anpassad mappning mellan fält i en data källa och fält i ett index, används outputFieldMappings för att mappa berikade fält, som skapas och fylls i pipeline, för att generera fält i ett index eller en projektion.

    ```json
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
    ```

1. Klicka på **Skicka** för att köra begäran. Svaret ska vara **201** och svars texten bör se ut ungefär som en begäran om nytto Last (rensad för det kortfattat).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    }
    ```

## <a name="explore-knowledge-store"></a>Utforska kunskaps lagret

Du kan börja utforska så snart det första dokumentet har importer ATS. För den här uppgiften använder du [**Storage Explorer**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) i portalen.

Det är viktigt att du inser att ett kunskaps lager är helt frånkopplat från Azure Search. Azure Search indexet och kunskaps lagret innehåller både data representation och innehåll, men delar från där. Använd indexet för full texts ökning, filtrerad sökning och alla scenarier som stöds i Azure Search. Du kan också gå vidare med ditt kunskaps lager och bifoga andra verktyg för att analysera innehållet.

## <a name="takeaways"></a>Lärdomar

Nu har du skapat ditt första kunskaps lager i Azure Storage och använt Storage Explorer för att visa de här berikarna. Detta är den grundläggande upplevelsen för att arbeta med lagrade berikare. 

## <a name="next-steps"></a>Nästa steg

Formaren-kompetensen gör det kraftigt att skapa detaljerade data formulär som kan kombineras till nya former. I nästa steg ska du gå till referens sidan för den här kunskapen för information om hur den används.

> [!div class="nextstepaction"]
> [Formaren-färdighets referens](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
