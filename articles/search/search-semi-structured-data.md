---
title: "Söka efter halvstrukturerade data i Azure-molnlagring"
description: "Söker halvstrukturerade blob-data med Azure Search."
author: roygara
manager: timlt
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: ea57fa35f09299f95cdfd3c11b44657d35972295
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="search-semi-structured-data-in-cloud-storage"></a>Söka efter halvstrukturerade data i molnet

Lär dig hur du söker delvis strukturerade och Ostrukturerade data med Azure search i den här självstudiekursen serien i två delar. Den här kursen visar hur du söker halvstrukturerade data, till exempel JSON som lagras i Azure BLOB. Halvstrukturerade data innehåller taggar eller märkning som separerar innehållet i data. Den skiljer sig från strukturerade data att det inte formellt strukturerat enligt en datamodell, till exempel en relationsdatabas schemat.

I den här delen vi upp hur du:

> [!div class="checklist"]
> * Skapa och fylla i ett index i en Azure Search-tjänsten
> * Använda Azure Search-tjänsten för att söka ditt index

> [!NOTE]
> ”Stöd för JSON-matris är en förhandsvisningsfunktion i Azure Search. Det finns för närvarande inte i portalen. Därför använder vi förhandsversionen av REST-API som ger den här funktionen och en REST-klientverktyg att anropa API: et ”.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:
* Slutför den [tidigare självstudiekursen](../storage/blobs/storage-unstructured-search.md)
    * Den här kursen använder kontot och Sök lagringstjänsten skapade i föregående kursen
* Installera en REST-klient och förstå hur du skapar en HTTP-begäran


## <a name="set-up-the-rest-client"></a>Konfigurera REST-klient

Den här kursen behöver en REST-klient. Vid tillämpningen av den här kursen använder vi [Postman](https://www.getpostman.com/). Du kan använda en annan REST-klient om du är nöjd med en viss.

Starta den när du har installerat Postman.

Om det här är första gången du REST-anrop till Azure här är en kort introduktion av viktiga komponenter för den här självstudiekursen: begäran-metoden för varje anrop i den här kursen är ”POST”. Rubrik-nycklar är ”Content-type” och ”api-nyckel”. Värden i huvudet nycklar är ”application/json” och din ”administrationsnyckeln” (admin-nyckel är en platshållare för sökning primärnyckel) respektive. Innehållet är placerar du det faktiska innehållet i samtalet. Det kan finnas vissa varianter på hur du utformar din fråga beroende på klienten som du använder, men de används grunderna.

  ![Halvstrukturerade sökning](media/search-semi-structured-data/postmanoverview.png)

Din sökning api-nyckel krävs för REST-anrop som beskrivs i den här självstudiekursen. Du kan hitta din api-nyckel under **nycklar** i din söktjänst. Den här api-nyckeln måste vara i rubriken för varje API-anrop (Ersätt ”administrationsnyckeln” i den föregående skärmbilden med) den här kursen riktar du kan göra. Behålla nyckeln eftersom du behöver för varje anrop.

  ![Halvstrukturerade sökning](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Hämta exempeldata

Data exempeldata har förberetts för dig. **Hämta [kliniska försök json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)**  och packa till sin egen mapp.

I exemplet är exempel JSON-filer som ursprungligen text filer som hämtats från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Vi har konvertera dem till JSON för din bekvämlighet.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Ladda upp exempeldata

I Azure portal, gå tillbaka till det lagringskonto som skapats i den [tidigare kursen](../storage/blobs/storage-unstructured-search.md). Öppna den **data** behållare och klicka på **överför**.

Klicka på **Avancerat**, ange ”kliniska-utvärderingar-json” och sedan ladda upp alla JSON-filer som du hämtat.

  ![Halvstrukturerade sökning](media/search-semi-structured-data/clinicalupload.png)

När överföringen är klar ska filerna visas i sin egen undermapp i behållaren data.

## <a name="connect-your-search-service-to-your-container"></a>Ansluta din söktjänst till din behållaren

Vi använder Postman för att göra tre API-anrop till din söktjänst för att skapa en datakälla, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

Frågesträngen måste innehålla **api-version = 2016-09-01-Preview** och varje anrop ska returnera en **201 Skapad**. Den allmänt tillgängliga api-versionen har inte möjlighet att hantera json som en jsonArray, för närvarande endast preview api-version har ännu.

Kör följande tre API-anrop från REST-klient.

### <a name="create-a-datasource"></a>Skapa en datakälla

En datakälla anger vilka data till index.

Slutpunkten för det här anropet är `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på din söktjänst.

För det här anropet måste namnet på ditt lagringskonto och din lagringskontonyckel. Lagringskontots åtkomstnyckel kan hittas i Azure-portalen i ditt lagringskonto **åtkomstnycklar**. Platsen visas i följande bild:

  ![Halvstrukturerade sökning](media/search-semi-structured-data/storagekeys.png)

Ersätt den `[storage account name]` och `[storage account key]` i brödtexten för samtalet innan anropet.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

Svaret ska se ut:

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

URL: en för det här anropet är `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på din söktjänst.

Ersätt först URL: en. Kopiera och klistra in följande kod i din brödtext och köra frågan.

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

Svaret ska se ut:

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

En indexerare ansluter datakällan till sökindex mål och ger eventuellt ett schema för att automatisera datauppdateringen.

URL: en för det här anropet är `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på din söktjänst.

Ersätt först URL: en. Kopiera och klistra in följande kod i din brödtext och köra frågan.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Svaret ska se ut:

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

Nu när din söktjänst har anslutits till en databehållare för dina, kan du börja söka dina filer.

Öppna Azure-portalen och gå tillbaka till din söktjänst. Precis som i föregående självstudiekursen.

  ![Ostrukturerade sökning](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Användardefinierade metadata sökning

Som tidigare kan data kan efterfrågas på ett antal olika sätt: fulltextsökning, Systemegenskaper eller användardefinierade metadata. Både Systemegenskaper och användardefinierade metadata kan bara genomsökas med den `$select` parametern om de har markerats som **strängfält** under skapandet av index för målet. Parametrarna i indexet får inte ändras när de skapas. Dock kan ytterligare parametrar som läggas till.

Ett exempel på en fråga är `$select=Gender,metadata_storage_size`, vilket begränsar återgå till de två parametrarna.

  ![Halvstrukturerade sökning](media/search-semi-structured-data/lastquery.png)

Ett exempel på en mer komplex fråga skulle vara `$filter=MinimumAge ge 30 and MaximumAge lt 75`, som returnerar endast resultat där parametrarna MinimumAge är större än eller lika med 30 och MaximumAge är mindre än 75.

  ![Halvstrukturerade sökning](media/search-semi-structured-data/metadatashort.png)

Passa på att göra det om du vill experimentera och prova några fler frågor själv. Vet att du kan använda logiska operatorer (och, eller inte) och jämförelseoperatorer (eq, ne, gt, lt, ge, le). Strängjämförelser är skiftlägeskänsliga.

Den `$filter` parametern fungerar endast med metadata som har markerats filtrera vid skapandet av ditt index.

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om att söka halvstrukturerade data med Azure search, till exempel att:

> [!div class="checklist"]
> * Skapa ett Azure Search-tjänsten med hjälp av REST-API
> * Använda Azure Search-tjänsten för att söka i behållaren

Den här länken om du vill veta mer om sökningen.

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)