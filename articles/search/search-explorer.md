---
title: "Skicka frågor mot ditt Azure Search-index med hjälp av Azure-portalen | Microsoft Docs"
description: "Skicka en sökfråga i Sökutforskaren på Azure Portal."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Skicka frågor mot ditt Azure Search-index med hjälp av Azure Portal
> [!div class="op_single_selector"]
> * [Översikt](search-query-overview.md)
> * [Portalen](search-explorer.md)
> * [NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Den här guiden beskriver hur du skickar frågor mot ditt Azure Search-index på Azure Portal.

Innan du påbörjar den här genomgången bör du redan ha [skapat ett Azure Search-index](search-what-is-an-index.md) och [fyllt det med data](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Gå till ditt Azure Search-blad
1. Klicka på ”Alla resurser” på menyn på vänster sida av [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Välj din Azure Search-tjänst.

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. Välj det index som du vill söka i
1. Välj det index som du vill söka i från indexpanelen.

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. Klicka på panelen ”Sökutforskaren”
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. Starta sökningen
1. Börja söka i Azure Search-indexet genom att skriva i fältet ” *Frågesträng* ” och tryck på ”**Sök**”.
   
   * När du använder Sökutforskaren kan du ange valfria [frågeparametrar](https://msdn.microsoft.com/library/dn798927.aspx)
2. I avsnittet ” *Resultat* ” visas frågans resultat i samma JSON-rådataformat som du får i en HTTP-svarstext när du skickar sökförfrågningar mot REST-API:et för Azure Search.
3. Frågesträngen parsas automatiskt till rätt begärande-URL för att sedan skicka en HTTP-begäran mot REST-API:et för Azure Search.

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


