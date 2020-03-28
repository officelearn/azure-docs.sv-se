---
title: 'Självstudiekurs: Python och AI över Azure-blobbar'
titleSuffix: Azure Cognitive Search
description: Gå igenom ett exempel på textextrahering och bearbetning av naturligt språk över innehåll i Blob-lagring med hjälp av en Jupyter Python-anteckningsbok och Azure Cognitive Search REST API:er.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78194062"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Självstudiekurs: Använda Python och AI för att generera sökbart innehåll från Azure-blobbar

Om du har ostrukturerad text eller avbildningar i Azure Blob-lagring kan en [AI-anrikningspipeline](cognitive-search-concept-intro.md) extrahera information och skapa nytt innehåll som är användbart för fulltextsökning eller kunskapsutvinningsscenarier. Även om en pipeline kan bearbeta avbildningar fokuserar den här Python-självstudien på text, tillämpa språkidentifiering och bearbetning av naturligt språk för att skapa nya fält som du kan använda i frågor, aspekter och filter.

Den här självstudien använder Python och [REST-API:erna för sökning](https://docs.microsoft.com/rest/api/searchservice/) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Börja med hela dokument (ostrukturerad text) som PDF, HTML, DOCX och PPTX i Azure Blob-lagring.
> * Definiera en pipeline som extraherar text, identifierar språk, känner igen entiteter och identifierar nyckelfraser.
> * Definiera ett index för att lagra utdata (råinnehåll, plus pipelinegenererade namnvärdespar).
> * Kör pipelinen för att starta omvandlingar och analyser och för att skapa och läsa in indexet.
> * Utforska resultat med hjälp av fulltextsökning och en omfattande frågesyntax.

Om du inte har en Azure-prenumeration öppnar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure-lagring](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnadsfria tjänsten för den här självstudien. En kostnadsfri söktjänst begränsar dig till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Innan du börjar, se till att du har utrymme på din tjänst för att acceptera de nya resurserna.

## <a name="download-files"></a>Hämta filer

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Hämta** längst upp till vänster för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **Extrahera alla**. Det finns 14 filer av olika slag. Du kommer att använda 7 för denna övning.

## <a name="1---create-services"></a>1 - Skapa tjänster

Den här självstudien använder Azure Cognitive Search för indexering och frågor, Cognitive Services på backend för AI-anrikning och Azure Blob-lagring för att tillhandahålla data. Den här självstudien förblir under den kostnadsfria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänster du behöver skapa är sökning och lagring.

Om möjligt, skapa både i samma region och resursgrupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i alla regioner.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga in på Azure-portalen](https://portal.azure.com/) och klicka på **+ Skapa resurs**.

1. Sök efter *lagringskonto* och välj Microsofts erbjudande om lagringskonto.

   ![Skapa lagringskonto](media/cognitive-search-tutorial-blob/storage-account.png "Skapa lagringskonto")

1. På fliken Grunderna krävs följande objekt. Acceptera standardvärdena för allt annat.

   + **Resursgrupp**. Välj en befintlig eller skapa en ny, men använd samma grupp för alla tjänster så att du kan hantera dem kollektivt.

   + **Namn på lagringskonto**. Om du tror att du har flera resurser av samma typ använder du namnet för att skilja efter typ och region, till exempel *blobstoragewestus*. 

   + **Plats**. Om möjligt väljer du samma plats som används för Azure Cognitive Search och Cognitive Services. En enda plats annullerar bandbreddsavgifter.

   + **Konto Typ**. Välj standard, *StorageV2 (allmänt ändamål v2)*.

1. Klicka på **Granska + Skapa** om du vill skapa tjänsten.

1. När den har skapats klickar du på **Gå till resursen** för att öppna sidan Översikt.

1. Klicka på **Blobbar-tjänsten.**

1. Klicka på **+ Behållare** om du vill skapa en behållare och namnge den *kugg-sök-demo*.

1. Välj *cog-search-demo* och klicka sedan på **Ladda upp** för att öppna mappen där du sparade nedladdningsfilerna. Markera alla filer som inte är bildfiler. Du borde ha 7 filer. Klicka på **OK** för att ladda upp.

   ![Ladda upp exempelfiler](media/cognitive-search-tutorial-blob/sample-files.png "Ladda upp exempelfiler")

1. Innan du lämnar Azure Storage får du en anslutningssträng så att du kan formulera en anslutning i Azure Cognitive Search. 

   1. Bläddra tillbaka till översiktssidan för ditt lagringskonto (vi använde *blobstragewestus* som exempel). 
   
   1. I det vänstra navigeringsfönstret väljer du **Access-nycklar** och kopierar en av anslutningssträngarna. 

   Anslutningssträngen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutningssträngen i Anteckningar. Du behöver det senare när du konfigurerar datakällanslutningen.

### <a name="cognitive-services"></a>Cognitive Services

AI-anrikning backas upp av Cognitive Services, inklusive textanalys och datorseende för naturligt språk och bildbehandling. Om ditt mål var att slutföra en verklig prototyp eller projekt, skulle du vid denna punkt tillhandahålla Cognitive Services (i samma region som Azure Cognitive Search) så att du kan koppla den till indexeringsåtgärder.

För den här övningen kan du dock hoppa över resursetablering eftersom Azure Cognitive Search kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnadsfria transaktioner per indexeringskörning. Eftersom den här självstudien använder 7 transaktioner är den kostnadsfria allokeringen tillräcklig. För större projekt bör du planera att etablera Cognitive Services på S0-nivån för betalning per steg. Mer information finns i [Bifoga Kognitiva tjänster](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure Cognitive Search, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnadsfria nivån för att slutföra den här genomgången. 

Precis som med Azure Blob-lagring tar du en stund att samla in åtkomstnyckeln. Vidare, när du börjar strukturera begäranden, måste du ange slutpunkten och admin api-nyckeln som används för att autentisera varje begäran.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Skaffa en api-nyckel och URL för Azure Cognitive Search

1. [Logga in på Azure-portalen](https://portal.azure.com/)och på **sidan** Översikt för söktjänsten får du namnet på söktjänsten. Du kan bekräfta ditt tjänstnamn genom att granska slutpunktsadressen. Om slutpunktsadressen var `https://mydemo.search.windows.net`skulle `mydemo`tjänstnamnet vara .

2. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

   Hämta frågenyckeln också. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratörs- och frågenycklar](media/search-get-started-nodejs/service-name-and-keys.png)

Alla begäranden kräver en api-nyckel i huvudet på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, per begäran, mellan programmet som skickar begäran och den tjänst som hanterar den.

## <a name="2---start-a-notebook"></a>2 - Starta en anteckningsbok

Skapa anteckningsboken med hjälp av följande instruktioner eller hämta en färdig anteckningsbok från [Repoet för Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Använd Anaconda Navigator för att starta Jupyter Notebook och skapa en ny Python 3-anteckningsbok.

I anteckningsboken kör du det här skriptet för att läsa in de bibliotek som används för att arbeta med JSON och formulera HTTP-begäranden.

```python
import json
import requests
from pprint import pprint
```

I samma anteckningsbok definierar du namnen för datakällan, indexet, indexeraren och kompetensen. Kör det här skriptet för att ställa in namnen för den här självstudien.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

I följande skript ersätter du platshållarna för söktjänsten (YOUR-SEARCH-SERVICE-NAME) och admin API-nyckeln (YOUR-ADMIN-API-KEY) och kör den för att konfigurera slutpunkten för söktjänsten.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3 - Skapa pipelinen

I Azure Cognitive Search sker AI-bearbetning under indexering (eller datainmatning). Den här delen av genomgången skapar fyra objekt: datakälla, indexdefinition, kompetens, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Ett [datakällobjekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) tillhandahåller anslutningssträngen till Blob-behållaren som innehåller filerna.

I följande skript ersätter du platshållaren YOUR-BLOB-RESOURCE-CONNECTION-STRING med anslutningssträngen för bloben som du skapade i föregående steg. Ersätt platshållartexten för behållaren. Kör sedan skriptet för att `cogsrch-py-datasource`skapa en datakälla med namnet .

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
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Begäran bör returnera en statuskod för 201 som bekräftar lyckade.

Kontrollera att kuggarlöddatakällan visas i listan **Datakällor** på instrumentpanelen för söktjänsten på instrumentpanelen för söktjänsten. Klicka på **Uppdatera** om du vill uppdatera sidan.

![Panelen Datakällor i portalen](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Panelen Datakällor i portalen")

### <a name="step-2-create-a-skillset"></a>Steg 2: Skapa en kompetens

I det här steget definierar du en uppsättning anrikningssteg som ska tillämpas på dina data. Du kallar varje berikande steg för en *kunskap*, och uppsättningen med berikande steg för en *kunskapsuppsättning*. Den här självstudien använder [inbyggda kognitiva färdigheter](cognitive-search-predefined-skills.md) för kompetensen:

+ [Entitetsigenkänning](cognitive-search-skill-entity-recognition.md) för att extrahera namnen på organisationer från innehåll i blob-behållaren.

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Textuppdelning](cognitive-search-skill-textsplit.md) för att dela upp stort innehåll i mindre delar innan du anropar färdigheten extrahering av nyckelfraser. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna. 

Kör följande skript för att `cogsrch-py-skillset`skapa en kompetensuppsättning som kallas .

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

Begäran bör returnera en statuskod för 201 som bekräftar lyckade.

Nyckelfrasutextraheringsfärdigheten används för varje sida. Genom att ange `"document/pages/*"`kontexten till kör du den här berikaren för varje medlem i dokument-/sidmatrisen (för varje sida i dokumentet).

Varje kunskap körs på innehållet i dokumentet. Under bearbetningen knäcker Azure Cognitive Search varje dokument för att läsa innehåll från olika filformat. Text som finns i källfilen `content` placeras i ett fält, ett för varje dokument. Ställ därför in `"/document/content"`indata som .

En grafisk representation av kunskapsuppsättningen visas nedan.

![Förstå en kompetens](media/cognitive-search-tutorial-blob/skillset.png "Förstå en kompetens")

Utdata kan mappas till ett index, användas som indata till en nedströms färdighet, eller båda, som är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Steg 3: Skapa ett index

I det här avsnittet definierar du indexschemat genom att ange de fält som ska inkluderas i det sökbara indexet och ange sökattribut för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | id         | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Kör skriptet om du `cogsrch-py-index`vill skapa indexet med namnet .

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

Begäran bör returnera en statuskod för 201 som bekräftar lyckade.

Mer information om hur du definierar ett index finns i [Skapa index (AZURE Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: Skapa och kör en indexerare

En [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) driver pipelinen. De tre komponenter som du har skapat hittills (datakälla, kompetens, index) är indata till en indexerare. Att skapa indexeraren på Azure Cognitive Search är den händelse som försätter hela pipelinen i rörelse. 

Om du vill koppla samman dessa objekt i en indexerare måste du definiera fältmappningar.

+ FältetMappningar bearbetas före kompetensuppsättningen, mappningskällorfält från datakällan till målfälten i ett index. Om fältnamn och typer är desamma i båda ändar krävs ingen mappning.

+ UtdataFieldMappings bearbetas efter kompetensuppsättningen, refererar till sourceFieldNames som inte finns förrän dokumentsprickor eller anrikning skapar dem. TargetFieldName är ett fält i ett index.

Förutom att koppla indata till utdata kan du också använda fältmappningar för att förenkla datastrukturer. Mer information finns i [Så här mappar du utökade fält till ett sökbart index](cognitive-search-output-field-mapping.md).

Kör skriptet om du `cogsrch-py-indexer`vill skapa en indexerare med namnet .

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

Begäran bör returnera en statuskod för 201 snart, men bearbetningen kan ta flera minuter att slutföra. Även om datauppsättningen är liten är analytiska färdigheter, till exempel bildanalys, beräkningsintensiva och tar tid.

Du kan [övervaka indexerarstatus](#check-indexer-status) för att avgöra när indexeraren körs eller är klar.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det har uppstått ett problem med att komma åt data, mappa indata och utdata eller med operationsordningen visas den i det här skedet. Om du vill köra pipelinen igen med kod- eller skriptändringar kan du behöva ta bort objekt först. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

#### <a name="about-the-request-body"></a>Om förfrå undertexten för begäran

Skriptet ställer in `"maxFailedItems"`  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också `"dataToExtract":"contentAndMetadata"`-instruktionen i konfigurationsparametrarna. Den här policyn talar om för indexeraren att extrahera innehållet från olika filformat och metadata som är relaterade till varje fil.

När innehållet har extraherats kan du ställa in `imageAction` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av `"imageAction":"generateNormalizedImages"`, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Detta gäller både de bilder som är inbäddade i dokumenten (tänk på en bild i en PDF-fil) och bilder som finns i datakällan, till exempel en JPG-fil.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Bildskärmsindexering

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Om du vill ta reda på om indexeringsbearbetningen är klar kör du följande skript.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I svaret övervakar du "lastResult" för dess "status" och "endTime"-värden. Kör regelbundet skriptet för att kontrollera statusen. När indexeraren har slutförts kommer statusen att ställas in på "framgång", en "endTime" kommer att anges och svaret kommer att innehålla eventuella fel och varningar som inträffade under anrikningen.

![Indexeraren skapas](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexeraren skapas")

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. Många varningar är godartade. Om du till exempel indexerar en JPEG-fil som inte har text visas varningen i den här skärmbilden.

![Exempel indexerarvarning](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Exempel indexerarvarning")

## <a name="5---search"></a>5 - Sök

När indexeringen är klar kör du frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Cognitive Search de 50 bästa resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Instruktioner finns [i Så här sidresultat i Azure Cognitive Search](search-pagination-page-layout.md).

Som ett verifieringssteg hämtar du indexdefinitionen som visar alla fält.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultaten bör se ut ungefär som i följande exempel. Skärmbilden visar bara en del av svaret.

![Frågeindex för alla fält](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Fråga indexet för alla fält")

Utdata är indexeringsschema med namn, typ och attribut för varje fält.

Skicka en ny fråga för `"*"` för att returnera hela innehållet i ett enda fält som `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Resultaten bör se ut ungefär som i följande exempel. Skärmbilden visar bara en del av svaret.

![Frågeindex för innehållet i organisationer](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Fråga indexet för att returnera innehållet i organisationer")

Upprepa för ytterligare fält: innehåll, språkKod, keyPhrases och organisationer i den här övningen. Du kan returnera flera fält via `$select` med hjälp av en kommaavgränsad lista.

Du kan använda GET eller POST, beroende på frågesträngens komplexitet och längd. Mer information finns i [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Fråga med REST API).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella utvecklingsstadierna är den mest praktiska metoden för designiteration att ta bort objekten från Azure Cognitive Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Du kan använda portalen för att ta bort index, indexerare, datakällor och skillsets. När du tar bort indexeraren kan du också selektivt ta bort index, kompetens och datakälla samtidigt.

![Ta bort sökobjekt](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Ta bort sökobjekt i portalen")

Du kan också ta bort dem med ett skript. Följande skript visar hur du tar bort en kompetens. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Statuskod 204 returneras vid borttagning.

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Inbyggda färdigheter](cognitive-search-predefined-skills.md) infördes, tillsammans med skillset definitioner och ett sätt att kedja färdigheter tillsammans genom ingångar och resultat. Du lärde dig `outputFieldMappings` också att i indexeringsdefinitionen krävs för routning av utökade värden från pipelinen till ett sökbart index på en Azure Cognitive Search-tjänst.

Slutligen har du lärt dig att testa resultaten och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. I den här versionen finns det en mekanism för att visa interna konstruktioner (berikade dokument som skapats av systemet). Du har också lärt dig hur du kontrollerar indexerarens status och vilka objekt som måste tas bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken Alla resurser eller Resursgrupper i fönstret för vänsternavigering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en AI-anrikningspipeline, låt oss ta en närmare titt på kompetensdefinitioner och individuella färdigheter.

> [!div class="nextstepaction"]
> [Hur man skapar en kompetens](cognitive-search-defining-skillset.md)
