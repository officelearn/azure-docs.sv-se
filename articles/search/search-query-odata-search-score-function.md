---
title: OData-sökning. score-funktions referens – Azure Search
description: OData search. score-funktionen i Azure Search frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647521"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` -funktion i Azure Search

När du skickar en fråga till Azure Search utan parametern [ **$OrderBy** ](search-query-odata-orderby.md), sorteras resultaten som kommer tillbaka i fallande ordning efter relevans-poäng. Även om du använder **$OrderBy**, kommer relevans poängen att användas för att avbryta band som standard. Men ibland är det praktiskt att använda relevans poängen som ett inledande sorterings villkor och andra villkor som slip-Break. `search.score` Funktionen gör det möjligt att göra detta.

## <a name="syntax"></a>Syntax

Syntaxen för `search.score` i **$OrderBy** är `search.score()`. Funktionen `search.score` tar inga parametrar. Den kan användas med `asc` specifikationen eller `desc` sorterings ordningen, precis som andra satser i **$OrderBy** parameter. Den kan visas var som helst i listan med sorterings villkor.

## <a name="example"></a>Exempel

Sortera hotell i fallande ordning efter `search.score` och `rating`och sedan i stigande ordning efter avstånd från givna koordinater, så att de två hotellen med identiska klassificeringar visas först:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
