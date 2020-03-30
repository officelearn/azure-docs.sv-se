---
title: OData logisk operatörsreferens
titleSuffix: Azure Cognitive Search
description: Syntax- och referensdokumentation för att använda logiska Operatorer i OData och, eller, och inte, i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113182"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData logiska operatorer i `and` `or`Azure Cognitive Search - ,`not`

[OData-filteruttryck](query-odata-filter-orderby-syntax.md) i Azure Cognitive Search är `true` booleska uttryck som utvärderas till eller `false`. Du kan skriva ett komplext filter genom att skriva en serie [enklare filter](search-query-odata-comparison-operators.md) och komponera dem med hjälp av logiska operatorer från [boolesk algebra:](https://en.wikipedia.org/wiki/Boolean_algebra)

- `and`: En binär operator `true` som utvärderar till om både dess `true`vänster och höger underuttryck utvärderar till .
- `or`: En binär operator `true` som utvärderas till om antingen ett av `true`dess vänster- eller högerunderuttryck utvärderar till .
- `not`: En unary operator `true` som utvärderar till om `false`dess underuttryck utvärderas till , och vice versa.

Dessa, tillsammans med [ `any` samlingsoperatorerna och `all` ](search-query-odata-collection-operators.md), gör att du kan konstruera filter som kan uttrycka mycket komplexa sökvillkor.

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för ett OData-uttryck som använder de logiska operatorerna.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Det finns två former av logiska uttryck: binär (`and`/`or`), där det`not`finns två underuttryck och unary ( ), där det bara finns en. Underuttrycken kan vara booleska uttryck av alla slag:

- Fält eller områdesvariabler av typen`Edm.Boolean`
- Funktioner som returnerar `Edm.Boolean`värden `geo.intersects` av typen, till exempel eller`search.ismatch`
- [Jämförelseuttryck,](search-query-odata-comparison-operators.md)t.ex.`rating gt 4`
- [Samlingsuttryck,](search-query-odata-collection-operators.md)t.ex.`Rooms/any(room: room/Type eq 'Deluxe Room')`
- De booleska `true` bokstaverna eller `false`.
- Andra logiska uttryck `and`som `or`skapats med , och `not`.

> [!IMPORTANT]
> Det finns vissa situationer där inte alla typer `and` / `or`av sub-uttryck kan användas med , särskilt inuti lambda uttryck. Mer information finns [i OData-samlingsoperatörer i Azure Cognitive Search.](search-query-odata-collection-operators.md#limitations)

### <a name="logical-operators-and-null"></a>Logiska operatorer och`null`

De flesta booleska uttryck, till `null` exempel funktioner och jämförelser, kan inte `null` ge värden, och `x and null` de logiska operatorerna kan inte användas på den litterala direkt (till exempel är inte tillåtet). Booleska fält kan `null`dock vara , så du `and` `or`måste `not` vara medveten om hur operatorerna , och fungerar i närvaro av null. Detta sammanfattas i följande tabell, där `b` är `Edm.Boolean`ett fält av typen:

| Uttryck | Resultat `b` när det är`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

När ett booleskt fält `b` visas av sig själv i ett `b eq true`filteruttryck `b` fungerar det som om det hade skrivits , så om är `null`, uttrycket utvärderas till `false`. På samma `not b` sätt `not (b eq true)`beter sig som `true`, så det utvärderas till . På så `null` sätt fungerar fälten på samma sätt som `false`. Detta är förenligt med hur de beter sig i kombination med andra uttryck med hjälp av `and` och `or`, som visas i tabellen ovan. Trots detta kommer en `false` `b eq false`direkt jämförelse med `false`( ) fortfarande att utvärdera till . Med andra `null` ord, är `false`inte lika med , även om det beter sig som det i booleska uttryck.

## <a name="examples"></a>Exempel

Matcha dokument `rating` där fältet är mellan 3 och 5, inklusive:

    rating ge 3 and rating le 5

Matcha dokument där alla `ratings` element i fältet är mindre än 3 eller fler än 5:

    ratings/all(r: r lt 3 or r gt 5)

Matcha dokument `location` där fältet finns inom den angivna polygonen och dokumentet innehåller inte termen "offentlig".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Matchdokument för hotell i Vancouver, Kanada där det finns ett deluxerum med ett baspris mindre än 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
