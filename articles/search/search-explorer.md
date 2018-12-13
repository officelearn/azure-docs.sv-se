---
title: Sökutforskaren i Azure-portalen för att fråga index - Azure Search
description: Använd Azure portal-verktyg som Sökutforskaren för frågan index i Azure Search. Ange sökvillkor eller fullständiga söksträngar med avancerade syntax.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11f102fcb2a24f9062313f9a3234c29e70a3dfe0
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315680"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Hur du använder Sökutforskaren för frågan index i Azure Search 

Den här artikeln visar hur du frågar efter en befintlig Azure Search index med hjälp av **Sökutforskaren** i Azure-portalen. Du kan använda Sökutforskaren för att skicka enkla eller fullständiga Lucene frågesträngar till befintliga index i din tjänst.

## <a name="open-the-service-dashboard"></a>Öppna instrumentpanelen
1. Klicka på **Alla resurser** i snabbåtkomstfältet på vänster sida av [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Välj din Azure Search-tjänst.

## <a name="select-an-index"></a>Välj ett index

Välj det index som du vill söka i från panelen **Index**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Öppna Sökutforskaren

Klicka på panelen Sök explorer för att öppna sökfältet och resultatfönstret.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Starta sökningen

När du använder Sökutforskaren kan du ange [frågeparametrar](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) formulera frågan.

1. Skriv en fråga i **Frågesträng** och tryck sedan på **Sök**. 

   Frågesträngen parsas automatiskt till rätt fråge-URL för att sedan skicka en HTTP-begäran mot REST-API:et för Azure Search.   
   
   Du kan skapa begäran med valfri enkel frågesyntax eller en fullständig Lucene-frågesyntax. Tecknet `*` motsvarar en tom eller ospecificerad sökning som returnerar alla dokument (inte i någon särskild ordning).

2. I **Resultat** visas frågeresultat i oformaterad JSON. Dessa data är identiska med nyttolasten som returneras i en HTTP-svarstext när begäranden görs via programmering.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Nästa steg

Följande resurser innehåller ytterligare information om och exempel på frågesyntax.

 + [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exempel på Lucene-frågesyntax](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Syntax för OData-filteruttryck](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 