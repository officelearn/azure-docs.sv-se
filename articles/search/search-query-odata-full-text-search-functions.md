---
title: OData referens för full texts ökning – Azure Search
description: OData full text search functions, search. ismatch och search. ismatchscoring, i Azure Search frågor.
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648019"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData full texts öknings funktioner i Azure Search `search.ismatch` -och`search.ismatchscoring`

Azure Search stöder full texts ökning i samband med [OData filter-uttryck](query-odata-filter-orderby-syntax.md) via `search.ismatch` -och `search.ismatchscoring` -funktionerna. Med de här funktionerna kan du kombinera full texts ökning med strikt boolesk filtrering på sätt som inte är möjligt med hjälp av den översta `search` parametern i Sök- [API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> \- `search.ismatch` Och `search.ismatchscoring` -funktionerna stöds bara i filter i [Sök-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents). De stöds inte i API: erna [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [komplettera automatiskt](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Syntax

Följande ebnf ([Extended backable-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för `search.ismatch` och `search.ismatchscoring` -funktionerna:

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
> [OData-syntax diagram för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Se [syntax för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

### <a name="searchismatch"></a>search. ismatch

`search.ismatch` Funktionen utvärderar en full text Sök fråga som en del av ett filter uttryck. Dokumenten som matchar Sök frågan kommer att returneras i resultat uppsättningen. Följande överlagringar av den här funktionen är tillgängliga:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametrarna definieras i följande tabell:

| Parameternamn | type | Beskrivning |
| --- | --- | --- |
| `search` | `Edm.String` | Sök frågan (i antingen [enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) Lucene-frågesyntax). |
| `searchFields` | `Edm.String` | Kommaavgränsad lista över sökbara fält att söka i. Standardvärdet är alla sökbara fält i indexet. När du använder en [sökt sökning](query-lucene-syntax.md#bkmk_fields) i `search` parametern, åsidosätter fält specifikationen i en Lucene-fråga alla fält som anges i den här parametern. |
| `queryType` | `Edm.String` | `'simple'`eller `'full'` ;`'simple'`standardvärdet är. Anger vilket frågespråk som användes i `search` parametern. |
| `searchMode` | `Edm.String` | `'any'``'all'` eller`'any'`standardvärdet är. Anger om en eller flera av Sök villkoren i `search` parametern måste matchas för att räkna dokumentet som en matchning. När du använder [booleska operatorer](query-lucene-syntax.md#bkmk_boolean) för Lucene `search` i parametern, kommer de att ha företräde framför den här parametern. |

Alla parametrarna ovan motsvarar motsvarande [Sök begär ande parametrar i Sök-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Funktionen returnerar ett värde av typen `Edm.Boolean`, vilket gör att du kan skapa den med andra filter under uttryck med de booleska [logiska operatorerna](search-query-odata-logical-operators.md). `search.ismatch`

> [!NOTE]
> Azure Search stöder inte användning av `search.ismatch` eller `search.ismatchscoring` inuti lambda-uttryck. Det innebär att det inte går att skriva filter över objekt samlingar som kan korrelera matchningar med full texts ökning med strikta filter matchningar på samma objekt. Mer information om den här begränsningen och exempel finns i [Felsöka samlings filter i Azure Search](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför den här begränsningen finns i [förstå samlings filter i Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search. ismatchscoring

Funktionen, till exempel funktionen, returnerar `true` dokument som matchar full text Sök frågan som har skickats som en parameter. `search.ismatch` `search.ismatchscoring` Skillnaden mellan dem är att relevans poängen för dokument som matchar `search.ismatchscoring` frågan kommer att bidra till det övergripande resultatet av dokumentet, medan det i `search.ismatch`fallet inte ändras. Följande överlagringar av den här funktionen är tillgängliga med parametrar som är identiska med `search.ismatch`dem i:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Både- `search.ismatchscoring` och-funktionerna kan användas i samma filter uttryck. `search.ismatch`

## <a name="examples"></a>Exempel

Hitta dokument med ordet "Waterfront". Den här filter frågan är identisk med en search- `search=waterfront` [begäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med.

    search.ismatchscoring('waterfront')

Hitta dokument med ordet "Hostel" och klassificera större eller lika med 4 eller dokument med ordet "Motel" och betyget är lika med 5. OBS! den här begäran kunde inte uttryckas `search.ismatchscoring` utan funktionen.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Hitta dokument utan ordet "lyxen".

    not search.ismatch('luxury')

Hitta dokument med frasen "oceanen" eller klassificeringen lika med 5. Frågan körs bara mot fält `HotelName` och `Rooms/Description`. `search.ismatchscoring`

Dokument som matchade bara den andra satsen i disknuten returneras för--hotell med `Rating` lika med 5. För att göra det tydligt att dokumenten inte matchade någon av uttrycken i uttrycket returneras de med poängen lika med noll.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Hitta dokument där villkoren "hotell" och "flyg plats" är inom 5 ord från varandra i en beskrivning av hotellet och där rökning inte tillåts i minst några av rummen. Den här frågan använder det [fullständiga Lucene-frågespråket](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
