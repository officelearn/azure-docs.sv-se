---
title: 'Python-Självstudier: Anropa Cognitive Services i en pipeline för fulltextindexering – Azure Search'
description: Gå igenom ett exempel på extrahering av data, naturligt språk och bilder AI bearbetning i Azure Search med en Jupyter Python notebook. Extraherade data är indexerade och lätt att komma åt fråga.
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: 7f8ac2b7973ddd21f16f2914b01618b2b005cd13
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485793"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Python-Självstudier: Anropa API: er med Cognitive Services i ett Azure Search indexering av pipeline

I den här självstudien har du lärt dig mekaniken bakom att programmera databerikande i Azure Search med *kognitiva kunskaper*. Kunskaper backas upp av språkbearbetning (NLP) och avbildning analysmöjligheter i Cognitive Services. Via kompetens sammansättning och konfiguration, för att extrahera text och text som representerar en bild eller skannade dokument. Du kan också identifiera språk, entiteter, nyckelfraser och mycket mer. Resultatet är omfattande ytterligare innehåll i ett Azure Search-index som skapats med AI enrichments i en pipeline för fulltextindexering. 

I den här självstudien får använder du Python för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en indexeringspipeline som berikar källdata på väg till ett index
> * Använd inbyggda färdigheter: entitetsigenkänning, språkidentifiering, textredigering och extrahering av viktiga fraser
> * Lära dig att sammanlänka kunskaper genom att mappa indata till utdata i en kompetens
> * Köra begäranden och granska resultatet
> * Återställa index och indexerare för ytterligare utveckling

Utdata är ett fulltextsökbart index i Azure Search. Du kan förbättra indexet med andra standardfunktioner som [synonymer](search-synonyms.md), [bedömningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analysverktyg](search-analyzers.md) och [filter](search-filters.md). 

Den här självstudien körs på den kostnadsfria tjänsten, men antalet kostnadsfria transaktioner är begränsat till 20 dokument per dag. Om du vill köra den här självstudien mer än en gång i samma dag, kan du använda en mindre fil så att du kan rymmas i fler körningar.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, att lägga till fler dokument eller att lägga till fler AI-algoritmer, måste du [bifoga en fakturerbar resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md). Avgifter tillkommer när du anropar API: er i Cognitive Services och extrahering av avbildningen som en del av det dokumentknäckning steget i Azure Search. Det finns inga avgifter för textextrahering från dokument.
>
> Körningen av inbyggda färdigheter som ingår debiteras enligt den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Bild extrahering priser beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data som används i den här självstudien. 

+ [Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempeldata. Kontrollera att lagringskontot är i samma region som Azure Search.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), vilket ger Python 3.x och Jupyter Notebooks.

+ [Exempeldata](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) består av en liten uppsättning olika typer. 

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

Du behöver för att interagera med Azure Search-tjänsten, tjänstens URL och en åtkomstnyckel. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende regelbundet per begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-data"></a>Förbereda exempeldata

Berikningspipelinen hämtar data från Azure-datakällor. Källdata måste komma från en datakällstyp som stöds av en [Azure Search-indexerare](search-indexer-overview.md). Azure Table Storage stöds inte för kognitiv sökning. I den här övningen använder vi blogglagring för att demonstrera flera typer av innehåll.

