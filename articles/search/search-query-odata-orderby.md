---
title: OData order-för-referens
titleSuffix: Azure Cognitive Search
description: Syntax- och språkreferensdokumentation för att använda order-by i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113159"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby syntax i Azure Cognitive Search

 Du kan använda [parametern OData **$orderby** ](query-odata-filter-orderby-syntax.md) för att tillämpa en anpassad sorteringsordning för sökresultat i Azure Cognitive Search. I den här artikeln beskrivs syntaxen för **$orderby** i detalj. Mer allmän information om hur du använder **$orderby** när du presenterar sökresultat finns [i Så här arbetar du med sökresultat i Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametern **$orderby** accepterar en kommaavgränsad lista med upp till 32 **order-by-satser**. Syntaxen för en order-by-sats beskrivs av följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Varje sats har sorteringsvillkor, eventuellt`asc` följt av `desc` en sorteringsriktning (för stigande eller fallande). Om du inte anger någon riktning är standardvärdet stigande. Sorteringsvillkoren kan antingen vara `sortable` sökvägen till ett [`geo.distance`](search-query-odata-geo-spatial-functions.md) fält [`search.score`](search-query-odata-search-score-function.md) eller ett anrop till antingen funktionerna eller funktionerna.

Om flera dokument har samma `search.score` sorteringsvillkor och funktionen inte används (till `Rating` exempel om du sorterar efter ett numeriskt fält och tre dokument alla har en klassificering på 4), bryts banden efter dokumentpoäng i fallande ordning. När dokumentpoängen är desamma (till exempel när ingen fulltextsökningsfråga anges i begäran) är den relativa ordningen för de bundna dokumenten obestämd.

Du kan ange flera sorteringsvillkor. Uttrycksordningen bestämmer den slutliga sorteringsordningen. Om du till exempel vill sortera fallande efter poäng, följt av Betyg, skulle syntaxen vara `$orderby=search.score() desc,Rating desc`.

Syntaxen `geo.distance` för i **$orderby** är densamma som i **$filter**. När `geo.distance` du använder i **$orderby**måste det fält som `Edm.GeographyPoint` det gäller vara `sortable`av typen och det måste också vara .

Syntaxen `search.score` för i **$orderby** `search.score()`är . Funktionen `search.score` tar inga parametrar.

## <a name="examples"></a>Exempel

Sortera hotell som stiger efter baspris:

    $orderby=BaseRate asc

Sortera hotell fallande efter betyg, sedan stigande efter basränta (kom ihåg att stigande är standard):

    $orderby=Rating desc,BaseRate

Sortera hotell fallande efter betyg, sedan stigande efter avstånd från de angivna koordinaterna:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sortera hotell i fallande ordning efter search.score och betyg, och sedan i stigande ordning efter avstånd från de angivna koordinaterna. Mellan två hotell med identiska relevanspoäng och betyg visas den närmaste först:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [Så här arbetar du med sökresultat i Azure Cognitive Search](search-pagination-page-layout.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
