---
title: OData-referens för logisk operator – Azure Search
description: OData logiska operatorer, och, eller, och inte i Azure Search-frågor.
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079774"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>OData logiska operatorer i Azure Search - `and`, `or`, `not`

[OData-filteruttryck](query-odata-filter-orderby-syntax.md) i Azure Search är booleska uttryck som utvärderas till `true` eller `false`. Du kan skriva ett komplexa filter genom att skriva en serie [enklare filter](search-query-odata-comparison-operators.md) och skapa dem med hjälp av de logiska operatorerna från [boolesk algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: En binär operator som utvärderas till `true` om båda dess vänster- och underordnade uttryck som utvärderas till `true`.
- `or`: En binär operator som utvärderas till `true` om antingen en av dess vänster eller höger underordnade uttryck utvärderas till `true`.
- `not`: En unär operator som utvärderas till `true` om dess underordnade uttryck som utvärderas till `false`, och vice versa.

Dessa, tillsammans med den [samling operatörer `any` och `all` ](search-query-odata-collection-operators.md), att du kan skapa filter som kan uttrycka mycket avancerade sökvillkor.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur för ett OData-uttryck som använder logiska operatorer.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Det finns två typer av logiska uttryck: binary (`and`/`or`), där det finns två underordnade uttryck och unära (`not`), där det finns bara ett. Underordnade uttryck kan vara booleska uttryck av något slag:

- Variabler för fält eller ett intervall av typen `Edm.Boolean`
- Funktioner som returnerar värden av typen `Edm.Boolean`, till exempel `geo.intersects` eller `search.ismatch`
- [Jämförelseuttryck](search-query-odata-comparison-operators.md), till exempel `rating gt 4`
- [Samling uttryck](search-query-odata-collection-operators.md), till exempel `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Booleska konstanter `true` eller `false`.
- Andra logiska uttryck som skapats med hjälp av `and`, `or`, och `not`.

> [!IMPORTANT]
> Det finns vissa situationer där inte alla typer av underordnade uttryck kan användas med `and` / `or`, särskilt inuti lambda-uttryck. Se [OData samling operatörer i Azure Search](search-query-odata-collection-operators.md#limitations) mer information.

### <a name="logical-operators-and-null"></a>Logiska operatorer och `null`

De flesta booleska uttryck, till exempel funktioner och jämförelser kan inte producera `null` värden och de logiska operatorerna kan inte tillämpas på den `null` literal direkt (till exempel `x and null` tillåts inte). Fält för booleska värden kan dock vara `null`, så du behöver känna till hur `and`, `or`, och `not` operatörer beter sig när det finns null. Detta är sammanfattas i tabellen nedan, där `b` är ett fält av typen `Edm.Boolean`:

| uttryck | Resultatet när `b` är `null` |
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

När typen Boolean `b` visas ensamt i ett filteruttryck fungerar som om det har utvecklats `b eq true`, så om `b` är `null`, uttrycket utvärderas till `false`. På samma sätt `not b` fungerar som `not (b eq true)`, så att det utvärderar till `true`. På så sätt kan `null` fält fungerar på samma sätt som `false`. Den här riktlinjen följer hur de beter sig när de kombineras med andra uttryck med hjälp av `and` och `or`, vilket visas i tabellen ovan. Trots detta som en direkt jämförelse till `false` (`b eq false`) kommer fortfarande utvärderas till `false`. Med andra ord `null` är inte lika `false`, trots att den fungerar som den i booleskt uttryck.

## <a name="examples"></a>Exempel

Matcha dokument där den `rating` fältet är mellan 3 och 5, inklusive:

    rating ge 3 and rating le 5

Matcha dokument där alla element i den `ratings` fältet är mindre än 3 eller större än 5:

    ratings/all(r: r lt 3 or r gt 5)

Matcha dokument där den `location` fält inom den angivna polygonen, och dokumentet inte innehåller termen ”offentliga”.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Matcha dokument efter hotell i Vancouver, Kanada där det finns ett deluxe rum med bas Betygsätt mindre än 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
