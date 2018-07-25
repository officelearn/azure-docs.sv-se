---
title: Självstudie om att söka i halvstrukturerade data från Azure-molnlagring i Azure Search | Microsoft Docs
description: I den här självstudien får du lära dig att söka i halvstrukturerade Azure-blobdata med Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: heidist
ms.openlocfilehash: a7b006bd8469ddce1415ab6cb7c52c0171ae11cd
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005085"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Självstudie: Söka i halvstrukturerade data i Azure-molnlagring

I en självstudiekurs i två delar får du lära dig hur du söker efter halvstrukturerade och ostrukturerade data med Azure Search. I [del 1](../storage/blobs/storage-unstructured-search.md) fick du lära dig att söka i ostrukturerade data och den innehöll också viktiga förutsättningar för den här kursen, till exempel information om hur du skapar ett lagringskonto. 

I en del 2 fokuserar vi på halvstrukturerade data, till exempel JSON som lagras i Azure-blobar. Halvstrukturerade data innehåller taggar eller märkord som separerar innehållet i data. Det görs skillnad på ostrukturerade data som måste indexeras som en helhet och formellt strukturerade data som följer en datamodell, till exempel ett relationsdatabasschema som kan genomsökas per fält.

I del 2 får du lära dig att:

> [!div class="checklist"]
> * Konfigurera en Azure Search-datakälla för en Azure-blobcontainer
> * Skapa och fylla i ett Azure Search-index och indexerare för att crawla containern och extrahera sökbart innehåll
> * Söka i indexet som du precis skapade

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Lagringskonto och söktjänst som erhålls genom att slutföra den [tidigare kursen](../storage/blobs/storage-unstructured-search.md).

* Installation av en REST-klient och en förståelse för hur du skapar en HTTP-begäran. I den här självstudiekursen använder vi [Postman](https://www.getpostman.com/). Du kan använda en annan REST-klient som du är van vid.

> [!NOTE]
> I den här kursen används JSON-matriser som för närvarande är en förhandsgranskningsfunktion i Azure Search. Den finns inte i portalen. Därför använder vi förhandsversionen av REST API som tillhandahåller den här funktionen och ett REST-klientverktyg för att anropa API:t.

## <a name="set-up-postman"></a>Konfigurera Postman

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget kan du se [Utforska REST-API:er för Azure Search REST API:er med hjälp av Fiddler eller Postman](search-fiddler.md) för mer information.

Metoden för begäran för varje anrop i den här kursen är ”POST”. Huvudnycklarna är ”Content-type” och ”api-key”. Värdena för huvudnycklarna är "application/json" och din "admin key" (administratörsnyckeln är platshållare för din sökprimärnyckel). Meddelandetexten är där du placerar det faktiska innehållet i anropet. Hur du konstruerar frågan kan variera beroende på vilken klient du använder, men det här är grunderna.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/postmanoverview.png)

För REST-anropen som används i den här kursen krävs din sök-api-nyckel. Du hittar din api-nyckel under **Nycklar** i din söktjänst. Api-nyckeln måste anges i huvudet i varje API-anrop (ersätt ”admin key” i föregående skärmbild med den) som du gör i den här kursen. Behåll nyckeln eftersom du behöver den för varje anrop.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Ladda ned exempeldata

En uppsättning exempeldata har förberetts för dig. **Ladda ned [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** och extrahera den i dess mapp.

I mappen finns JSON-exempelfiler som ursprungligen var textfiler från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Vi har konverterat dem till JSON för att göra det enklare för dig.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Ladda upp exempeldata

I Azure Portal går du tillbaka till det lagringskonto som skapades i den [tidigare kursen](../storage/blobs/storage-unstructured-search.md). Öppna **datacontainern** och klicka på **Överför**.

Klicka på **Avancerat**, ange ”clinical-trials-json” och ladda sedan upp alla JSON-filer som du hämtade.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/clinicalupload.png)

När överföringen är klar ska filerna visas i en egen undermapp i datacontainern.

## <a name="connect-your-search-service-to-your-container"></a>Ansluta din söktjänst till containern

Vi använder Postman för att göra tre API-anrop till din söktjänst för att skapa en datakälla, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och dina JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

Frågesträngen måste innehålla **api-version=2016-09-01-Preview** och varje anrop ska returnera **201 Skapad**. Den allmänt tillgängliga api-versionen kan inte ännu hantera JSON som en JSON-matris. Det kan för närvarande endast förhandsgranskningsversionen av api-versionen.

Kör följande tre API-anrop från REST-klienten.

### <a name="create-a-datasource"></a>Skapa en datakälla

En datakälla anger vilka data som ska indexeras.

Slutpunkten för anropet är `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på söktjänsten.

För det här anropet behöver du namnet på lagringskontot och lagringskontonyckeln. Lagringskontonyckeln hittar du i **Åtkomstnycklar** i ditt lagringskonto i Azure Portal. Platsen visas på följande bild:

  ![Halvstrukturerad sökning](media/search-semi-structured-data/storagekeys.png)

Ersätt `[storage account name]` och `[storage account key]` i meddelandetexten för anropet innan du utför anropet.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

Svaret ska se ut så här:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>Skapa ett index
    
Det andra API-anropet skapar ett index. Ett index anger alla parametrar och deras attribut.

URL:en för det här anropet är `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på söktjänsten.

Ersätt först URL:en. Sedan kopierar du och klistrar in följande kod i meddelandetexten och kör frågan.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

Svaret ska se ut så här:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>Skapa en indexerare

En indexerare ansluter datakällan till målsökindex och tillhandahåller eventuellt ett schema för att automatisera datauppdateringen.

URL:en för det här anropet är `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på söktjänsten.

Ersätt först URL:en. Sedan kopierar du och klistrar in följande kod i meddelandetexten och kör frågan.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Svaret ska se ut så här:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Söka i JSON-filer

Nu när söktjänsten har anslutits till datacontainern kan du börja söka i dina filer.

Öppna Azure Portal och gå tillbaka till din söktjänst. Precis som du gjorde i föregående självstudiekurs.

  ![Ostrukturerad sökning](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Sökning med användardefinierade metadata

Som tidigare kan du söka data på flera olika sätt: fulltextsökning, systemegenskaper eller användardefinierade metadata. Du kan bara söka systemegenskaper och användardefinierade metadata med parametern `$select` om de markerades som **hämtbara** när målindex skapades. Parametrarna i indexet får inte ändras när de har skapats. Men du kan lägga till ytterligare parametrar.

`$select=Gender,metadata_storage_size` är ett exempel på en enkel fråga som begränsar svaret till dessa två parametrar.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/lastquery.png)

Ett exempel på en mer komplex fråga är `$filter=MinimumAge ge 30 and MaximumAge lt 75`, som endast returnerar resultat där parametrarna MinimumAge är större än eller lika med 30 och MaximumAge är mindre än 75.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/metadatashort.png)

Passa på att experimentera och prova några frågor själv. Observera att du kan använda logiska operatorer (och, eller, inte) och jämförelseoperatorer (eq, ne, gt, lt, ge, le). Strängjämförelser är skiftlägeskänsliga.

Parametern `$filter` fungerar endast med metadata som markerades som filtrerbara när indexet skapades.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten. Du kan ta bort resursgruppen nu om du vill ta bort allt innehåll i den permanent. I portalen ser du resursgruppens namn på översiktssidan för Azure Search-tjänsten.

## <a name="next-steps"></a>Nästa steg

Du kan koppla AI-drivna algoritmer till en indexerarpipeline. I nästa steg fortsätter du med följande självstudie:

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)