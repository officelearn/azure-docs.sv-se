---
title: 'Självstudie: skapa en färdigheter i python med hjälp av REST API: er'
titleSuffix: Azure Cognitive Search
description: Stega genom ett exempel på data extrahering, naturligt språk och bild-AI-bearbetning i Azure Kognitiv sökning med en Jupyter python-anteckningsbok. Extraherade data indexeras och lätt nås av frågan.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d9ae7f4b7dd8b0f45ae02bd2a90aca78127fd3d3
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472408"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>Självstudie: skapa en pipeline för AI-anrikning med REST och python

I den här självstudien får du lära dig Mechanics programmerings data i Azure Kognitiv sökning med *kognitiva kunskaper*. Färdigheter backas upp av NLP (Natural Language Processing) och bild analys funktioner i Cognitive Services. Genom färdigheter komposition och konfiguration kan du extrahera text-och text representationer av en bild eller skannad dokument fil. Du kan också identifiera språk, entiteter, nyckel fraser och mycket annat. Resultatet är omfattande ytterligare innehåll i ett sökindex som skapats med AI-anrikninger i en indexerings pipeline. 

I den här självstudien använder du python för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en indexeringspipeline som berikar källdata på väg till ett index
> * Använd inbyggda färdigheter: entitetsigenkänning, språkidentifiering, textredigering och extrahering av viktiga fraser
> * Lära dig att sammanlänka kunskaper genom att mappa indata till utdata i en kompetens
> * Köra begäranden och granska resultatet
> * Återställa index och indexerare för ytterligare utveckling

Output är ett fullständigt text sökbart index i Azure Kognitiv sökning. Du kan förbättra indexet med andra standardfunktioner som [synonymer](search-synonyms.md), [bedömningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analysverktyg](search-analyzers.md) och [filter](search-filters.md). 

Den här självstudien körs på den kostnads fria tjänsten, men antalet kostnads fria transaktioner är begränsat till 20 dokument per dag. Om du vill köra den här kursen mer än en gång på samma dag tar du bort indexeraren för att återställa räknaren.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://go.microsoft.com/fwlink/?linkid=2042400).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data används i den här självstudien. 

+ [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data. Kontrol lera att lagrings kontot finns i samma region som Azure Kognitiv sökning.

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section)som tillhandahåller python 3. x-och Jupyter-anteckningsböcker.

+ [Exempel data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) består av en liten fil uppsättning av olika typer. 

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

Om du vill interagera med din Azure Kognitiv sökning-tjänst behöver du tjänst-URL: en och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**får du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende per begäran mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-data"></a>Förbereda exempel data

Berikningspipelinen hämtar data från Azure-datakällor. Källdata måste härstamma från en typ av data källa som stöds för en [Azure kognitiv sökning-indexerare](search-indexer-overview.md). I den här övningen använder vi blogglagring för att demonstrera flera typer av innehåll.

