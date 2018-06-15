---
title: Köra frågor mot ett index (Portal – Azure Search) | Microsoft Docs
description: Skicka en sökfråga i Sökutforskaren på Azure Portal.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: heidist
ms.openlocfilehash: a3592bd0c304dfb78374eeba432c0d28203980c9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790516"
---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Köra frågor mot ett Azure Search-index med Sökutforskaren i Azure Portal
> [!div class="op_single_selector"]
> * [Översikt](search-query-overview.md)
> * [Portalen](search-explorer.md)
> * [NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Den här artikeln beskriver hur du kör frågor mot ett Azure Search-index med hjälp av **Sökutforskaren** i Azure Portal. Med Sökutforskaren kan du skicka enkla frågor eller fullständiga Lucene-frågesträngar till befintliga index i din tjänst.

## <a name="open-the-service-dashboard"></a>Öppna instrumentpanelen
1. Klicka på **Alla resurser** i snabbåtkomstfältet på vänster sida av [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Välj din Azure Search-tjänst.

## <a name="select-an-index"></a>Välj ett index

Välj det index som du vill söka i från panelen **Index**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Öppna Sökutforskaren

Klicka på panelen Sökutforskaren för att öppna sökfältet och resultatfönstret.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Starta sökningen

När du använder Sökutforskaren kan du formulera frågan genom att ange valfria [frågeparametrar](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

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