---
title: Hur att komma igång med knowledge utvinningsmodellen (förhandsversion) – Azure Search
description: Läs om stegen för att skicka avancerad och dokument som skapats i AI indexering pipelines i Azure Search i ett knowledge lager i Azure storage-kontot. Därifrån kan du visa, omformar och använda avancerad och dokument i Azure Search och i andra program.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: e50dfcdc5ac2fbe2435066546a340874e1b8f682
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551057"
---
# <a name="how-to-get-started-with-knowledge-mining-in-azure-search"></a>Hur du kommer igång med knowledge utvinning i Azure Search

> [!Note]
> Knowledge store är i förhandsversion och inte är avsett för användning i produktion. Den [REST API-version 2019-05-06-Preview](search-api-preview.md) ger den här funktionen. Det finns inget stöd för .NET SDK just nu.
>
[Knowledge store](knowledge-store-concept-intro.md) sparar AI-berikats dokument som skapats under indexering till Azure storage-kontot för underordnade knowledge utvinning i andra appar. Du kan också använda sparade enrichments för att förstå och förfina en pipeline för fulltextindexering av Azure Search. 

En knowledge store definieras av en *kompetens* och skapats av en *indexeraren*. Fysiska uttrycket för en knowledge butik har angetts via *projektioner* som fastställer datastrukturer i lagring. Du har skapat alla dessa objekt när du är klar med den här genomgången, och du vet hur de fungerar ihop. 

I den här övningen börjar du med exempeldata, tjänster och verktyg för att lära dig det grundläggande arbetsflödet för att skapa och använda din första knowledge store, med betoning på kompetens definition.

## <a name="prerequisites"></a>Förutsättningar

Knowledge store är i mitten av flera tjänster med Azure Blob storage och Azure Table storage tillhandahåller fysisk lagring och Azure Search och Cognitive Services för objektskapande och uppdateringar. Bekant med den [grundläggande arkitektur](knowledge-store-concept-intro.md) krävs för att den här genomgången.

Följande tjänster och verktyg som används i den här snabbstarten. 

