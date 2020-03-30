---
title: OData search.in funktionsreferens
titleSuffix: Azure Cognitive Search
description: Syntax- och referensdokumentation för att använda funktionen search.in i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113117"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData-funktionen `search.in` i Azure Cognitive Search

Ett vanligt scenario i [OData-filteruttryck](query-odata-filter-orderby-syntax.md) är att kontrollera om ett enda fält i varje dokument är lika med ett av många möjliga värden. Det är till exempel så här vissa program implementerar [säkerhetstrimning](search-security-trimming-for-azure-search.md) – genom att kontrollera ett fält som innehåller ett eller flera huvud-ID:er mot en lista över huvud-ID:er som representerar användaren som utfärdar frågan. Ett sätt att skriva en fråga [`eq`](search-query-odata-comparison-operators.md) som [`or`](search-query-odata-logical-operators.md) denna är att använda operatorerna och:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Det finns dock ett kortare sätt att `search.in` skriva detta, med hjälp av funktionen:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Förutom att vara kortare och `search.in` lättare att läsa, med hjälp av ger också [prestandafördelar](#bkmk_performance) och undviker vissa [storleksbegränsningar av filter](search-query-odata-filter.md#bkmk_limits) när det finns hundratals eller tusentals värden att inkludera i filtret. Därför rekommenderar vi starkt `search.in` att du använder i stället för en mer komplex disjunction av jämställdhetsuttryck.

> [!NOTE]
> Version 4.01 av OData-standarden har nyligen introducerat [ `in` operatorn](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), som har liknande beteende som `search.in` funktionen i Azure Cognitive Search. Azure Cognitive Search stöder dock inte den här `search.in` operatorn, så du måste använda funktionen i stället.

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `search.in` definierar grammatiken i funktionen:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Funktionen `search.in` testar om ett visst strängfält eller områdesvariabel är lika med en av en viss lista med värden. Likheten mellan variabeln och varje värde i listan bestäms på ett skiftlägeskänsligt sätt, på samma sätt som för `eq` operatören. Därför ett `search.in(myfield, 'a, b, c')` uttryck som `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`motsvarar `search.in` , förutom att kommer att ge mycket bättre prestanda.

Det finns två överbelastningar av `search.in` funktionen:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametrarna definieras i följande tabell:

| Parameternamn | Typ | Beskrivning |
| --- | --- | --- |
| `variable` | `Edm.String` | En strängfältsreferens (eller en intervallvariabel över `search.in` ett strängsamlingsfält i det fall där används inuti ett `any` eller `all` uttryck). |
| `valueList` | `Edm.String` | En sträng som innehåller en avgränsad lista `variable` med värden som matchar parametern. Om `delimiters` parametern inte anges är standardavgränsarna blanksteg och kommatecken. |
| `delimiters` | `Edm.String` | En sträng där varje tecken behandlas som en `valueList` avgränsare vid tolkning av parametern. Standardvärdet för den `' ,'` här parametern innebär att alla värden med blanksteg och/eller kommatecken mellan dem kommer att separeras. Om du behöver använda andra avgränsare än blanksteg och kommatecken på grund av `'|'` att värdena innehåller dessa tecken, kan du ange alternativa avgränsare, till exempel i den här parametern. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestanda för`search.in`

Om du `search.in`använder kan du förvänta dig svarstid under sekund när den andra parametern innehåller en lista med hundratals eller tusentals värden. Det finns ingen uttrycklig gräns för hur `search.in`många objekt du kan skicka till , även om du fortfarande begränsas av den maximala begäranden. Svarstiden kommer dock att öka i takt med att antalet värden växer.

## <a name="examples"></a>Exempel

Hitta alla hotell med namn som är lika med antingen "Sea View motell" eller "Budget hotel". Fraser innehåller blanksteg, vilket är en standard avgränsare. Du kan ange en alternativ avgränsare i enstaka citattecken som den tredje strängparametern:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Hitta alla hotell med namn som är lika med antingen "Sea View motel" eller "Budget hotel" åtskilda av |'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Hitta alla hotell med rum som har taggen "wifi" eller "badkar":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Hitta en matchning på fraser i en samling, till exempel "handdukshängare" eller "hårtork ingår" i taggar.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Hitta alla hotell utan taggen "motell" eller "stuga":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
