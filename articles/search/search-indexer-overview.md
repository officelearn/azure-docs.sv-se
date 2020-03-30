---
title: Indexerare för crawlning av data under import
titleSuffix: Azure Cognitive Search
description: Crawla Azure SQL-databas, Azure Cosmos DB eller Azure-lagring för att extrahera sökbara data och fylla i ett Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282996"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexerare i Azure Cognitive Search

En *indexerare* i Azure Cognitive Search är en sökrobot som extraherar sökbara data och metadata från en extern Azure-datakälla och fyller i ett index baserat på fält-till-fält-mappningar mellan indexet och datakällan. Den här metoden kallas ibland för en pull-modell eftersom tjänsten hämtar data utan att du behöver skriva någon kod som lägger till data i ett index.

Indexerare baseras på datakälltyper eller plattformar, med enskilda indexerare för SQL Server på Azure, Cosmos DB, Azure Table Storage och Blob Storage. Blob-lagringsindexerare har ytterligare egenskaper som är specifika för blob-innehållstyper.

Du kan använda en indexerare som enda metod för datapåfyllning eller använda en kombination av tekniker som inbegriper en indexerare för att läsa in bara några av fälten i ditt index.

Du kan köra indexerare på begäran eller på ett återkommande datauppdateringsschema som körs lika ofta som var femte minut. Mer frekventa uppdateringar kräver en push-modell som samtidigt uppdaterar data i både Azure Cognitive Search och din externa datakälla.

## <a name="approaches-for-creating-and-managing-indexers"></a>Metoder för att skapa och hantera indexerare

Du kan skapa och hantera indexerare med hjälp av följande metoder:

* [Guiden Importera > portal](search-import-data-portal.md)
* [Tjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Varje ny indexerare lanseras först som en förhandsversion av funktionen. Funktionerna i förhandsversionen introduceras via API:er (REST och .NET) och integreras sedan i portalen när de blivit allmänt tillgängliga. Om du utvärderar en ny indexerare bör du även göra upp en plan för att skriva kod.

## <a name="permissions"></a>Behörigheter

Alla åtgärder som är relaterade till indexerare, inklusive GET-begäranden om status eller definitioner, kräver en [api-nyckel](search-security-api-keys.md)för administratörer . 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

Indexerare genomsöker datalager på Azure.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (i förhandsversion)
* [Lagring av Azure-bord](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server på Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL-hanterade instanser på Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Grundläggande konfigurationssteg
Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En indexerare hämtar datakällanslutning från ett *datakällobjekt.* Datakälldefinitionen tillhandahåller en anslutningssträng och eventuellt autentiseringsuppgifter. Anropa klassen [Skapa DATASOURCE](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API eller [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) för att skapa resursen.

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget.

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
En indexerare automatiserar vissa uppgifter som rör datapåfyllning, men att skapa ett index är vanligtvis inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Fält måste matcha efter namn och datatyp. Mer information om hur du strukturerar ett index finns i [Skapa ett index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) eller [Indexklass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Hjälp med fältassociationer finns [i Fältmappningar i Azure Cognitive Search-indexerare](search-indexer-field-mappings.md).

> [!Tip]
> Indexerare kan inte generera ett index åt dig, men du kan få hjälp av guiden **Importera data** i portalen. I de flesta fall kan guiden härleda ett indexschema från befintliga metadata i källan, vilket skapar ett preliminärt indexschema som du kan redigera direkt när guiden är aktiv. När indexet har skapats i tjänsten är ytterligare redigeringar i portalen i huvudsak begränsade till tillägg av nya fält. Överväg att använda guiden för att skapa, men inte revidera, ett index. I [steg-för-steg-beskrivningen för portalen](search-get-started-portal.md) kan du få en praktisk genomgång.

### <a name="step-3-create-and-schedule-the-indexer"></a>Steg 3: Skapa och schemalägg indexeraren
Indexerdefinitionen är en konstruktion som sammanför alla element som är relaterade till datainmatning. Obligatoriska element inkluderar en datakälla och ett index. Valfria element inkluderar ett schema och fältmappningar. Fältmappning är bara valfritt om källfält och indexfält tydligt stämmer överens. Mer information om hur du strukturerar en indexerare finns i [Skapa Indexer (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Kör indexerare på begäran

Även om det är vanligt att schemalägga indexering, kan en indexerare också anropas på begäran med [kommandot Kör:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> När Kör API-returer har indexerarens åkallan schemalagts, men den faktiska bearbetningen sker asynkront. 

Du kan övervaka indexerarstatusen i portalen eller via Hämta api för indexeringsstatus. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Hämta indexerarstatus

Du kan hämta status- och körningshistoriken för en indexerare via [kommandot Hämta indexerarestatus:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Svaret innehåller övergripande indexerarstatus, den sista (eller pågående) indexerarens åkallan och historiken för de senaste indexeringsyrkena.

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

Körningshistorik innehåller upp till de 50 senaste slutförda avrättningarna, som sorteras i omvänd kronologisk ordning (så den senaste körningen kommer först i svaret).

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt grunden är nästa steg att granska krav och uppgifter som är specifika för varje typ av datakälla.

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Lagring av Azure-bord](search-howto-indexing-azure-tables.md)
* [Indexera CSV-blobbar med hjälp av Azure Cognitive Search Blob-indexeraren](search-howto-index-csv-blobs.md)
* [Indexering av JSON-blobbar med Azure Cognitive Search Blob-indexerare](search-howto-index-json-blobs.md)