+ [Hämta skrivbordsappen Postman](https://www.getpostman.com/), som används för att skicka HTTP-begäranden till Azure Search.

+ [Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempeldata och kunskap lagra. Din kunskap store kommer att finnas i Azure storage.

+ [Skapa en resurs för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) på S0-nivån för broad-spectrum åtkomst till en fullständig uppsättning färdigheter som används i AI enrichments för betala per användning. Cognitive Services och Azure Search-tjänsten måste vara i samma region.

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen. 

Exempel-JSON-dokument och Postman collection-filen måste också anges. Anvisningar om hur hittar och läser in aviserar om ytterligare filer finns i den [förbereda exempeldata](#prepare-sample-data) avsnittet.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

    ![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. Du ger tjänstnamnet och API-nyckel i varje HTTP-begäran i följande avsnitt.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Förbereda exempeldata

En knowledge-arkivet innehåller utdata från en berikande pipeline. Indata består av ”oanvändbara” data som slutligen blir ”användbara” eftersom processen omfattar hela pipelinen. Exempel på oanvändbara data kan innehålla bildfiler som måste analyseras efter text eller avbildning egenskaper eller kompakta textfiler som kan analyseras för entiteter, nyckelfraser och sentiment. 

Den här övningen använder kompakta textfiler (fallet lag information) som kommer från den [Caselaw Access-projekt](https://case.law/bulk/download/) hämtningssidan för offentliga stora mängder Data. Vi laddade upp ett exempel på 10-dokumentet till GitHub för den här övningen. 

I det här steget skapar du en Azure Blob-behållare för dessa dokument som ska användas som indata till pipelinen. 

1. Ladda ned och extrahera den [exempeldata i Azure Search](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) lagringsplatsen för att hämta den [Caselaw datauppsättning](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Logga in på Azure-portalen](https://portal.azure.com)navigerar du till ditt Azure storage-konto, klickar du på **Blobar**, och klicka sedan på **+ behållare**.

1. [Skapa en blobbehållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempeldata: 

   1. Namnge behållaren `caselaw-test`. 
   
   1. Ange offentlig åtkomst till någon av dess giltiga värden.

1. När behållaren har skapats kan du öppna den och välj **överför** i kommandofältet.

   ![Ladda upp i kommandofältet](media/search-semi-structured-data/upload-command-bar.png "överför i kommandofältet")

1. Navigera till den mapp som innehåller den **caselaw sample.json** exempelfilen. Markera filen och klicka sedan på **överför**.

1. När du arbetar med Azure storage får du anslutningsnamn sträng och en behållare.  Du måste båda de här strängarna i [Skapa datakälla](#create-data-source):

   1. I översiktssidan, klickar du på **åtkomstnycklar** och kopiera en *anslutningssträngen*. Det börjar med `DefaultEndpointsProtocol=https;` och avslutas med `EndpointSuffix=core.windows.net`. Ditt kontonamn och nyckel är däremellan. 

   1. Behållarens namn bör vara `caselaw-test` eller valfritt namn som du har tilldelat.



## <a name="set-up-postman"></a>Konfigurera Postman

Postman är klientappen som du använder för att skicka begäran och JSON-dokument till Azure Search. Flera av begäranden kan formuleras med data i den här artikeln. Dock innehålla två av de största förfrågningar (skapa ett index som skapar en kompetens) utförlig JSON som är för stor för att bädda in i en artikel. 

För att göra alla JSON-dokument och begäranden fullt tillgänglig har skapat vi Postman collection-filen. Ladda ned och sedan importera den här filen är din första uppgift att konfigurera klienten.

1. Hämta och packa upp den [Azure Search Postman exempel](https://github.com/Azure-Samples/azure-search-postman-samples) lagringsplats.

1. Starta Postman och importera Caselaw Postman-samlingen:

   1. Klicka på **Import** > **importera filer** > **Välj filer**. 

   1. Navigera till mappen \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Välj **Caselaw.postman_collection_v2.json**. Du bör se fyra **POST** begäranden i samlingen.

   ![Postman-samling för Caselaw demo](media/knowledge-store-howto/postman-collection.png "Postman-samling för Caselaw demo")
   

## <a name="create-an-index"></a>Skapa ett index
    
Den första begäran använder den [Create Index-API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), skapa ett Azure Search-index som lagrar alla sökbara data. Ett index anger alla fält, parametrar och attribut.

Du behöver inte nödvändigtvis ett index för knowledge utvinning, men en indexerare inte körs, såvida inte ett index har angetts. 

1. I URL: en `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`, Ersätt `YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din söktjänst. 

1. Under rubriken Ersätt `<YOUR AZURE SEARCH ADMIN API-KEY>` med en administrations-API-nyckel för Azure Search.

1. I avsnittet brödtext är JSON-dokumentet ett indexschema. Komprimerat för synlighet yttre shell av ett index som består av följande element. Fältsamlingen motsvarar fält i datauppsättningen caselaw.

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

1. Expandera den `fields` samling. Den innehåller stora mängd indexdefinitionen, består av enkla fält [komplexa fält](search-howto-complex-data-types.md) med kapslade underordnade strukturer och samlingar.

   Ta en stund att granska fältdefinition för den `casebody` komplexa fältet på rader 302-384. Observera att ett komplexa fält kan innehålla andra komplexa fält när det behövs hierarkisk garantier. Hierarkisk strukturer kan utformas i ett index som visas här och även som en projektion i en kompetens därför skapa en kapslad datastruktur i arkivet kunskap.

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

1. Klicka på **skicka** att utföra begäran.  Du bör få en **Status: 201 Skapad** meddelandet som ett svar.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Skapa en datakälla

Andra begäran använder den [skapa API för Data källan](https://docs.microsoft.com/rest/api/searchservice/create-data-source) att ansluta till Azure Blob storage. 

1. I URL: en `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`, Ersätt `YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din söktjänst. 

1. Under rubriken Ersätt `<YOUR AZURE SEARCH ADMIN API-KEY>` med en administrations-API-nyckel för Azure Search.

1. I avsnittet brödtext innehåller JSON-dokumentet anslutning sträng- och blob-behållare namnet på ditt lagringskonto. Anslutningssträngen kan hittas i Azure-portalen i ditt lagringskonto **åtkomstnycklar**. 

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

1. Klicka på **skicka** att utföra begäran.  Du bör få en **Status: 201 Skapad** meddelandet som ett svar.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Skapa en kompetens och kunskap butik

Den tredje begäran använder den [skapa kompetens API](https://docs.microsoft.com/rest/api/searchservice/create-skillset), skapa ett Azure Search-objekt som anger vilka kognitiva kunskaper för att anropa, hur du kopplar kunskaper tillsammans och viktigast av allt i den här genomgången - så här anger du ett knowledge Arkiv.

1. I URL: en `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`, Ersätt `YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din söktjänst. 

1. Under rubriken Ersätt `<YOUR AZURE SEARCH ADMIN API-KEY>` med en administrations-API-nyckel för Azure Search.

1. I avsnittet brödtext är JSON-dokumentet en kompetens-definition. Komprimerat för synlighet består i yttre en kompetens-gränssnittet av följande element. Den `skills` samling definierar InMemory--enrichments men `knowledgeStore` definition anger hur utdata lagras. Den `cognitiveServices` definitionen är anslutningen till AI berikande motorerna.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Expandera `cognitiveServices` och `knowledgeStore` så att du kan ange anslutningsinformation. I det här exemplet finns de här strängarna efter kompetens definitionen, fram till slutet av begärandetexten. 

   För `cognitiveServices`, etablera en resurs på S0-nivån finns i samma region som Azure Search. Du kan hämta cognitiveServices-namnet och nyckeln från samma sida i Azure-portalen. 
   
   För `knowledgeStore`, du kan använda samma anslutningssträng som används för caselaw Blob-behållare.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Expandera samlingen kunskaper, i synnerhet formaren färdigheter på raderna 85 och 179, respektive. Formaren färdighet är viktig eftersom den monterar datastrukturer som du vill använda för knowledge utvinningsstrukturen. Dessa strukturer är i minnet enbart under kompetens körning, men när du flyttar till nästa steg, ser du hur du kan spara dessa utdata för en kunskap för ytterligare utforskning.

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

1. Expandera `projections` element i `knowledgeStore`, från på rad 262. Projektioner ange information store-sammansättning. Projektioner har angetts i par tabeller-objekt, men för närvarande bara en tidpunkt. Som du ser i den första projektionen, `tables` har angetts men `objects` är inte. I andra är det motsatsen.

   Tabeller kommer att skapas i Table storage för varje tabell som du skapar i Azure storage, och varje objekt får en behållare i Blob storage.

   BLOB-objekt innehåller vanligtvis fullständig uttrycket för en berikande. Tabeller innehåller vanligtvis partiella enrichments i kombinationer som du ordnar för specifika ändamål. Det här exemplet visar tabellerna fall och en yttranden, men visas inte finns andra tabeller som entiteter, advokater, Domarna och parter.

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

1. Klicka på **skicka** att utföra begäran. Svaret bör vara **201** och likna följande exempel som visar den första delen av svaret.

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

Den fjärde begäran använder den [skapa Indexer API](https://docs.microsoft.com/rest/api/searchservice/create-indexer), skapa en Azure Search-indexerare. En indexerare är motorn för körning av för indexering av pipeline. Alla de definitioner som du har skapat sätts i rörelse med det här steget.

1. I URL: en `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`, Ersätt `YOUR-AZURE-SEARCH-SERVICE-NAME` med namnet på din söktjänst. 

1. Under rubriken Ersätt `<YOUR AZURE SEARCH ADMIN API-KEY>` med en administrations-API-nyckel för Azure Search.

1. I avsnittet brödtext namnet JSON-dokumentet indexerare. En datakälla och index krävs av indexeraren. En kompetens är valfritt för en indexerare, men krävs för AI-funktioner.

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

1. Expandera outputFieldMappings. Till skillnad från fieldMappings som används för anpassad mappning mellan fält i en datakälla och fält i ett index, outputFieldMappings används för att mappa avancerad och fält, skapas och konfigureras av pipelinen till utdatafält i ett index eller en projektion.

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

1. Klicka på **skicka** att utföra begäran. Svaret bör vara **201** och svarstexten bör vara nästan identiska med nyttolasten i begäran du angav (tas bort av utrymmesskäl).

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
