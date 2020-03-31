---
title: OData search.score-funktionsreferens
titleSuffix: Azure Cognitive Search
description: Syntax- och referensdokumentation för att använda funktionen search.score i Azure Cognitive Search-frågor.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113135"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData-funktionen `search.score` i Azure Cognitive Search

När du skickar en fråga till Azure Cognitive Search utan [ **parametern $orderby** ](search-query-odata-orderby.md)sorteras resultaten som kommer tillbaka i fallande ordning efter relevansresultat. Även när du använder **$orderby**används relevanspoängen för att bryta banden som standard. Men ibland är det användbart att använda relevanspoängen som ett första sorteringsvillkor, och några andra kriterier som tie-breaker. Funktionen `search.score` låter dig göra detta.

## <a name="syntax"></a>Syntax

Syntaxen `search.score` för i **$orderby** `search.score()`är . Funktionen `search.score` tar inga parametrar. Den kan användas `asc` med `desc` sortorderspecificeraren, precis som alla andra satser i **parametern $orderby.** Det kan visas var som helst i listan med sorteringsvillkor.

## <a name="example"></a>Exempel

Sortera hotell i fallande ordning `search.score` efter och `rating`, och sedan i stigande ordning efter avstånd från de angivna koordinaterna så att mellan två hotell med identiska betyg, den närmaste listas först:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
