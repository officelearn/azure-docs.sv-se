---
title: OData fullständig text Sök funktion referens
titleSuffix: Azure Cognitive Search
description: OData full text search functions, search. ismatch och search. ismatchscoring, i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "72793353"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData full texts öknings funktioner i Azure Kognitiv sökning `search.ismatch` – och`search.ismatchscoring`

Azure Kognitiv sökning stöder full texts ökning i samband med [OData filter-uttryck](query-odata-filter-orderby-syntax.md) via- `search.ismatch` och `search.ismatchscoring` -funktionerna. Med de här funktionerna kan du kombinera full texts ökning med strikt boolesk filtrering på sätt som inte är möjligt med hjälp av den översta `search` parametern i Sök- [API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> - `search.ismatch` Och `search.ismatchscoring` -funktionerna stöds bara i filter i [Sök-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents). De stöds inte i API: erna [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [komplettera automatiskt](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended backable-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för `search.ismatch` och `search.ismatchscoring` -funktionerna:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

### <a name="searchismatch"></a>search. ismatch

`search.ismatch` Funktionen utvärderar en full text Sök fråga som en del av ett filter uttryck. Dokumenten som matchar Sök frågan kommer att returneras i resultat uppsättningen. Följande överlagringar av den här funktionen är tillgängliga:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametrarna definieras i följande tabell:

| Parameternamn | Typ | Beskrivning |
| --- | --- | --- |
| `search` | `Edm.String` | Sök frågan (i antingen [enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) Lucene-frågesyntax). |
| `searchFields` | `Edm.String` | Kommaavgränsad lista över sökbara fält att söka i. Standardvärdet är alla sökbara fält i indexet. När du använder en [sökt sökning](query-lucene-syntax.md#bkmk_fields) i `search` parametern, åsidosätter fält specifikationen i en Lucene-fråga alla fält som anges i den här parametern. |
| `queryType` | `Edm.String` | `'simple'`eller `'full'`; Standardvärdet är `'simple'`. Anger vilket frågespråk som användes i `search` parametern. |
| `searchMode` | `Edm.String` | `'any'`eller `'all'`standardvärdet `'any'`är. Anger om en eller flera av Sök villkoren i `search` parametern måste matchas för att räkna dokumentet som en matchning. När du använder [booleska operatorer](query-lucene-syntax.md#bkmk_boolean) för Lucene `search` i parametern, kommer de att ha företräde framför den här parametern. |

Alla parametrarna ovan motsvarar motsvarande [Sök begär ande parametrar i Sök-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents).

`search.ismatch` Funktionen returnerar ett värde av typen `Edm.Boolean`, vilket gör att du kan skapa den med andra filter under uttryck med de booleska [logiska operatorerna](search-query-odata-logical-operators.md).

> [!NOTE]
> Azure Kognitiv sökning stöder inte användning av `search.ismatch` eller `search.ismatchscoring` inuti lambda-uttryck. Det innebär att det inte går att skriva filter över objekt samlingar som kan korrelera matchningar med full texts ökning med strikta filter matchningar på samma objekt. Mer information om den här begränsningen och exempel finns i [Felsöka samlings filter i Azure kognitiv sökning](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför den här begränsningen finns i [förstå samlings filter i Azure kognitiv sökning](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search. ismatchscoring

`search.ismatchscoring` Funktionen, till exempel `search.ismatch` funktionen, returnerar `true` dokument som matchar full text Sök frågan som har skickats som en parameter. Skillnaden mellan dem är att relevans poängen för dokument som matchar `search.ismatchscoring` frågan kommer att bidra till det övergripande resultatet av dokumentet, medan det i fallet `search.ismatch`inte ändras. Följande överlagringar av den här funktionen är tillgängliga med parametrar som är identiska med `search.ismatch`dem i:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Både- `search.ismatch` och `search.ismatchscoring` -funktionerna kan användas i samma filter uttryck.

## <a name="examples"></a>Exempel

Hitta dokument med ordet "Waterfront". Den här filter frågan är identisk med en [search](https://docs.microsoft.com/rest/api/searchservice/search-documents) - `search=waterfront`begäran med.

    search.ismatchscoring('waterfront')

Hitta dokument med ordet "Hostel" och klassificera större eller lika med 4 eller dokument med ordet "Motel" och betyget är lika med 5. OBS! den här begäran kunde inte uttryckas `search.ismatchscoring` utan funktionen.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Hitta dokument utan ordet "lyxen".

    not search.ismatch('luxury')

Hitta dokument med frasen "oceanen" eller klassificeringen lika med 5. `search.ismatchscoring` Frågan körs bara mot fält `HotelName` och `Rooms/Description`.

Dokument som matchade bara den andra satsen i disknuten returneras för--hotell med `Rating` lika med 5. För att göra det tydligt att dokumenten inte matchade någon av uttrycken i uttrycket returneras de med poängen lika med noll.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Hitta dokument där villkoren "hotell" och "flyg plats" är inom 5 ord från varandra i en beskrivning av hotellet och där rökning inte tillåts i minst några av rummen. Den här frågan använder det [fullständiga Lucene-frågespråket](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