1. [Logga in på Azure-portalen](https://portal.azure.com)navigerar du till ditt Azure storage-konto, klickar du på **Blobar**, och klicka sedan på **+ behållare**.

1. [Skapa en blobbehållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempeldata. Du kan ange offentlig åtkomstnivå till någon av dess giltiga värden.

1. När behållaren har skapats kan du öppna den och välj **överför** i kommandofältet för att ladda upp exempelfilerna som du hämtade i föregående steg.

   ![Källfiler i Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. När exempelfilerna har lästs in hämtar du containerns namn och en anslutningssträng för Blob Storage. Det kan du göra genom att gå till lagringskontot i Azure Portal. Klicka på **åtkomstnycklar**, och sedan kopiera den **Connection String** fält.

Anslutningssträngen har följande format: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Behåll anslutningssträngen till hands. Du behöver det i ett kommande steg.

Det finns andra sätt att ange anslutningssträngen, till exempel att ange en signatur för delad åtkomst. Om du vill veta mer om autentiseringsuppgifter för datakällor kan du läsa [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Indexera Azure Blob Storage).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

> [!Note]
> Den här artikeln visar hur du skapar en datakälla, index, indexerare och kompetens med hjälp av en serie med Python-skript. Om du vill ladda ned exemplet fullständig anteckningsbok, går du till den [lagringsplatsen för Azure Search-python-exempel](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Använd Anaconda Navigator för att starta Jupyter Notebook och skapa en ny Python 3-anteckningsbok.

## <a name="connect-to-azure-search"></a>Anslut till Azure Search

I anteckningsboken, kör du skriptet för att läsa in de bibliotek som används för att arbeta med JSON och utformningen av HTTP-begäranden.

```python
import json
import requests
from pprint import pprint
```

Definiera namn för datakällan, index, indexerare och kompetens. Kör skriptet att ställa in namn för den här självstudien.

```python
#Define the names for the data source, skillset, index and indexer
datasource_name="cogsrch-py-datasource"
skillset_name="cogsrch-py-skillset"
index_name="cogsrch-py-index"
indexer_name="cogsrch-py-indexer"
```

> [!Tip]
> Du är begränsad till tre index, indexerare och datakällor på en kostnadsfri tjänst. I den här kursen skapar du en av varje. Kontrollera att det finns utrymme att skapa nya objekt innan skickas vidare.

I följande skript, Ersätt platshållarna för search-tjänsten (din-SEARCH-SERVICE-NAME) och administrations-API (din-ADMIN-API-nyckel) och kör den för att konfigurera tjänstslutpunkt för sökning.

```python
#Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
        'api-key': '<YOUR-ADMIN-API-KEY>' }
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Skapa en datakälla

Nu när dina tjänster och källfiler är förberedda kan du börja samla in komponenterna för din indexeringspipeline. Börja med ett datakällobjekt som talar om Azure Search för att hämta externa data.

I följande skript, ersätter du platshållaren för din BLOB-RESOURCE-ANSLUTNINGSSTRÄNG med anslutningssträngen för den blob som du skapade i föregående steg. Kör skriptet för att skapa en datakälla med namnet `cogsrch-py-datasource`.

```python
#Create a data source
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
r = requests.put( endpoint + "/datasources/" + datasource_name, data=json.dumps(datasource_payload), headers=headers, params=params )
print (r.status_code)
```

Begäran ska returnera statuskod 201 bekräftar lyckades.

Azure-portalen på instrumentpanelen söktjänstsidan kontrollerar du att cogsrch-py-datasource visas i den **datakällor** lista. Klicka på **uppdatera** att uppdatera sidan.

![Datakällspanel i portalen](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Datakällspanel i portalen")

## <a name="create-a-skillset"></a>Skapa en kunskapsuppsättning

I det här steget definierar du en uppsättning berikande anvisningar för att tillämpa till dina data. Du kallar varje berikande steg för en *kunskap*, och uppsättningen med berikande steg för en *kunskapsuppsättning*. Den här självstudien används [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) för gruppens kunskaper avgör:

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Textuppdelning](cognitive-search-skill-textsplit.md) för att dela upp stort innehåll i mindre delar innan du anropar färdigheten extrahering av nyckelfraser. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Entitetsidentifiering](cognitive-search-skill-entity-recognition.md) för att extrahera namnen på organisationer från innehållet i blob-behållaren.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna. 

### <a name="python-script"></a>Python-skriptet
Kör följande skript för att skapa en kompetens som kallas `cogsrch-py-skillset`.

```python
#Create a skillset
skillset_payload = {
  "name": skillset_name,
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
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
      "textSplitMode" : "pages",
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
          "name":"languageCode", "source": "/document/languageCode"
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

r = requests.put(endpoint + "/skillsets/" + skillset_name, data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera statuskod 201 bekräftar lyckades.

Diskussionsämne extrahering färdighet tillämpas för varje sida. Genom att ställa in kontexten `"document/pages/*"`, du kör den här enricher för varje medlem i dokumentsidor /-matris (för varje sida i dokumentet).

Varje kunskap körs på innehållet i dokumentet. Under bearbetningen delar Azure Search upp varje dokument för att läsa innehåll från olika filformat. Text som hittas i källfilen placeras i en `content` fältet, en för varje dokument. Ställ därför in indata som `"/document/content"`.

En grafisk representation av kunskapsuppsättningen visas nedan.

![Förstå en kunskapsuppsättning](media/cognitive-search-tutorial-blob/skillset.png "Förstå en kunskapsuppsättning")

Utdata kan mappas till ett index som används som indata för en underordnad färdighet eller båda, så är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Skapa ett index

I det här avsnittet definierar du indexschemat genom att ange de fält som ska ingå i sökbart index och Sök-attribut för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | id         | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Kör det här skriptet för att skapa indexet med namnet `cogsrch-py-index`.

```python
#Create an index
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

r = requests.put(endpoint + "/indexes/" + index_name, data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran ska returnera statuskod 201 bekräftar lyckades.

Om du vill veta mer om att definiera ett index kan du läsa [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Skapa index (Azure Search-REST API)).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Skapa en indexerare, mappa fält och köra transformeringar

Hittills har du skapat en datakälla, en kompetens och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. För att knyta ihop dessa objekt i en indexerare, måste du definiera fältmappningar.

+ FieldMappings bearbetas före kompetens, mappar källfält från datakällan till målfält i ett index. Om fältnamn och typer är samma i båda ändar, krävs ingen mappning.

+ OutputFieldMappings bearbetas efter kompetens, refererar till sourceFieldNames som inte finns tills dokumentknäckning eller funktioner som de skapas. TargetFieldName är ett fält i ett index.

Du kan också använda fältmappningar platta ut datastrukturer förutom anslutning upp indata till utdata. Mer information finns i [avancerad och mappning till ett sökbart index](cognitive-search-output-field-mapping.md).

Kör det här skriptet för att skapa en indexerare med namnet `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
   "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
   "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}

r = requests.put(endpoint + "/indexers/" + indexer_name, data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran snabbt ska returnera statuskod 201, men det kan ta flera minuter att slutföra. Även om datauppsättningen är liten, analytiska kunskaper, till exempel bildanalys, intensiv och ta tid.

Använd den [Kontrollera status för indexeraren](#check-indexer-status) skriptet i nästa avsnitt för att avgöra när indexeraren processen är klar.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det finns ett problem som har åtkomst till data, mappa indata och utdata, eller med utförs, visas den i det här skedet. Om du vill köra pipelinen kod eller skript ändringar igen, kan du behöva först ta bort objekt. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

#### <a name="explore-the-request-body"></a>Utforska begärantexten

Skriptet ställer in `"maxFailedItems"`  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också `"dataToExtract":"contentAndMetadata"`-instruktionen i konfigurationsparametrarna. Den här sekretesspolicyn beskriver indexeraren extrahera innehållet från olika filformat och de metadata som hör till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av `"imageAction":"generateNormalizedImages"`, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Detta gäller både för bilder som är inbäddade i dokument (se för en bild i en PDF-fil) och avbildningar hittades i datakällan, till exempel en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Kontrollera status för indexerare

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Om du vill veta om indexerare bearbetningen är klar kör du följande skript.

```python
#Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name + "/status", headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Övervaka ”lastResult” för dess värden för ”status” och ”endTime” i svaret. Regelbundet kör skript för att kontrollera status. När indexeraren har slutförts status anges till ”lyckades”, ”endTime” anges och svaret innehåller eventuella fel och varningar som uppstod under funktioner.

![Indexeraren skapas](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer har skapats")

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga. Till exempel visar en JPEG-filer som inte har någon text varningen i den här skärmbilden.

![Exempel indexeraren varning](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "exempel indexeraren varning")

## <a name="query-your-index"></a>Skicka frågor mot ditt index

När indexeringen är klar kör du frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Search de 50 bästa resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Instruktioner finns i [How to page results in Azure Search](search-pagination-page-layout.md) (Söka resultat i Azure Search).

Som ett verifieringssteg ska du fråga indexet för alla fält.

```python
#Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name, headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultatet bör likna följande exempel. Skärmbilden visar bara en del av svaret.

![Frågeindex för alla fält](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "fråga indexet för alla fält")

Utdata är indexeringsschema med namn, typ och attribut för varje fält.

Skicka en ny fråga för `"*"` för att returnera hela innehållet i ett enda fält som `organizations`.

```python
#Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name + "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultatet bör likna följande exempel. Skärmbilden visar bara en del av svaret.

![Fråga index för innehållet i organisationer](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "fråga indexet för att returnera innehållet i organisationer")

Upprepa detta för ytterligare fält: innehåll, languageCode, keyPhrases och organisationer i den här övningen. Du kan returnera flera fält via `$select` med hjälp av en kommaavgränsad lista.

Du kan använda GET eller POST, beroende på frågesträngens komplexitet och längd. Mer information finns i [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Fråga med REST API).
den <a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella stadierna för pipelineutvecklingen är det mest praktiskt för designiterationer att ta bort objekten från Azure Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Så här indexerar du dokument med de nya definitionerna:

1. Ta bort indexet för att ta bort sparade data. Ta bort indexeraren för att återskapa den på din tjänst.
2. Ändra kunskaps- och index definitioner.
3. Återskapa ett index och en indexerare på tjänsten för att köra pipelinen.

Du kan använda portalen för att ta bort index, indexerare och kompetens. När du tar bort indexeraren du kan också, selektivt ta bort källan index, kompetens och data på samma gång.

![Ta bort Sök objekt](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Delete Sök objekt i portalen")

Du kan också ta bort dem med hjälp av ett skript. Följande skript tar bort kompetens som vi skapade. Du kan enkelt ändra begäran om att ta bort index, indexerare och datakällan.

```python
#delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name, headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

När koden utvecklas kanske du vill begränsa en strategi för återskapning. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Fördefinierade kunskaper](cognitive-search-predefined-skills.md) introducerades tillsammans med kompetens definitioner och ett sätt att kedja kunskaper tillsammans med indata och utdata. Du har också lärt dig att `outputFieldMappings` i indexerardefinitionen krävs för routningsberikade värden från pipelinen i ett sökbart index på en Azure Search-tjänst.

Slutligen kan du lärt dig hur Testa resultaten och återställa systemet efter ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. I den här versionen finns det en mekanism för att visa interna konstruktioner (berikade dokument som skapats av systemet). Du lärt dig hur du kontrollerar status för indexerare och vilka objekt måste tas bort innan du köra en pipeline.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten och Azure Blob Service. Om vi antar att du placerar båda tjänsterna i samma grupp, ta bort resursgruppen för att permanent ta bort allt, inklusive tjänsterna och lagrade innehåll som du skapade för den här kursen. På portalen visas resursgruppens namn på översiktssidan för varje tjänst.

## <a name="next-steps"></a>Nästa steg

Anpassa eller utöka pipelinen med anpassade kunskaper. När du skapar en anpassad kunskap och lägger till den i en kunskapsuppsättning kan du publicera text eller bildanalys som du skriver själv.

> [!div class="nextstepaction"]
> [Exempel: skapa en anpassad kunskap](cognitive-search-create-custom-skill-example.md)
