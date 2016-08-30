<properties
    pageTitle="Indexerare i Azure Search | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Crawla Azure SQL-databasen, DocumentDB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Search-index."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="04/14/2016"
    ms.author="heidist"/>

# Indexerare i Azure Search
> [AZURE.SELECTOR]
- [Översikt](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [BLOB Storage (förhandsversion)](search-howto-indexing-azure-blob-storage.md)
- [Table Storage (förhandsversion)](search-howto-indexing-azure-tables.md)

En **indexerare** i Azure Search är en crawler som extraherar sökbara data och metadata från en extern datakälla och som fyller ett index baserat på fält-till-fält-mappningar mellan indexet och din datakälla. Den här metoden kallas ibland för en pull-modell eftersom tjänsten samlar in data utan att du behöver skriva någon kod som skickar data till ett index.

Du kan använda en indexerare som enda metod för datapåfyllning eller använda en kombination av tekniker som inbegriper en indexerare för att läsa in bara några av fälten i ditt index.

Indexerare kan köras på begäran eller enligt ett återkommande datauppdateringsschema som körs så ofta som var 15:e minut. Mer frekventa uppdateringar kräver en push-modell som uppdaterar data i Azure Search och i din externa datakälla samtidigt.

En indexerare hämtar data från en **datakälla** som innehåller information som till exempel en anslutningssträng. För närvarande stöds följande datakällor:

- [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (eller SQL Server på en virtuell Azure-dator)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (För närvarande endast tillgänglig som förhandsversion. Extraherar text från PDF, Office-dokument, HTML och  XML).
- [Azure Table Storage](search-howto-indexing-azure-tables.md) (för närvarande endast tillgänglig som förhandsversion)

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget. 

Både [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) och [REST-API:et för tjänsten](https://msdn.microsoft.com/library/azure/dn946891.aspx) stöder hanteringen av indexerare och datakällor. 

Alternativt kan du också konfigurera en indexerare på portalen när du använder guiden **Importera data**. Gå till [Komma igång med Azure Search på portalen](search-get-started-portal) om du vill gå en snabb självstudiekurs som använder exempeldata och DocumentDB-indexeraren för att skapa och läsa in ett index med hjälp av guiden.






<!--HONumber=jun16_HO2-->


