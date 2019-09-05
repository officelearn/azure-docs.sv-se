---
title: Indexerare för crawlning av data källor under indexering – Azure Search
description: Crawla en Azure SQL-databas, Azure Cosmos DB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Search-index.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: e50d88181a27dcc46da858f220404eb09ad9b4bd
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308971"
---
# <a name="indexers-in-azure-search"></a>Indexerare i Azure Search

En *indexerare* i Azure Search är en Crawler som extraherar sökbara data och metadata från en extern Azure-datakälla och fyller ett index baserat på fält-till-fält-mappningar mellan indexet och data källan. Den här metoden kallas ibland för "pull-modell" eftersom tjänsten hämtar data i utan att du behöver skriva kod som lägger till data i ett index.

Indexerare baseras på data käll typer eller plattformar, med enskilda indexerare för SQL Server på Azure, Cosmos DB Azure Table Storage och Blob Storage. Blob Storage-indexerare har ytterligare egenskaper som är speciella för BLOB-innehållstyp.

Du kan använda en indexerare som enda metod för datapåfyllning eller använda en kombination av tekniker som inbegriper en indexerare för att läsa in bara några av fälten i ditt index.

Du kan köra indexerare på begäran eller enligt ett återkommande data uppdaterings schema som körs så ofta som var femte minut. Mer frekventa uppdateringar kräver en push-modell som uppdaterar data i Azure Search och i din externa datakälla samtidigt.

## <a name="approaches-for-creating-and-managing-indexers"></a>Metoder för att skapa och hantera indexerare

Du kan skapa och hantera indexerare med hjälp av följande metoder:

* [Portal > guiden Importera data](search-import-data-portal.md)
* [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Varje ny indexerare lanseras först som en förhandsversion av funktionen. Funktionerna i förhandsversionen introduceras via API:er (REST och .NET) och integreras sedan i portalen när de blivit allmänt tillgängliga. Om du utvärderar en ny indexerare bör du även göra upp en plan för att skriva kod.

## <a name="permissions"></a>Behörigheter

Alla åtgärder som rör indexerare, inklusive GET-begäranden för status eller definitioner, kräver en [admin-API-nyckel](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

Indexerare söker efter data lager i Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)

## <a name="basic-configuration-steps"></a>Grundläggande konfigurationssteg
Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En indexerare hämtar data källans lutning från ett *data käll* objekt. Definitionen av data källan tillhandahåller en anslutnings sträng och eventuellt autentiseringsuppgifter. Anropa klassen [create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API eller [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) för att skapa resursen.

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget.

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
En indexerare automatiserar vissa uppgifter som rör datapåfyllning, men att skapa ett index är vanligtvis inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Fält måste matcha efter namn och datatyp. Mer information om hur du strukturerar ett index finns i [skapa ett index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) eller en [index klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Om du behöver hjälp med fältkopplingar kan du läsa mer i informationen om [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).

> [!Tip]
> Indexerare kan inte generera ett index åt dig, men du kan få hjälp av guiden **Importera data** i portalen. I de flesta fall kan guiden härleda ett indexschema från befintliga metadata i källan, vilket skapar ett preliminärt indexschema som du kan redigera direkt när guiden är aktiv. När indexet har skapats i tjänsten är ytterligare redigeringar i portalen i huvudsak begränsade till tillägg av nya fält. Överväg att använda guiden för att skapa, men inte revidera, ett index. I [steg-för-steg-beskrivningen för portalen](search-get-started-portal.md) kan du få en praktisk genomgång.

### <a name="step-3-create-and-schedule-the-indexer"></a>Steg 3: Skapa och Schemalägg indexeraren
Indexer-definitionen är en konstruktion som sammanställer alla element som är relaterade till data inmatning. De element som krävs är en data källa och ett index. Valfria element innehåller ett schema och fält mappningar. Fält mappning är bara valfritt om käll fält och index fält tydligt motsvarar. En indexerare referera till en datakälla från en annan tjänst så länge som den datakällan är från samma prenumeration. Mer information om att strukturera en indexerare finns i [Skapa et indexerare (REST-API för Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Köra indexerare på begäran

Även om det är vanligt att schemalägga indexering kan en indexerare också anropas på begäran med [kommandot kör](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> När körnings-API: t återgår har indexerings anropet schemalagts, men den faktiska bearbetningen sker asynkront. 

Du kan övervaka indexerings status i portalen eller genom att hämta indexerings status-API. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Hämta indexerings status

Du kan hämta status och körnings historik för en indexerare med hjälp av [status kommandot Get indexerare](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Svaret innehåller övergripande indexerings status, det sista (eller inaktuella) indexerings anropet och historiken för senaste indexerare-anrop.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Körnings historiken innehåller upp till 50 senaste slutförda körningar, som sorteras i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt grunden är nästa steg att granska krav och uppgifter som är specifika för varje typ av datakälla.

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indexera CSV-blobbar med Azure Search Blob-indexeraren](search-howto-index-csv-blobs.md)
* [Indexera JSON-blobbar med Azure Search Blob-indexeraren](search-howto-index-json-blobs.md)
