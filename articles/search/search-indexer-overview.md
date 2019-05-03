---
title: Indexerare för crawlning datakällor under indexering – Azure Sök
description: Crawla en Azure SQL-databas, Azure Cosmos DB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Search-index.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e35573eea836fc8a88c7515409c070ec63aa3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024899"
---
# <a name="indexers-in-azure-search"></a>Indexerare i Azure Search

En *indexeraren* i Azure Search är en crawler som extraherar sökbara data och metadata från en extern datakälla för Azure och fylla ett index baserat på fältet till fält-mappningar mellan indexet och din datakälla. Den här metoden kallas ibland en pull modell eftersom tjänsten samlar in data utan att du behöver skriva någon kod som lägger till data i ett index.

Indexerare baseras på typer av datakällor och plattformar, med separata indexerare för SQL Server på Azure, Cosmos DB, Azure Table Storage och Blob Storage. BLOB storage-indexerare har ytterligare egenskaper som är specifika för blob-innehållstyper.

Du kan använda en indexerare som enda metod för datapåfyllning eller använda en kombination av tekniker som inbegriper en indexerare för att läsa in bara några av fälten i ditt index.

Du kan köra indexerare på begäran eller enligt ett återkommande datauppdateringsschema som körs så ofta som var 15:e minut. Mer frekventa uppdateringar kräver en push-modell som uppdaterar data i Azure Search och i din externa datakälla samtidigt.

## <a name="approaches-for-creating-and-managing-indexers"></a>Metoder för att skapa och hantera indexerare

Du kan skapa och hantera indexerare med hjälp av följande metoder:

* [Portal > guiden Importera Data](search-import-data-portal.md)
* [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Varje ny indexerare lanseras först som en förhandsversion av funktionen. Funktionerna i förhandsversionen introduceras via API:er (REST och .NET) och integreras sedan i portalen när de blivit allmänt tillgängliga. Om du utvärderar en ny indexerare bör du även göra upp en plan för att skriva kod.

## <a name="permissions"></a>Behörigheter

Alla åtgärder som rör indexerare, inklusive GET-begäranden för status eller definitioner, kräver en [api-administratörsnyckel](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

Indexerare crawla datalager på Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> Azure Table Storage stöds inte för [kognitiv sökning](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Grundläggande konfigurationssteg
Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En indexerare hämtar en anslutning till datakälla från en *datakälla* objekt. Definitionen av datakällan innehåller en anslutningssträng och eventuellt autentiseringsuppgifter. Anropa den [skapa Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API eller [DataSource klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) att skapa resursen.

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget.

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
En indexerare automatiserar vissa uppgifter som rör datapåfyllning, men att skapa ett index är vanligtvis inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Fält måste vara av namn och datatyp. Läs mer om att strukturera ett index, [skapa ett Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) eller [indexera klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Om du behöver hjälp med fältkopplingar kan du läsa mer i informationen om [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).

> [!Tip]
> Indexerare kan inte generera ett index åt dig, men du kan få hjälp av guiden **Importera data** i portalen. I de flesta fall kan guiden härleda ett indexschema från befintliga metadata i källan, vilket skapar ett preliminärt indexschema som du kan redigera direkt när guiden är aktiv. När indexet har skapats i tjänsten är ytterligare redigeringar i portalen i huvudsak begränsade till tillägg av nya fält. Överväg att använda guiden för att skapa, men inte revidera, ett index. I [steg-för-steg-beskrivningen för portalen](search-get-started-portal.md) kan du få en praktisk genomgång.

### <a name="step-3-create-and-schedule-the-indexer"></a>Steg 3: Skapa och Schemalägg indexeraren
Indexerardefinitionen är en konstruktion som samlar alla element som rör datapåfyllning. Obligatoriska elementen omfattar en datakälla och index. Valfria element innehåller ett schema och fält-mappningar. Fältet mappning är endast valfri om källan och index fält motsvara tydligt. En indexerare referera till en datakälla från en annan tjänst så länge som den datakällan är från samma prenumeration. Mer information om att strukturera en indexerare finns i [Skapa et indexerare (REST-API för Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Köra indexerare på begäran

Det är vanligt att schemalägga indexering, en indexerare också kan anropas på begäran med den [Körningskommando](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> När du kör API: et returnerar har, indexeraren anrop har schemalagts, men den faktiska bearbetningen sker asynkront. 

Du kan övervaka statusen indexerare i portalen eller via hämta API för indexerare Status. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Hämta status för indexerare

Du kan hämta status och körning historiken för en indexerare via den [hämta Status för indexeraren kommandot](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Svaret innehåller status för övergripande indexerare, senaste (eller pågående) indexer-anrop och historiken för de senaste indexer-anrop.

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

Körningshistorik innehåller upp till 50 senaste slutförda körningar, vilket är sorterade i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt grunden är nästa steg att granska krav och uppgifter som är specifika för varje typ av datakälla.

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indexera CSV-blobbar med Azure Search Blob-indexeraren](search-howto-index-csv-blobs.md)
* [Indexera JSON-blobbar med Azure Search Blob-indexeraren](search-howto-index-json-blobs.md)
