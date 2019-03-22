---
title: Självstudie för att söka efter JSON i Azure Blob Storage – Azure Search
description: I den här självstudien får du lära dig att söka i halvstrukturerade Azure-blobdata med Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201355"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Självstudie: Söka efter halvstrukturerade data i Azure-molnlagring

Azure Search kan indexera JSON-dokument och matriser i Azure blob storage med en [indexeraren](search-indexer-overview.md) som vet hur du läser halvstrukturerade data. Halvstrukturerade data innehåller taggar eller märkord som separerar innehållet i data. Skillnaden mellan Ostrukturerade data, som måste indexeras fullständigt och formellt strukturerade data som följer en datamodell, till exempel en relationsdatabas-schema, som kan indexeras på basis av per fält delas.

I den här självstudien använder den [Azure Search REST API: er](https://docs.microsoft.com/rest/api/searchservice/) och en REST-klient för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera en Azure Search-datakälla för en Azure-blobcontainer
> * Skapa ett Azure Search-index ska innehålla sökbart innehåll
> * Konfigurera och köra en indexerare för att läsa behållaren och extrahera sökbart innehåll från Azure blob storage
> * Söka i indexet som du precis skapade

> [!NOTE]
> I den här kursen används JSON-matriser som för närvarande är en förhandsgranskningsfunktion i Azure Search. Den finns inte i portalen. Därför använder vi förhandsversionen av REST API som tillhandahåller den här funktionen och ett REST-klientverktyg för att anropa API:t.

## <a name="prerequisites"></a>Förutsättningar

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen.

[Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) som innehåller exempeldata.

[Använda Postman](https://www.getpostman.com/) eller någon annan REST-klient för att skicka dina önskemål. Anvisningar för hur du konfigurerar en HTTP-begäran i Postman finns i nästa avsnitt.

## <a name="set-up-postman"></a>Konfigurera Postman

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget, se [utforska Azure Search REST API: er med Postman](search-fiddler.md).

Metoden för begäran för varje anrop i den här kursen är ”POST”. Huvudnycklarna är ”Content-type” och ”api-key”. Värdena för huvudnycklarna är "application/json" och din "admin key" (administratörsnyckeln är platshållare för din sökprimärnyckel). Meddelandetexten är där du placerar det faktiska innehållet i anropet. Hur du konstruerar frågan kan variera beroende på vilken klient du använder, men det här är grunderna.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/postmanoverview.png)

För REST-anropen som används i den här kursen krävs din sök-api-nyckel. Du hittar din api-nyckel under **Nycklar** i din söktjänst. Api-nyckeln måste anges i huvudet i varje API-anrop (ersätt ”admin key” i föregående skärmbild med den) som du gör i den här kursen. Behåll nyckeln eftersom du behöver den för varje anrop.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Förbereda exempeldata

1. **Ladda ned [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** och extrahera den i dess mapp. Data samlas in från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), konvertera till JSON för den här självstudiekursen.

2. Logga in på den [Azure-portalen](https://portal.azure.com), gå till Azure storage-kontot, öppna den **data** behållare och klickar på **överför**.

3. Klicka på **Avancerat**, ange ”clinical-trials-json” och ladda sedan upp alla JSON-filer som du hämtade.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/clinicalupload.png)

När överföringen är klar ska filerna visas i en egen undermapp i datacontainern.

## <a name="connect-your-search-service-to-your-container"></a>Ansluta din söktjänst till containern

Vi använder Postman för att göra tre API-anrop till din söktjänst för att skapa en datakälla, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och dina JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

Frågesträngen måste innehålla en förhandsversionen av API (till exempel **api-version = 2017-11-11-Preview**) och varje anrop ska returnera en **201 Skapad**. Den allmänt tillgängliga api-versionen kan inte ännu hantera JSON som en JSON-matris. Det kan för närvarande endast förhandsgranskningsversionen av api-versionen.

Kör följande tre API-anrop från REST-klienten.

## <a name="create-a-data-source"></a>Skapa en datakälla

En datakälla är ett Azure Search-objekt som anger vilka data som ska indexeras.

Slutpunkten för anropet är `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på söktjänsten. För det här anropet behöver du namnet på lagringskontot och lagringskontonyckeln. Lagringskontonyckeln hittar du i **Åtkomstnycklar** i ditt lagringskonto i Azure Portal. Platsen visas på följande bild:

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

## <a name="create-an-index"></a>Skapa ett index
    
Det andra API-anropet skapar ett Azure Search-index. Ett index anger alla parametrar och deras attribut.

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

## <a name="create-and-run-an-indexer"></a>Skapa och köra en indexerare

En indexerare ansluter datakällan, importerar data till målsökindex och tillhandahåller eventuellt ett schema för att automatisera datauppdateringen.

URL:en för det här anropet är `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Ersätt `[service name]` med namnet på söktjänsten.

Ersätt först URL:en. Kopiera och klistra in följande kod i meddelandetexten och skicka begäran. Begäran bearbetas omedelbart. När svaret kommer tillbaka, har du ett index som är fulltext sökbara.

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

Nu kan du skicka frågor mot indexet. Den här uppgiften ska använda [ **Sökutforskaren** ](search-explorer.md) i portalen.

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

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen ser du resursgruppens namn på översiktssidan för Azure Search-tjänsten.

## <a name="next-steps"></a>Nästa steg

Du kan koppla AI-drivna algoritmer till en indexerarpipeline. I nästa steg fortsätter du med följande självstudie:

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)