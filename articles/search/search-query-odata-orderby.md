---
title: OData ordning av referens – Azure Search
description: Språkreferens för OData för ordning av syntax i Azure Search-frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079761"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>OData $orderby syntax i Azure Search

 Du kan använda den [OData **$orderby** parametern](query-odata-filter-orderby-syntax.md) att tillämpa en anpassad sorteringsordning för sökresultat i Azure Search. Den här artikeln beskrivs syntaxen för **$orderby** i detalj. Mer allmän information om hur du använder **$orderby** när du presenterar sökresultat, se [hur du arbetar med sökning resulterar i Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Den **$orderby** parametern accepterar en kommaavgränsad lista med upp till 32 **order by-satser**. Syntaxen för en order by-sats beskrivs i följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Varje villkor har sorteringsvillkor, eventuellt följt av en sorteringsriktning (`asc` för stigande eller `desc` för fallande). Om du inte anger en riktning, är standardinställningen stigande. Villkor för sortering kan antingen vara sökvägen till en `sortable` fält eller ett anrop till antingen den [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) eller [ `search.score` ](search-query-odata-search-score-function.md) funktioner.

Om flera dokument har samma villkor för sortering och `search.score` funktionen inte används (till exempel om du sorterar efter ett numeriskt `Rating` fält och tre dokument alla har en klassificering på 4), kommer att brytas ties dokumentet poäng i fallande ordning. När du poäng för dokumentet är desamma (till exempel när det finns ingen fulltextsökning-fråga som anges i begäran), sedan är relativa ordning bundet dokument obestämd.

Du kan ange flera villkor för sortering. Ordningen på uttryck anger sista sorteringsordning. Till exempel om du vill sortera fallande efter poäng, följt av klassificering, syntax skulle vara `$orderby=search.score() desc,Rating desc`.

Syntaxen för `geo.distance` i **$orderby** är densamma som den tillhör **$filter**. När du använder `geo.distance` i **$orderby**, fältet som gäller måste vara av typen `Edm.GeographyPoint` och det måste också vara `sortable`.

Syntaxen för `search.score` i **$orderby** är `search.score()`. Funktionen `search.score` tar inga parametrar.

## <a name="examples"></a>Exempel

Sortera stigande efter baspriset hotels:

    $orderby=BaseRate asc

Sortera hotels fallande efter klassificering och sedan stigande efter baspriset (Kom ihåg att stigande är standard):

    $orderby=Rating desc,BaseRate

Sortera hotels fallande efter klassificering och sedan stigande efter avståndet från de angivna koordinaterna:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sortera hotell i fallande ordning genom att search.score och klassificering och sedan i stigande ordning efter avståndet från de angivna koordinaterna. Mellan två hotels med identiska relevans poäng och betyg visas den närmaste som först:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [Hur du arbetar med sökning resulterar i Azure Search](search-pagination-page-layout.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
