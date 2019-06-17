---
title: Funktionsreferens för OData - search.score Azure Search
description: OData search.score funktion i Azure Search-frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079696"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` funktion i Azure Search

När du skickar en fråga till Azure Search utan den [ **$orderby** parametern](search-query-odata-orderby.md), som kommer tillbaka resultaten sorteras i fallande ordning efter relevansgradering. Även när du använder **$orderby**, relevansgradering används för att bryta ties som standard. Men är ibland det praktiskt att använda relevansgradering som ett villkor för inledande sortering och andra kriterier som prioritet. Den `search.score` funktionen kan du göra detta.

## <a name="syntax"></a>Syntax

Syntaxen för `search.score` i **$orderby** är `search.score()`. Funktionen `search.score` inte tar några parametrar. Den kan användas med den `asc` eller `desc` sorteringsordningen specificerare, precis som andra sats i den **$orderby** parametern. Det kan visas var som helst i listan med villkor för sortering.

## <a name="example"></a>Exempel

Sortera hotell i fallande ordning av `search.score` och `rating`, och sedan i stigande ordning efter avståndet från de angivna koordinaterna så att mellan två hotels med identiska betyg den närmaste som visas först:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
