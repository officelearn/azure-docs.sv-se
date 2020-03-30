---
title: OData-referens för fulltextsökningsfunktionen
titleSuffix: Azure Cognitive Search
description: OData fulltext sökfunktioner, search.ismatch och search.ismatchscoring, i Azure Cognitive Search frågor.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793353"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData fulltextsökfunktioner i Azure `search.ismatch` Cognitive Search - och`search.ismatchscoring`

Azure Cognitive Search stöder fulltextsökning i kontexten för `search.ismatch` `search.ismatchscoring` [OData-filteruttryck](query-odata-filter-orderby-syntax.md) via funktionerna och funktionerna. Med dessa funktioner kan du kombinera fulltextsökning med strikt boolesk filtrering på `search` ett sätt som inte är möjligt bara genom att använda parametern på den översta nivån i [sök-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> `search.ismatch` Funktionerna `search.ismatchscoring` och stöds endast i filter i [sök-API:et](https://docs.microsoft.com/rest/api/searchservice/search-documents). De stöds inte i [API:erna Föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) [eller Komplettera automatiskt.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `search.ismatch` definierar grammatiken i och `search.ismatchscoring` funktioner:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

### <a name="searchismatch"></a>sök.ismatch

Funktionen `search.ismatch` utvärderar en fulltextsökfråga som en del av ett filteruttryck. De dokument som matchar sökfrågan returneras i resultatuppsättningen. Följande överbelastningar av den här funktionen är tillgängliga:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametrarna definieras i följande tabell:

| Parameternamn | Typ | Beskrivning |
| --- | --- | --- |
| `search` | `Edm.String` | Sökfrågan (i [antingen enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) Lucene-frågesyntax). |
| `searchFields` | `Edm.String` | Kommaavgränsad lista över sökbara fält att söka i. alla sökbara fält i indexet. När du använder [fältsök i](query-lucene-syntax.md#bkmk_fields) parametern åsidosätter fältspecificerarna i Lucene-frågan alla fält som anges i den `search` här parametern. |
| `queryType` | `Edm.String` | `'simple'`eller `'full'`; som standard `'simple'`. Anger vilket frågespråk som `search` användes i parametern. |
| `searchMode` | `Edm.String` | `'any'`eller `'all'`, som `'any'`standard . Anger om något eller alla söktermer i parametern `search` måste matchas för att dokumentet ska räknas som en matchning. När du använder [lucene boolesiska operatorer](query-lucene-syntax.md#bkmk_boolean) i parametern, `search` kommer de att ha företräde framför den här parametern. |

Alla ovanstående parametrar motsvarar motsvarande [parametrar för sökbegäran i sök-API:et](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Funktionen `search.ismatch` returnerar ett `Edm.Boolean`typvärde , vilket gör att du kan komponera det med andra filterunderuttryck med hjälp av de booleska [logiska operatorerna](search-query-odata-logical-operators.md).

> [!NOTE]
> Azure Cognitive Search stöder `search.ismatch` `search.ismatchscoring` inte användning eller inuti lambda-uttryck. Det innebär att det inte går att skriva filter över samlingar av objekt som kan korrelera fulltextsökmatchningar med strikta filtermatchningar på samma objekt. Mer information om den här begränsningen och exempel finns [i Felsöka samlingsfilter i Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför den här begränsningen finns finns i [Förstå samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>sök.ismatchscoring

Funktionen, `search.ismatchscoring` liksom `search.ismatch` funktionen, `true` returnerar för dokument som matchar den fulltextsökfråga som skickas som en parameter. Skillnaden mellan dem är att relevanspoängen `search.ismatchscoring` för dokument som matchar frågan bidrar till `search.ismatch`den totala dokumentpoängen, medan dokumentpoängen inte ändras när det gäller . Följande överbelastningar av denna funktion är tillgängliga `search.ismatch`med parametrar som är identiska med:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Både `search.ismatch` funktionerna och `search.ismatchscoring` kan användas i samma filteruttryck.

## <a name="examples"></a>Exempel

Hitta dokument med ordet "vattnet". Den här filterfrågan är identisk `search=waterfront`med en [sökbegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med .

    search.ismatchscoring('waterfront')

Hitta dokument med ordet "vandrarhem" och betyg större eller lika med 4, eller dokument med ordet "motell" och betyg lika med 5. Observera att den här begäran `search.ismatchscoring` inte kunde uttryckas utan funktionen.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Hitta dokument utan ordet "lyx".

    not search.ismatch('luxury')

Hitta dokument med frasen "havsvy" eller betyg som är lika med 5. Frågan `search.ismatchscoring` körs endast mot fält `HotelName` `Rooms/Description`och .

Dokument som matchade endast den andra klausulen i disjunction `Rating` kommer att returneras också - hotell med lika med 5. För att klargöra att dessa dokument inte matchade någon av de poängsatta delarna av uttrycket, kommer de att returneras med poäng lika med noll.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Hitta dokument där termerna "hotell" och "flygplats" är inom 5 ord från varandra i beskrivningen av hotellet, och där rökning inte är tillåten i åtminstone några av rummen. Den här frågan använder det [fullständiga Lucene-frågespråket](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
