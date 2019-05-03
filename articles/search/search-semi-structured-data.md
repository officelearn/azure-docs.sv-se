---
title: 'Självstudier: Indexering halvstrukturerade strutured data i JSON-blobar – Azure Search'
description: 'Lär dig mer om att indexera och söka efter halvstrukturerade Azure JSON-blobar med hjälp av Azure Search REST API: er och Postman.'
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4b996effbc03bd1f7c446965b0aa5fb6fa2d0175
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024377"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>REST-självstudiekurs: Indexera och söka efter halvstrukturerade data (JSON-blobar) i Azure Search

Azure Search kan indexera JSON-dokument och matriser i Azure blob storage med en [indexeraren](search-indexer-overview.md) som vet hur du läser halvstrukturerade data. Halvstrukturerade data innehåller taggar eller märkord som separerar innehållet i data. Skillnaden mellan Ostrukturerade data, som måste indexeras fullständigt och formellt strukturerade data som följer en datamodell, till exempel en relationsdatabas-schema, som kan indexeras på basis av per fält delas.

I den här självstudien använder den [Azure Search REST API: er](https://docs.microsoft.com/rest/api/searchservice/) och en REST-klient för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera en Azure Search-datakälla för en Azure-blobcontainer
> * Skapa ett Azure Search-index ska innehålla sökbart innehåll
> * Konfigurera och köra en indexerare för att läsa behållaren och extrahera sökbart innehåll från Azure blob storage
> * Söka i indexet som du precis skapade

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster, verktyg och data som används i den här snabbstarten. 

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen. 

[Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempeldata.

[Skrivbordsappen postman](https://www.getpostman.com/) för att skicka begäranden till Azure Search.

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) innehåller de data som används i den här självstudien. Hämta och packa upp filen till sin egen mapp. Data samlas in från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), konvertera till JSON för den här självstudiekursen.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-data"></a>Förbereda exempeldata

1. [Logga in på Azure-portalen](https://portal.azure.com)navigerar du till ditt Azure storage-konto, klickar du på **Blobar**, och klicka sedan på **+ behållare**.

1. [Skapa en blobbehållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempeldata. Du kan ange offentlig åtkomstnivå till någon av dess giltiga värden.

1. När behållaren har skapats kan du öppna den och välj **överför** i kommandofältet.

   ![Ladda upp i kommandofältet](media/search-semi-structured-data/upload-command-bar.png "överför i kommandofältet")

1. Navigera till mappen som innehåller exempelfilerna. Markera alla av dem och klicka sedan på **överför**.

   ![Ladda upp filer](media/search-semi-structured-data/clinicalupload.png "ladda upp filer")

När överföringen är klar ska filerna visas i en egen undermapp i datacontainern.

## <a name="set-up-postman"></a>Konfigurera Postman

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget, se [utforska Azure Search REST API: er med Postman](search-fiddler.md).

Metoden för begäran för varje anrop i den här självstudien är **POST**. Huvudnycklarna är ”Content-type” och ”api-key”. Värdena för huvudnycklarna är "application/json" och din "admin key" (administratörsnyckeln är platshållare för din sökprimärnyckel). Meddelandetexten är där du placerar det faktiska innehållet i anropet. Hur du konstruerar frågan kan variera beroende på vilken klient du använder, men det här är grunderna.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/postmanoverview.png)

Vi använder Postman för att göra tre API-anrop till din söktjänst för att skapa en datakälla, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och dina JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

Fråga strängar måste ange en api-versionen och varje anrop ska returnera en **201 Skapad**. Den allmänt tillgängliga api-versionen för att använda JSON-matriser är `2019-05-06`.

Kör följande tre API-anrop från REST-klienten.

## <a name="create-a-data-source"></a>Skapa en datakälla

Den [skapa API för Data källan](https://docs.microsoft.com/rest/api/searchservice/create-data-source)skapar ett Azure Search-objekt som anger vilka data som ska indexeras.

Slutpunkten för anropet är `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Ersätt `[service name]` med namnet på söktjänsten. 

Begärandetexten måste innehålla namnet på ditt lagringskonto, din lagringskontonyckel och blob-behållarnamn för det här anropet. Lagringskontonyckeln hittar du i **Åtkomstnycklar** i ditt lagringskonto i Azure Portal. Platsen visas på följande bild:

  ![Halvstrukturerad sökning](media/search-semi-structured-data/storagekeys.png)

Ersätt `[storage account name]`, `[storage account key]`, och `[blob container name]` i meddelandetexten för anropet innan du utför anropet.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
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
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Skapa ett index
    
Det andra anropet är [Create Index-API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), skapa ett Azure Search-index som lagrar alla sökbara data. Ett index anger alla parametrar och deras attribut.

URL:en för det här anropet är `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Ersätt `[service name]` med namnet på söktjänsten.

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

En indexerare ansluter datakällan, importerar data till målsökindex och tillhandahåller eventuellt ett schema för att automatisera datauppdateringen. REST-API: et är [skapa et indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

URL:en för det här anropet är `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Ersätt `[service name]` med namnet på söktjänsten.

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

Du kan börja söka så fort det första dokumentet har lästs in. Den här uppgiften ska använda [ **Sökutforskaren** ](search-explorer.md) i portalen.

I Azure-portalen öppnar du söktjänsten **översikt** sidan, hitta det index som du skapade i den **index** lista.

Var noga med att välja det index som du nyss skapade. 

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

Det finns flera metoder och flera olika sätt att indexera JSON-blobar. I nästa steg, granska och testa de olika alternativen för att se vad som fungerar bäst för ditt scenario.

> [!div class="nextstepaction"]
> [Indexera JSON-blobar med Azure Search Blob-indexeraren](search-howto-index-json-blobs.md)