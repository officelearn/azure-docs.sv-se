---
title: Indexerare med Azure Search| Microsoft Docs
description: "Crawla Azure SQL-databasen, DocumentDB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Search-index."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 6ff31940f3a4e7557e0caf3d9d3740590be3bc04
ms.openlocfilehash: 9a8a4454a0676c403356e9989665242978949011


---
# <a name="indexers-in-azure-search"></a>Indexerare i Azure Search
> [!div class="op_single_selector"]
> * [Översikt](search-indexer-overview.md)
> * [Portalen](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
> * [DocumentDB](../documentdb/documentdb-search-indexer.md)
> * [Blob Storage (förhandsversion)](search-howto-indexing-azure-blob-storage.md)
> * [Table Storage (förhandsversion)](search-howto-indexing-azure-tables.md)
> 
> 

En **indexerare** i Azure Search är en crawler som extraherar sökbara data och metadata från en extern datakälla och som fyller ett index baserat på fält-till-fält-mappningar mellan indexet och din datakälla. Den här metoden kallas ibland för en pull-modell eftersom tjänsten samlar in data utan att du behöver skriva någon kod som skickar data till ett index.

Du kan använda en indexerare som enda metod för datapåfyllning eller använda en kombination av tekniker som inbegriper en indexerare för att läsa in bara några av fälten i ditt index.

Du kan köra indexerare på begäran eller enligt ett återkommande datauppdateringsschema som körs så ofta som var 15:e minut. Mer frekventa uppdateringar kräver en push-modell som uppdaterar data i Azure Search och i din externa datakälla samtidigt.

## <a name="approaches-for-creating-and-managing-indexers"></a>Metoder för att skapa och hantera indexerare
För allmänt tillgängliga indexerare, som Azure SQL och DocumentDB, kan du skapa och hantera indexerare med hjälp av följande metoder:

* [Portal > Guiden Importera Data](search-get-started-portal.md)
* [Tjänsten REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Förhandsgranskning av indexerare, som Azure Blob eller Table Storage, kräver kod- och förhandsgransknings-API:er, som [Azure Search Preview REST API för indexerare](search-api-indexers-2015-02-28-preview.md). Portalverktygsuppsättningen är i normalfallet inte tillgänglig för förhandsgranskningsfunktioner.

## <a name="basic-configuration-steps"></a>Grundläggande konfigurationssteg
Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-an-index"></a>Steg 1: Skapa ett index
En indexerare automatiserar vissa uppgifter som rör datainhämtning, men att skapa ett index är inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Mer information om att strukturera ett index finns i [Skapa ett Index (REST-API för Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>Steg 2: Skapa en datakälla
En indexerare hämtar data från en **datakälla** som innehåller information som till exempel en anslutningssträng. För närvarande stöds följande datakällor:

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
* [DocumentDB](../documentdb/documentdb-search-indexer.md)
* [Azure Blob Storage (förhandsversion)](search-howto-indexing-azure-blob-storage.md) används för att extrahera text från PDF, Office-dokument, HTML eller XML
* [Azure Table Storage (förhandsversion)](search-howto-indexing-azure-tables.md)

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget. 

### <a name="step-3create-and-schedule-the-indexer"></a>Steg 3: Skapa och schemalägg indexeraren
Indexerardefinitionen är en konstruktion som specificerar index, datakälla och schema. En indexerare referera till en datakälla från en annan tjänst så länge som den datakällan är från samma prenumeration. Mer information om att strukturera en indexerare finns i [Skapa et indexerare (REST-API för Azure Search)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt grunden är nästa steg att granska krav och uppgifter som är specifika för varje typ av datakälla.

* [Azure SQL Database (eller SQL Server på en virtuell Azure-dator)](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
* [DocumentDB](../documentdb/documentdb-search-indexer.md)
* [Azure Blob Storage (förhandsversion)](search-howto-indexing-azure-blob-storage.md) används för att extrahera text från PDF, Office-dokument, HTML eller XML
* [Azure Table Storage (förhandsversion)](search-howto-indexing-azure-tables.md)
* [Indexera CSV-blobar med Azure Search Blob-indexeraren (förhandsversion)](search-howto-index-csv-blobs.md)
* [Indexera JSON-blobar med Azure Search-blob-indexeraren (förhandsversion)](search-howto-index-json-blobs.md)




<!--HONumber=Nov16_HO2-->


