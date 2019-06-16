---
title: OData-filterreferens – Azure Search
description: Språkreferens för OData för filtersyntaxen i Azure Search-frågor.
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079917"
---
# <a name="odata-filter-syntax-in-azure-search"></a>OData $filter syntax i Azure Search

Använder Azure Search [OData-filteruttryck](query-odata-filter-orderby-syntax.md) att tillämpa ytterligare kriterier till en sökfråga förutom villkoren för fulltextsökning. Den här artikeln beskrivs syntaxen för filter i detalj. Mer allmän information om filter är och hur du använder dem för att upptäcka specifika fråga scenarier finns i [filter i Azure Search](search-filters.md).

## <a name="syntax"></a>Syntax

Ett filter på OData-språk är ett booleskt uttryck, som i sin tur kan vara något av flera typer av uttryck, som i följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Typer av booleska uttryck vara:

- Filteruttryck för samling med hjälp av `any` eller `all`. Dessa tillämpas Filtervillkoren på samlingen fält. Mer information finns i [OData samling operatörer i Azure Search](search-query-odata-collection-operators.md).
- Logiska uttryck som kombinerar andra booleskt uttryck med hjälp av operatorerna `and`, `or`, och `not`. Mer information finns i [OData logiska operatorer i Azure Search](search-query-odata-logical-operators.md).
- Jämförelseuttryck som jämför fält eller intervall variabler till konstanta värden med hjälp av operatorerna `eq`, `ne`, `gt`, `lt`, `ge`, och `le`. Mer information finns i [OData jämförelseoperatorer i Azure Search](search-query-odata-comparison-operators.md). Jämförelseuttryck används också för att jämföra avstånd mellan geospatial koordinater med hjälp av den `geo.distance` funktion. Mer information finns i [OData geospatiala funktioner i Azure Search](search-query-odata-geo-spatial-functions.md).
- Booleska konstanter `true` och `false`. Konstanterna kan vara användbart ibland när du genererar programmässigt filter, men annars inte användas i praktiken.
- Anrop till booleskt funktioner, inklusive:
  - `geo.intersects`, som testar om en viss tidpunkt som ligger inom en viss polygon. Mer information finns i [OData geospatiala funktioner i Azure Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, som jämför en variabel för fältet eller ett intervall med varje värde i en lista med värden. Mer information finns i [OData `search.in` funktion i Azure Search](search-query-odata-search-in-function.md).
  - `search.ismatch` och `search.ismatchscoring`, vilket köra fulltextsökning i en filterkontext. Mer information finns i [OData fulltextsökning funktioner i Azure Search](search-query-odata-full-text-search-functions.md).
- Fältet sökvägar eller intervall variabler av typen `Edm.Boolean`. Exempel: om ditt index har ett booleskt fält med namnet `IsEnabled` och du vill returnera alla dokument där det här fältet är `true`, filteruttrycket kan bara vara namnet `IsEnabled`.
- Booleska uttryck inom parentes. Med parenteser kan bidra till att uttryckligen fastställa i vilken ordning åtgärder i ett filter. Mer information om standard prioriteten för OData-operatörerna finns i nästa avsnitt.

### <a name="operator-precedence-in-filters"></a>Operatorer i filter

Om du skriver ett filteruttryck med utan parenteser kring dess underordnade uttryck utvärderar Azure Search den enligt en uppsättning regler för kommandoprioritering för operatorn. Dessa regler är baserade på vilka operatörer som används för att kombinera underuttryck. I följande tabell visas grupper av operatörer i ordning från högsta till lägsta prioritet:

| Grupp | Operatören eller operatörerna |
| --- | --- |
| Logiska operatorer | `not` |
| Jämförelseoperatorer | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logiska operatorer | `and` |
| Logiska operatorer | `or` |

En operatör som är högre upp i tabellen ovan ”Binder tätare” till operanderna än andra operatörer. Till exempel `and` har högre prioritet än `or`, och jämförelseoperatorer har högre prioritet än något av dem, så att följande två uttryck är likvärdiga:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Den `not` operatören har högst prioritet för alla – även högre än jämförelseoperatorerna. Det är därför om du försöker skapa ett filter så här:

    not Rating gt 5

Du får felmeddelandet:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Det här felet inträffar eftersom operatorn är associerad med just den `Rating` fält som är av typen `Edm.Int32`, och inte med hela jämförelseuttryck. Korrigeringen är att placera operanden för `not` inom parentes:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Storleksbegränsningar för filter

Det finns begränsningar för storlek och komplexitet filteruttryck som du kan skicka till Azure Search. Gränserna är ungefär utifrån antalet satser i filteruttrycket. En tumregel är om du har hundratals satser kan vara utsatta för överskrider gränsen. Vi rekommenderar att du designa programmet så att den inte genererar filter med obegränsad storlek.

> [!TIP]
> Med hjälp av [den `search.in` funktionen](search-query-odata-search-in-function.md) i stället för lång disjunctions lika jämförelser kan undvika filter-satsen gräns, eftersom ett funktionsanrop räknas som en enskild-sats.

## <a name="examples"></a>Exempel

Hitta alla hotels med minst en plats med en basavgift mindre än 200 USD som är klassificerade vid eller över 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Hitta alla hotell än ”Sea visa Motel” som har varit renoverade sedan 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Hitta alla hotell som har renoverade 2010 eller senare. Datetime-literalen innehåller tidszonsinformation för Pacific Standard Time:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Hitta alla hotell som har parkering ingår och där alla rooms är icke-hälsan:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- ELLER –  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Hitta alla hotell som är Lyxig eller inkludera parkering och har en klassificering på 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Hitta alla hotell med taggen ”wifi” i minst ett rum (där varje rum har taggar som lagras i en `Collection(Edm.String)` fält):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Hitta alla hotels med alla rum:  

    $filter=Rooms/any()

Hitta alla hotell som inte har rum:

    $filter=not Rooms/any()

Hitta alla hotell inom 10 kilometer för en viss referenspunkt (där `Location` är ett fält av typen `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Hitta alla hotell inom en viss visningsområdet beskrivs som en polygon (där `Location` är ett fält av typen Edm.GeographyPoint). Polygonen måste stängas, vilket innebär att först och sista punkt uppsättningar måste vara samma. Dessutom [punkterna måste anges i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Hitta alla hotels där fältet ”Beskrivning” är null. Fältet är null om det har aldrig har, eller om den explicit inställd på null:  

    $filter=Description eq null

Hitta alla hotels med namnet lika med ”Sea visa motel” eller ”Budget hotell”). Dessa fraser innehålla blanksteg och utrymme är en standard-avgränsare. Du kan ange ett alternativt avgränsare med enkla citattecken som den tredje parametern för frågesträngen:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Hitta alla hotell med namn som är lika med ”Sea visa motel” eller ”Budget hotell, avgränsade med ' |'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Hitta alla hotels där alla rum har taggen ”wifi' eller 'badkar”:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Hitta en matchning på fraser i en samling, till exempel ”värmas upp handdukar rack' eller 'hairdryer ingår” i taggar.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Hitta dokument med ordet ”Hamnområde”. Den här filterfrågan är identisk med en [sökbegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Hitta dokument med ordet ”hostel” och omdöme större eller lika med 4 eller dokument med ordet ”motel” och omdöme lika med 5. Den här begäran kunde inte uttryckas utan den `search.ismatchscoring` fungera eftersom den kombinerar fulltextsökning med filtret åtgärder med hjälp av `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Hitta dokument utan ordet ”Lyxig”.

    $filter=not search.ismatch('luxury')

Hitta dokument med frasen ”ocean view” eller klassificering som motsvarar 5. Den `search.ismatchscoring` frågan körs bara mot fält `HotelName` och `Description`. Dokument som matchade endast andra delen av disjunktion som ska returneras för--hotels med `Rating` lika med 5. Dessa dokument kommer att returneras med poäng som är lika med noll så att de blir Rensa att de inte matchar någon av de poängsatta delarna i uttrycket.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Hitta hotell där villkoren ”hotell” och ”flygplats” är mer än fem ord från varandra i beskrivningen och där alla rooms är icke-hälsan. Den här frågan använder den [fullständig Lucene frågespråk](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