1. [Logga](https://portal.azure.com)in på Azure Portal, navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempel data. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden.

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet för att ladda upp exempelfilerna som du laddade ned i föregående steg.

   ![Källfiler i Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. När exempelfilerna har lästs in hämtar du containerns namn och en anslutningssträng för Blob Storage. Det kan du göra genom att gå till lagringskontot i Azure Portal. Klicka på **åtkomst nycklar**och kopiera sedan fältet **anslutnings sträng** .

Anslutnings strängen har följande format: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Håll anslutnings strängen praktisk. Du behöver den i ett kommande steg.

Det finns andra sätt att ange anslutningssträngen, till exempel att ange en signatur för delad åtkomst. Om du vill veta mer om autentiseringsuppgifter för datakällor kan du läsa [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Indexera Azure Blob Storage).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

> [!Note]
> Den här artikeln visar hur du skapar en data källa, index, indexerare och färdigheter med hjälp av en serie Python-skript. Om du vill ladda ned hela Notebook-exemplet går du till [lagrings platsen för Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Använd Anaconda Navigator för att starta Jupyter Notebook och skapa en ny python 3-anteckningsbok.

## <a name="connect-to-azure-cognitive-search"></a>Ansluta till Azure Kognitiv sökning

I din bärbara dator kör du det här skriptet för att läsa in de bibliotek som används för att arbeta med JSON och utforma HTTP-begäranden.

```python
import json
import requests
from pprint import pprint
```

Definiera sedan namnen på data källan, indexet, Indexer och färdigheter. Kör det här skriptet för att konfigurera namnen för den här självstudien.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> I en kostnads fri tjänst är du begränsad till tre index, indexerare och data källor. I den här kursen skapar du en av varje. Se till att du har plats för att skapa nya objekt innan du fortsätter.

I följande skript ersätter du plats hållarna för Sök tjänsten (ditt-SEARCH-SERVICE-NAME) och Admin API-nyckeln (din-ADMIN-API-nyckel) och kör sedan den för att konfigurera Sök tjänstens slut punkt.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Skapa en datakälla

Nu när dina tjänster och källfiler är förberedda kan du börja samla in komponenterna för din indexeringspipeline. Börja med ett data käll objekt som talar om för Azure Kognitiv sökning hur du hämtar externa källdata.

I följande skript ersätter du plats hållaren YOUR-BLOB-RESOURCE-STRING med anslutnings strängen för blobben som du skapade i föregående steg. Kör sedan skriptet för att skapa en data källa med namnet `cogsrch-py-datasource`.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera status koden 201 som bekräftar att det lyckades.

På sidan för Sök tjänstens instrument panel i Azure Portal kontrollerar du att cogsrch-py-DataSource visas i listan **data källor** . Klicka på **Uppdatera** för att uppdatera sidan.

![Panelen data källor i portalen](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Panelen data källor i portalen")

## <a name="create-a-skillset"></a>Skapa en kunskapsuppsättning

I det här steget ska du definiera en uppsättning med anriknings steg som ska tillämpas på dina data. Du kallar varje berikande steg för en *kunskap*, och uppsättningen med berikande steg för en *kunskapsuppsättning*. I den här självstudien används [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) för färdigheter:

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Textuppdelning](cognitive-search-skill-textsplit.md) för att dela upp stort innehåll i mindre delar innan du anropar färdigheten extrahering av nyckelfraser. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Enhets igenkänning](cognitive-search-skill-entity-recognition.md) för extrahering av namn på organisationer från innehåll i BLOB-behållaren.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna. 

### <a name="python-script"></a>Python-skript
Kör följande skript för att skapa en färdigheter som heter `cogsrch-py-skillset`.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera status koden 201 som bekräftar att det lyckades.

Extraherings kunskaper för nyckel fraser används för varje sida. Genom att ställa in kontexten på `"document/pages/*"`kör du den här berikaren för varje medlem i matrisen dokument/sidor (för varje sida i dokumentet).

Varje kunskap körs på innehållet i dokumentet. Under bearbetningen kommer Azure Kognitiv sökning att knäcka varje dokument för att läsa innehåll från olika fil format. Text som hittas i käll filen placeras i ett `content` fält, en för varje dokument. Ange därför indatatypen som `"/document/content"`.

En grafisk representation av kunskapsuppsättningen visas nedan.

![Förstå en färdigheter](media/cognitive-search-tutorial-blob/skillset.png "Förstå en färdigheter")

Utdata kan mappas till ett index, användas som indata till en underordnad färdighet eller båda, som är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Skapa ett index

I det här avsnittet definierar du index schemat genom att ange de fält som ska ingå i det sökbara indexet och ange sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | id         | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Kör det här skriptet för att skapa indexet med namnet `cogsrch-py-index`.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera status koden 201 som bekräftar att det lyckades.

Mer information om hur du definierar ett index finns i [skapa index (Azure Kognitiv sökning REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Skapa en indexerare, mappa fält och köra transformeringar

Hittills har du skapat en data källa, en färdigheter och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. Om du vill koppla ihop dessa objekt i en indexerare måste du definiera fält mappningar.

+ FieldMappings bearbetas före färdigheter och mappar käll fälten från data källan till mål fälten i ett index. Om fält namn och typer är desamma i båda ändar, krävs ingen mappning.

+ OutputFieldMappings bearbetas efter färdigheter, refererar till sourceFieldNames som inte finns förrän dokument sprickor eller berikning skapar dem. TargetFieldName är ett fält i ett index.

Förutom att koppla in indata till utdata kan du också använda fält mappningar för att förenkla data strukturer. Mer information finns i [så här mappar du berikade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

Kör det här skriptet för att skapa en indexerare med namnet `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran bör snabbt returnera status kod 201, men bearbetningen kan ta flera minuter att slutföra. Även om data uppsättningen är liten, är analys kunskaper, till exempel bild analys, i beräknings intensiva och tar tid.

Använd [status skriptet kontrol lera indexerare](#check-indexer-status) i nästa avsnitt för att fastställa när indexerings processen har slutförts.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att komma åt data, mappa indata och utdata eller med ordningen på åtgärder, visas det i det här skedet. Om du vill köra pipelinen igen med kod-eller skript ändringar kan du behöva ta bort objekten först. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

#### <a name="explore-the-request-body"></a>Utforska begärantexten

Skriptet ställer in `"maxFailedItems"`  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också `"dataToExtract":"contentAndMetadata"`-instruktionen i konfigurationsparametrarna. Den här instruktionen instruerar indexeraren att extrahera innehållet från olika fil format och de metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av `"imageAction":"generateNormalizedImages"`, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Det här beteendet gäller för båda de bilder som är inbäddade i dokumenten (Tänk på en bild i en PDF) och bilder som finns i data källan, t. ex. en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Kontrollera status för indexerare

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Kör följande skript för att ta reda på om bearbetningen av indexeraren är slutförd.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I svaret övervakar du "lastResult" för värdena "status" och "slut tid". Kör skriptet regelbundet för att kontrol lera statusen. När indexeraren har slutförts ändras statusen till "lyckades", en "slut tid" anges och svaret innehåller eventuella fel och varningar som uppstått under anrikningen.

![Indexeraren skapas](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexeraren skapas")

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga. Till exempel visas varningen i den här skärm bilden i en av JPEG-filerna som inte har text.

![Exempel på indexerings varning](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Exempel på indexerings varning")

## <a name="query-your-index"></a>Skicka frågor mot ditt index

När indexeringen är klar kör du frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Kognitiv sökning de översta 50 resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Anvisningar finns i [så här visar du sid resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

Som ett verifieringssteg ska du fråga indexet för alla fält.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultatet bör se ut som i följande exempel. Skärm bilden visar bara en del av svaret.

![Fråga efter index för alla fält](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Fråga indexet efter alla fält")

Utdata är indexeringsschema med namn, typ och attribut för varje fält.

Skicka en ny fråga för `"*"` för att returnera hela innehållet i ett enda fält som `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultatet bör se ut som i följande exempel. Skärm bilden visar bara en del av svaret.

![Fråga efter index för organisationers innehåll](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Fråga indexet för att returnera innehållet i organisationer")

Upprepa för ytterligare fält: innehåll, languageCode, diskussions fraser och organisationer i den här övningen. Du kan returnera flera fält via `$select` med hjälp av en kommaavgränsad lista.

Du kan använda GET eller POST, beroende på frågesträngens komplexitet och längd. Mer information finns i [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Fråga med REST API).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment faserna av pipeline-utveckling är den mest praktiska metoden för design iterationer att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Så här indexerar du dokument med de nya definitionerna:

1. Ta bort indexet för att ta bort sparade data. Ta bort indexeraren för att återskapa den på din tjänst.
2. Ändra färdigheter och index definitionerna.
3. Återskapa ett index och en indexerare på tjänsten för att köra pipelinen.

Du kan använda portalen för att ta bort index, indexerare och färdighetsuppsättningar. När du tar bort indexeraren kan du välja att selektivt ta bort index, färdigheter och data källan samtidigt.

![Ta bort Sök objekt](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Ta bort Sök objekt i portalen")

Du kan också ta bort dem med hjälp av ett skript. Följande skript tar bort färdigheter som vi skapade. Du kan enkelt ändra begäran om du vill ta bort index, indexerare och data källa.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

När koden utvecklas kanske du vill begränsa en strategi för återskapning. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Inbyggda kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med färdigheter-definitioner och ett sätt att kedja samman färdigheter genom indata och utdata. Du har också lärt dig att `outputFieldMappings` i Indexer-definitionen krävs för att dirigera berikade värden från pipelinen till ett sökbart index i en Azure Kognitiv sökning-tjänst.

Slutligen har du lärt dig hur du testar resultaten och återställer systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. I den här versionen finns det en mekanism för att visa interna konstruktioner (berikade dokument som skapats av systemet). Du har också lärt dig hur du kontrollerar indexerings status och vilka objekt som måste tas bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa efter en själv studie kurs är att ta bort resurs gruppen som innehåller Azure Kognitiv sökning service och Azure Blob Service. Förutsatt att du sätter båda tjänsterna i samma grupp tar du bort resurs gruppen för att ta bort allt innehåll i den, inklusive tjänsterna och allt lagrat innehåll som du skapade i den här självstudien. På portalen visas resursgruppens namn på översiktssidan för varje tjänst.

## <a name="next-steps"></a>Nästa steg

Anpassa eller utöka pipelinen med anpassade kunskaper. När du skapar en anpassad kunskap och lägger till den i en kunskapsuppsättning kan du publicera text eller bildanalys som du skriver själv.

> [!div class="nextstepaction"]
> [Exempel: skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)
