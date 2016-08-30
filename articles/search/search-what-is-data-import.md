<properties
    pageTitle="Dataöverföring i Azure Search | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Lär dig hur du laddar upp data till ett index i Azure Search."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Ladda upp data till Azure Search
> [AZURE.SELECTOR]
- [Översikt](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)


## Dataöverföringsmodeller i Azure Search
Du kan fylla ett Azure Search-index med data på två sätt. Det första alternativet är push-modellen där du skickar dina data manuellt till indexet med hjälp av [REST-API:et](search-import-data-rest-api.md) eller [.NET SDK](search-import-data-dotnet.md) för Azure Search. Det andra alternativet är pull-modellen där du [pekar en datakälla som stöds](search-indexer-overview.md) mot ditt Azure Search-index och låter Azure Search hämta in dina data till Search-tjänsten.

Den här guiden innehåller endast anvisningar för hur du använder push-modellen för dataöverföring (som endast stöds i [REST-API:et](search-import-data-rest-api.md) och [.NET SDK](search-import-data-dotnet.md)), men du kan även lära dig mer om pull-modellen nedan.

### Skicka data till ett index

Med den här metoden skickar du data via programmering till Azure Search så att de blir tillgängliga för sökning. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Du kan använda [REST-API:et](https://msdn.microsoft.com/library/azure/dn798930.aspx) eller [.NET SDK](search-import-data-dotnet.md) för att skicka data till ett index. Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Search, oavsett var dina data finns.

### Hämta in data till ett index

Pull-modellen crawlar en datakälla som stöds och överför automatiskt data till Azure Search-indexet. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt.

I Azure Search implementeras den här funktionen genom *indexerare*, som för närvarande är tillgängliga för [Blob Storage (förhandsversion)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Azure SQL Database och SQL Server på virtuella datorer i Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

Indexerarfunktionen exponeras på [Azure Portal](search-import-data-portal.md) samt i [REST-API:et](https://msdn.microsoft.com/library/azure/dn946891.aspx).



<!--HONumber=jun16_HO2-->


