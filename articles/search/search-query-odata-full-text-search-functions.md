---
title: Funktionsreferens för OData - fulltextsökning Azure Search
description: OData fulltextsökning fungerar, search.ismatch och search.ismatchscoring i Azure Search-frågor.
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079813"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData fulltextsökning funktioner i Azure Search - `search.ismatch` och `search.ismatchscoring`

Azure Search har stöd för fulltextsökning i samband med [OData-filteruttryck](query-odata-filter-orderby-syntax.md) via den `search.ismatch` och `search.ismatchscoring` funktioner. Dessa funktioner kan du kombinera fulltextsökning med strikta booleskt filtrering på ett sätt som inte är möjligt genom att använda den översta `search` -parametern för den [API: et Search](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Den `search.ismatch` och `search.ismatchscoring` funktionerna stöds endast i filter i den [API: et Search](https://docs.microsoft.com/rest/api/searchservice/search-documents). De stöds inte i den [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API: er.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur av den `search.ismatch` och `search.ismatchscoring` funktioner:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

### <a name="searchismatch"></a>search.ismatch

Den `search.ismatch` evaluerar en fulltextsökning-fråga som en del av ett filteruttryck. Dokument som matchar sökfrågan returneras i resultatet. Följande överlagringar av den här funktionen är tillgängliga:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametrarna har definierats i tabellen nedan:

| Parameternamn | Typ | Beskrivning |
| --- | --- | --- |
| `search` | `Edm.String` | Sökfrågan (antingen [enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) Lucene-frågesyntax). |
| `searchFields` | `Edm.String` | Kommaavgränsad lista över sökbara fält att söka i. Standardvärdet är alla sökbara fält i indexet. När du använder [fielded search](query-lucene-syntax.md#bkmk_fields) i den `search` parametern fältet specificerare i Lucene frågan åsidosätter alla fält som anges i den här parametern. |
| `queryType` | `Edm.String` | `'simple'` eller `'full'`; som standard `'simple'`. Anger vilka frågespråk som användes i den `search` parametern. |
| `searchMode` | `Edm.String` | `'any'` eller `'all'`, som standard `'any'`. Anger om en eller alla sökningen villkor i den `search` måste matcha parametern för att kunna räkna dokumentet som en matchning. När du använder den [Lucene booleska operatorer](query-lucene-syntax.md#bkmk_boolean) i den `search` parameter, de åsidosätter den här parametern. |

Alla ovanstående parametrar är likvärdiga med motsvarande [söka efter parametrarna i API: et Search](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Den `search.ismatch` funktionen returnerar ett värde av typen `Edm.Boolean`, vilket gör att du till att skapa den med andra underordnade filteruttryck som använder Boolean [logiska operatorer](search-query-odata-logical-operators.md).

> [!NOTE]
> Azure Search stöder inte användning av `search.ismatch` eller `search.ismatchscoring` i lambda-uttryck. Det innebär att det inte går att skriva filter över samlingar av objekt som kan jämföra fulltextsökning matchningar med strikta filtret matchar på samma objekt. Mer information om den här begränsningen samt exempel finns i [felsökning samling filter i Azure Search](search-query-troubleshoot-collection-filters.md). Mer information om varför den här begränsningen finns, finns i [förstå samling filter i Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search.ismatchscoring

Den `search.ismatchscoring` fungerar som den `search.ismatch` funktionen returnerar `true` för dokument som matchar frågan fulltextsökning skickas som en parameter. Skillnaden mellan dem är att upplevelsen poäng dokument som matchar den `search.ismatchscoring` frågan kommer att bidra till den övergripande dokument poängen, medan i fallet med `search.ismatch`, dokumentet poängen ändras inte. Följande överlagringar av den här funktionen är tillgängliga med parametrar som är identiska med de av `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Både den `search.ismatch` och `search.ismatchscoring` funktioner kan användas i samma filteruttrycket.

## <a name="examples"></a>Exempel

Hitta dokument med ordet ”Hamnområde”. Den här filterfrågan är identisk med en [sökbegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med `search=waterfront`.

    search.ismatchscoring('waterfront')

Hitta dokument med ordet ”hostel” och omdöme större eller lika med 4 eller dokument med ordet ”motel” och omdöme lika med 5. Observera att denna begäran inte kan uttryckas utan den `search.ismatchscoring` funktion.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Hitta dokument utan ordet ”Lyxig”.

    not search.ismatch('luxury')

Hitta dokument med frasen ”ocean view” eller klassificering som motsvarar 5. Den `search.ismatchscoring` frågan körs bara mot fält `HotelName` och `Rooms/Description`.

Dokument som matchade endast andra delen av disjunktion som ska returneras för--hotels med `Rating` lika med 5. Att göra det Rensa att dokumenten inte matchar någon av de poängsatta delarna av uttrycket, kommer att returneras med poäng som är lika med noll.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Hitta dokument där villkoren ”hotell” och ”flygplats” är inom 5 ord från varandra i beskrivningen av hotellet och där hälsan tillåts inte i minst några av lokalerna. Den här frågan använder den [fullständig Lucene frågespråk](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
