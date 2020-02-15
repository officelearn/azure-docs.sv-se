---
title: 'Självstudie: indexera halv strutured data i JSON-blobar'
titleSuffix: Azure Cognitive Search
description: 'Lär dig att indexera och söka i halv strukturerade Azure JSON-blobbar med Azure Kognitiv sökning REST-API: er och Postman.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0603ad1fbecf33e5880fd7f18d35af51795f8e39
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251999"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>REST-självstudie: indexera och Sök i halv strukturerade data (JSON-blobbar) i Azure Kognitiv sökning

Azure Kognitiv sökning kan indexera JSON-dokument och matriser i Azure Blob Storage med hjälp av en [indexerare](search-indexer-overview.md) som vet hur man kan läsa semi-strukturerade data. Halvstrukturerade data innehåller taggar eller märkord som separerar innehållet i data. Den delar skillnaden mellan ostrukturerade data som måste vara fullständigt indexerad och formellt strukturerade data som följer en data modell, till exempel ett Relations databas schema, som kan indexeras per fält.

I den här självstudien använder du [Azure KOGNITIV sökning REST-API: er](https://docs.microsoft.com/rest/api/searchservice/) och en rest-klient för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera en Azure Kognitiv sökning-datakälla för en Azure Blob-behållare
> * Skapa ett Azure Kognitiv sökning-index som innehåller sökbart innehåll
> * Konfigurera och kör en indexerare för att läsa behållaren och extrahera sökbart innehåll från Azure Blob Storage
> * Söka i indexet som du precis skapade

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data används i den här snabb starten. 

[Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien. 

[Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data.

[Postman-appen](https://www.getpostman.com/) för att skicka förfrågningar till Azure kognitiv sökning.

[Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) innehåller de data som används i den här självstudien. Ladda ned och zippa upp den här filen till en egen mapp. Data härstammar från [ClinicalTrials.gov](https://clinicaltrials.gov/ct2/results), konverteras till JSON för den här självstudien.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**får du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="prepare-sample-data"></a>Förbereda exempel data

1. [Logga](https://portal.azure.com)in på Azure Portal, navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempel data. Du kan ställa in den offentliga åtkomst nivån på alla giltiga värden.

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet.

   ![Ladda upp i kommando fältet](media/search-semi-structured-data/upload-command-bar.png "Ladda upp i kommando fältet")

1. Navigera till mappen som innehåller exempelfilerna. Markera alla och klicka sedan på **överför**.

   ![Överföra filer](media/search-semi-structured-data/clinicalupload.png "Överföra filer")

När överföringen är klar ska filerna visas i en egen undermapp i datacontainern.

## <a name="set-up-postman"></a>Konfigurera Postman

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget kan du läsa [utforska Azure KOGNITIV sökning REST-API: er med Postman](search-get-started-postman.md).

Metoden för begäran för varje anrop i den här självstudien är **post**. Huvudnycklarna är ”Content-type” och ”api-key”. Värdena för huvudnycklarna är "application/json" och din "admin key" (administratörsnyckeln är platshållare för din sökprimärnyckel). Meddelandetexten är där du placerar det faktiska innehållet i anropet. Hur du konstruerar frågan kan variera beroende på vilken klient du använder, men det här är grunderna.

  ![Halvstrukturerad sökning](media/search-semi-structured-data/postmanoverview.png)

Vi använder Postman för att göra tre API-anrop till din söktjänst för att skapa en datakälla, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och dina JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

Frågesträngar måste ange en API-version och varje anrop ska returnera en **201 som skapats**. Den allmänt tillgängliga API-versionen för att använda JSON-matriser är `2019-05-06`.

Kör följande tre API-anrop från REST-klienten.

## <a name="create-a-data-source"></a>Skapa en datakälla

Med [skapa data källans API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) skapas ett Azure kognitiv sökning-objekt som anger vilka data som ska indexeras.

Slutpunkten för anropet är `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Ersätt `[service name]` med namnet på söktjänsten. 

För det här anropet måste begär ande texten innehålla namnet på ditt lagrings konto, lagrings konto nyckel och namn på BLOB container. Lagringskontonyckeln hittar du i **Åtkomstnycklar** i ditt lagringskonto i Azure Portal. Platsen visas på följande bild:

  ![Halvstrukturerad sökning](media/search-semi-structured-data/storagekeys.png)

Se till att ersätta `[storage account name]`, `[storage account key]`och `[blob container name]` i meddelande texten i ditt anrop innan du kör anropet.

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
    
Det andra anropet är [skapa index-API](https://docs.microsoft.com/rest/api/searchservice/create-index), vilket skapar ett Azure kognitiv sökning-index som lagrar alla sökbara data. Ett index anger alla parametrar och deras attribut.

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

En indexerare ansluter data källan, importerar data till mål Sök indexet och tillhandahåller eventuellt ett schema för att automatisera data uppdateringen. REST API är [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

URL:en för det här anropet är `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Ersätt `[service name]` med namnet på söktjänsten.

Ersätt först URL:en. Kopiera och klistra sedan in följande kod i bröd texten och skicka begäran. Begäran bearbetas omedelbart. När svaret kommer tillbaka har du ett index som är full text sökbar.

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

Du kan börja söka så snart det första dokumentet har lästs in. För den här uppgiften använder du [**Sök Utforskaren**](search-explorer.md) i portalen.

I Azure Portal öppnar du sidan Sök tjänst **Översikt** och letar reda på det index som du skapade i listan **index** .

Se till att välja det index som du nyss skapade. 

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

Det snabbaste sättet att rensa efter en själv studie kurs är att ta bort resurs gruppen som innehåller Azure Kognitiv sökning-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen finns resurs gruppens namn på sidan Översikt i Azure Kognitiv sökning-tjänsten.

## <a name="next-steps"></a>Nästa steg

Det finns flera metoder och flera alternativ för att indexera JSON-blobbar. I nästa steg ska du granska och testa de olika alternativen för att se vad som passar bäst för ditt scenario.

> [!div class="nextstepaction"]
> [Så här indexerar du JSON-blobbar med Azure Kognitiv sökning BLOB-indexeraren](search-howto-index-json-blobs.md)
