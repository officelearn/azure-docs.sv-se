---
title: Sökutforskaren för att fråga index i Azure Search | Microsoft Docs
description: Lär dig hur du använder Sökutforskaren för att fråga index i Azure Search.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 520d9e7b1899c54d922ff6fb77e0901f9609b029
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004141"
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