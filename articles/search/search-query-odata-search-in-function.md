---
title: Funktionsreferens för OData - search.in Azure Search
description: OData search.in funktion i Azure Search-frågor.
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449975"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` funktion i Azure Search

Ett vanligt scenario i [OData-filteruttryck](query-odata-filter-orderby-syntax.md) är att kontrollera om ett enskilt fält i varje dokument är lika med ett av många möjliga värden. Detta är till exempel hur vissa program implementera [säkerhetsoptimering](search-security-trimming-for-azure-search.md) --genom att markera ett fält som innehåller en eller flera principal-ID: N mot en lista över huvudkonto-ID: N som representerar den användare som utfärdade frågan. Ett sätt att skriva en fråga så här är att använda den [ `eq` ](search-query-odata-comparison-operators.md) och [ `or` ](search-query-odata-logical-operators.md) operatorer:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Men det finns ett kortare sätt att skriva detta med hjälp av den `search.in` funktionen:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Förutom att kortare och lättare att läsa med `search.in` innehåller också [prestandafördelarna](#bkmk_performance) och undvika vissa [storlek begränsningar av filter](search-query-odata-filter.md#bkmk_limits) när det finns hundratals eller tusentals värden ska ingå i filtret. Därför rekommenderar vi använder `search.in` i stället för en mer komplex disjunktion av likhet uttryck.

> [!NOTE]
> Version 4.01 av OData-standarden har nyligen har introducerats i [ `in` operatorn](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), som har samma problem som den `search.in` funktionen i Azure Search. Men Azure Search stöder inte den här operatorn, så du måste använda den `search.in` i stället.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur av den `search.in` funktionen:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Den `search.in` funktion testar om ett visst strängfält eller intervall variabeln är lika med ett av en specifik lista med värden. Likheten mellan variabeln och varje värde i listan bestäms på ett skiftlägeskänsligt sätt, på samma sätt som för den `eq` operator. Därför ett uttryck som `search.in(myfield, 'a, b, c')` motsvarar `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, förutom att `search.in` resulterar i mycket bättre prestanda.

Det finns två överlagringar av den `search.in` funktionen:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametrarna har definierats i tabellen nedan:

| Parameternamn | Type | Beskrivning |
| --- | --- | --- |
| `variable` | `Edm.String` | En sträng fältreferens (eller en variabel för intervallet över ett strängfält samling i fall där `search.in` används inuti en `any` eller `all` uttryck). |
| `valueList` | `Edm.String` | En sträng som innehåller en avgränsad lista med värdena för att matcha mot den `variable` parametern. Om den `delimiters` parametern inte anges, standard-avgränsarna är blanksteg och kommatecken. |
| `delimiters` | `Edm.String` | En sträng där varje tecken behandlas som avgränsare vid parsning av den `valueList` parametern. Standardvärdet för den här parametern är `' ,'` vilket innebär att alla värden med blanksteg och/eller skrivas ut skiljs åt. Om du vill använda avgränsarna än blanksteg och kommatecken dina värden innehåller de tecken du kan ange alternativa avgränsare som `'|'` i den här parametern. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestanda för `search.in`

Om du använder `search.in`, du kan förvänta dig under en sekund svarstid när den andra parametern innehåller en lista över hundratals eller tusentals värden. Det finns ingen explicit gräns för hur många objekt du kan skicka till `search.in`, även om du fortfarande är begränsad av tillåtna storleken. Svarstiden kommer dock att växa när antalet värden växer.

## <a name="examples"></a>Exempel

Hitta alla hotell med namn som är lika med ”Sea visa motel” eller ”Budget hotell'. Fraser innehåller blanksteg, vilket är en standard-avgränsare. Du kan ange ett alternativt avgränsare med enkla citattecken som den tredje parametern för frågesträngen:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Hitta alla hotell med namn som är lika med ”Sea visa motel” eller ”Budget hotell, avgränsade med ' |'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Hitta alla hotels med rum som har taggen ”wifi' eller 'badkar”:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Hitta en matchning på fraser i en samling, till exempel ”värmas upp handdukar rack' eller 'hairdryer ingår” i taggar.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Hitta alla hotels utan taggen ”motel” eller ”utrustning”:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
