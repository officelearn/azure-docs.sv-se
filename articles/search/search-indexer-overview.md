---
title: Indexerare med Azure Search| Microsoft Docs
description: Crawla en Azure SQL-databas, Azure Cosmos DB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Search-index.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: heidist
ms.openlocfilehash: 8def65c15d631909c69428a1cb5f100beb1f9b08
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="indexers-in-azure-search"></a>Indexerare i Azure Search
> [!div class="op_single_selector"]
>
> * [Översikt](search-indexer-overview.md)
> * [Portalen](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-cosmosdb.md)
> * [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
> * [Azure Table Storage](search-howto-indexing-azure-tables.md)
>

En *indexerare* i Azure Search är en crawler som extraherar sökbara data och metadata från en extern datakälla och som fyller ett index baserat på fält-till-fält-mappningar mellan indexet och din datakälla. Den här metoden kallas ibland för en pull-modell eftersom tjänsten samlar in data utan att du behöver skriva någon kod som skickar data till ett index.

Indexerarna baseras på de typer av datakällor och plattformar som finns tillgängliga. Det finns separata indexerare för SQL Server på Azure, Azure Cosmos DB, Azure Table Storage, Blob Storage och så vidare.

Du kan använda en indexerare som enda metod för datapåfyllning eller använda en kombination av tekniker som inbegriper en indexerare för att läsa in bara några av fälten i ditt index.

Du kan köra indexerare på begäran eller enligt ett återkommande datauppdateringsschema som körs så ofta som var 15:e minut. Mer frekventa uppdateringar kräver en push-modell som uppdaterar data i Azure Search och i din externa datakälla samtidigt.

## <a name="approaches-for-creating-and-managing-indexers"></a>Metoder för att skapa och hantera indexerare

Du kan skapa och hantera indexerare med hjälp av följande metoder:

* [Portal > Guiden Importera Data](search-get-started-portal.md)
* [Tjänsten REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Varje ny indexerare lanseras först som en förhandsversion av funktionen. Funktionerna i förhandsversionen introduceras via API:er (REST och .NET) och integreras sedan i portalen när de blivit allmänt tillgängliga. Om du utvärderar en ny indexerare bör du även göra upp en plan för att skriva kod.

## <a name="basic-configuration-steps"></a>Grundläggande konfigurationssteg
Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En indexerare hämtar data från en *datakälla* som innehåller information som till exempel en anslutningssträng och autentiseringsuppgifter. För närvarande stöds följande datakällor:

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) för utvalda innehållstyper
* [Azure Table Storage](search-howto-indexing-azure-tables.md)

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget.

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
En indexerare automatiserar vissa uppgifter som rör datapåfyllning, men att skapa ett index är vanligtvis inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Mer information om att strukturera ett index finns i [Skapa ett Index (REST-API för Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Om du behöver hjälp med fältkopplingar kan du läsa mer i informationen om [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).

> [!Tip]
> Indexerare kan inte generera ett index åt dig, men du kan få hjälp av guiden **Importera data** i portalen. I de flesta fall kan guiden härleda ett indexschema från befintliga metadata i källan, vilket skapar ett preliminärt indexschema som du kan redigera direkt när guiden är aktiv. När indexet har skapats i tjänsten är ytterligare redigeringar i portalen i huvudsak begränsade till tillägg av nya fält. Överväg att använda guiden för att skapa, men inte revidera, ett index. I [steg-för-steg-beskrivningen för portalen](search-get-started-portal.md) kan du få en praktisk genomgång.

### <a name="step-3-create-and-schedule-the-indexer"></a>Steg 3: Skapa och schemalägg indexeraren
Indexerardefinitionen är en konstruktion som specificerar index, datakälla och schema. En indexerare referera till en datakälla från en annan tjänst så länge som den datakällan är från samma prenumeration. Mer information om att strukturera en indexerare finns i [Skapa et indexerare (REST-API för Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt grunden är nästa steg att granska krav och uppgifter som är specifika för varje typ av datakälla.

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indexera CSV-blobbar med Azure Search Blob-indexeraren](search-howto-index-csv-blobs.md)
* [Indexera JSON-blobbar med Azure Search Blob-indexeraren](search-howto-index-json-blobs.md)
