---
title: Återskapa en Azure Search-index eller uppdatera sökbart innehåll – Azure Search
description: Lägga till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en fullständig återskapning eller partiella inkrementella indexering för att uppdatera ett Azure Search-index.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9c9af69e45af6a70c5327393a1c10385ba2c2aed
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316904"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Återskapar ett Azure Search-index

Återskapa en index ändras dess struktur, ändra fysiska uttrycket för index i Azure Search-tjänsten. Omvänt kan är uppdatera ett index en endast innehåll uppdatering att hämta de senaste ändringarna från en bidragande extern datakälla. Den här artikeln innehåller riktning om hur du uppdaterar index strukturellt och sekretessförfrågningar.

Läs-och skrivbehörighet på servicenivån måste anges för index uppdateringar. Programmässigt, kan du anropa REST- eller .NET API: er för en fullständig återskapning eller inkrementell indexering av innehåll, med parametrar som du anger alternativ för klientuppdatering. 

Uppdateringar för ett index är vanligtvis på begäran. Men för index som fylls i med hjälp av specifik [indexerare](search-indexer-overview.md), du kan använda en inbyggd scheduler. Scheduler har stöd för uppdatering av dokumentet så ofta som var femtonde minut, upp till det intervall och mönster som du behöver. En snabbare uppdateringsintervall kräver push-överföra index uppdateringar manuellt, kanske via en dubbel skrivning till transaktioner som uppdaterar både den externa datakällan och Azure Search-index samtidigt.

## <a name="full-rebuilds"></a>Fullständig behöver

Återskapa krävs för många typer av uppdateringar. Återskapa refererar till borttagning av ett index, både data och metadata, följt av sedan hämtar nytt index från externa datakällor. Programmässigt, [ta bort](https://docs.microsoft.com/rest/api/searchservice/delete-index), [skapa](https://docs.microsoft.com/rest/api/searchservice/create-index), och [ladda](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) i index för att återskapa den. 

Återskapa efter, Kom ihåg att om du har testat frågemönster och poängprofiler, du kan förvänta dig variation i frågeresultaten om det underliggande innehållet har ändrats.

## <a name="when-to-rebuild"></a>När du ska återskapa

Plan för frekventa, fullständig återskapar under aktivt med utveckling, när indexet scheman är i ett tillstånd för som.

| Ändring av | Återskapa status|
|--------------|---------------|
| Ändra ett fältnamn datatypen, eller dess [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index) | Om du ändrar en fältdefinition vanligtvis tillkommer en återskapning särskilda avgifter, med undantag för dessa [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index): Hämtningsbar, SearchAnalyzer, SynonymMaps. Du kan lägga till attributen hämtningsbara, SearchAnalyzer och SynonymMaps till ett befintligt fält utan att behöva återskapa dess index.|
| Lägg till ett fält | Inga strikta krav på återskapning. Befintliga indexerade dokumenten ges ett null-värde för det nya fältet. I en framtida reindex Ersätt värden källdata null-värden har lagts till av Azure Search. |
| Ta bort ett fält | Du kan inte ta bort ett fält direkt från ett Azure Search-index. I stället bör du ha programmet Ignorera fältet ”borttagen” för att undvika att använda den. Fysiskt, förblir fältdefinition och innehåll i indexet tills nästa gång du återskapa index med hjälp av ett schema som utesluter det aktuella området.|

> [!Note]
> Återskapning av en krävs också om du växlar nivåer. Om det finns ingen uppgradering på plats vid en viss tidpunkt som du bestämmer dig för mer kapacitet. En ny tjänst måste skapas på den nya kapacitet punkten och index skapas från början på den nya tjänsten. 

## <a name="partial-or-incremental-indexing"></a>Partiell eller inkrementell indexering

När ett index är i produktion, Vi fokuserar på inkrementella indexering, vanligtvis med inga går avbrott. Partiell eller inkrementell indexering är en endast innehåll arbetsbelastning som synkroniserar innehållet i ett search-index för att återspegla tillståndet för innehåll i en bidragande datakälla. Ett dokument läggs till eller tas bort i källan läggs till eller tas bort i indexet. I koden anropar den [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) åtgärden eller motsvarande .NET.

> [!Note]
> När du använder indexerare som crawlar externa datakällor kan utnyttjas ändringsspårningen autentiseringsmekanismer i källsystem för inkrementell indexering. För [Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), ett `lastModified` fältet används. På [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` har samma funktion. På samma sätt, både [Azure SQL Database-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) och [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md#indexing-changed-documents) ha fält för flagga uppdateringar av rader. Läs mer om indexerare [översikt över indexerare](search-indexer-overview.md).


## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Index stora datauppsättningar i stor skala](search-howto-large-index.md)
+ [Indexeringen i portalen](search-import-data-portal.md)
+ [Azure SQL Database-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Search](search-security-overview.md)