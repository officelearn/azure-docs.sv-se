---
title: OData-filterreferens
titleSuffix: Azure Cognitive Search
description: OData språkreferens och fullständig syntax som används för att skapa filteruttryck i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282892"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter syntax i Azure Cognitive Search

Azure Cognitive Search använder [OData-filteruttryck](query-odata-filter-orderby-syntax.md) för att tillämpa ytterligare villkor på en sökfråga förutom fullständiga söktermer. I den här artikeln beskrivs syntaxen för filter i detalj. Mer allmän information om vilka filter som är och hur du använder dem för att förverkliga specifika frågescenarier finns [i Filter i Azure Cognitive Search](search-filters.md).

## <a name="syntax"></a>Syntax

Ett filter i OData-språket är ett booleskt uttryck, som i sin tur kan vara en av flera typer av uttryck, vilket visas av följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

De olika typerna av booleska uttryck är:

- Samla in filteruttryck med eller `any` `all`. Dessa tillämpar filtervillkor på samlingsfält. Mer information finns [i OData-samlingsoperatörer i Azure Cognitive Search](search-query-odata-collection-operators.md).
- Logiska uttryck som kombinerar andra booleska `and` `or`uttryck `not`med operatorerna , och . Mer information finns [i Logiska Operatorer för OData i Azure Cognitive Search](search-query-odata-logical-operators.md).
- Jämförelseuttryck som jämför fält eller intervallvariabler med `eq` `ne`konstanta värden med operatorerna , `gt`, , `lt`och `ge` `le`. Mer information finns [i OData-jämförelseoperatorer i Azure Cognitive Search](search-query-odata-comparison-operators.md). Jämförelseuttryck används också för att jämföra avstånd mellan `geo.distance` geospatisiska koordinater med hjälp av funktionen. Mer information finns i [OData geo-spatial funktioner i Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
- De booleska `true` bokstaverna och `false`. Dessa konstanter kan vara användbara ibland när programmatiskt generera filter, men annars inte tenderar att användas i praktiken.
- Anrop till booleska funktioner, inklusive:
  - `geo.intersects`, som testar om en viss punkt ligger inom en viss polygon. Mer information finns i [OData geo-spatial funktioner i Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, som jämför en fält- eller intervallvariabel med varje värde i en värdelista. Mer information finns [i `search.in` OData-funktionen i Azure Cognitive Search](search-query-odata-search-in-function.md).
  - `search.ismatch`och `search.ismatchscoring`, som kör fulltextsökningsåtgärder i en filterkontext. Mer information finns [i OData fulltextsökfunktioner i Azure Cognitive Search](search-query-odata-full-text-search-functions.md).
- Fältbanor eller områdesvariabler av typen `Edm.Boolean`. Om indexet till exempel har ett `IsEnabled` booleskt som anropas och `true`du vill returnera alla dokument `IsEnabled`där det här fältet finns, kan filteruttrycket bara vara namnet .
- Booleska uttryck inom parentes. Med hjälp av parenteser kan du uttryckligen bestämma ordningen på åtgärderna i ett filter. Mer information om OData-operatorernas standardprioritet finns i nästa avsnitt.

### <a name="operator-precedence-in-filters"></a>Operatörsprioritet i filter

Om du skriver ett filteruttryck utan parenteser runt dess underuttryck utvärderar Azure Cognitive Search det enligt en uppsättning operatörsprioritetsregler. Dessa regler baseras på vilka operatorer som används för att kombinera underuttryck. I följande tabell visas grupper av operatorer i ordning från högsta till lägsta prioritet:

| Grupp | Operator(er) |
| --- | --- |
| Logiska operatorer | `not` |
| Jämförelseoperatorer | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logiska operatorer | `and` |
| Logiska operatorer | `or` |

En operatör som är högre i tabellen ovan kommer att "binda tätare" till sina operor än andra operatörer. Till exempel `and` är av högre `or`prioritet än , och jämförelseoperatorer har högre prioritet än någon av dem, så följande två uttryck är likvärdiga:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Operatören `not` har den högsta prioriteten av alla - ännu högre än jämförelseoperatorerna. Det är därför om du försöker skriva ett filter som detta:

    not Rating gt 5

Det här felmeddelandet visas:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Det här felet inträffar eftersom `Rating` operatorn är associerad med bara fältet, som är av typen `Edm.Int32`, och inte med hela jämförelseuttrycket. Fixen är att sätta `not` operand av inom parentes:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Begränsningar för filterstorlek

Det finns gränser för storleken och komplexiteten i filteruttryck som du kan skicka till Azure Cognitive Search. Gränserna baseras ungefär på antalet satser i filteruttrycket. En bra riktlinje är att om du har hundratals klausuler, är du riskerar att överskrida gränsen. Vi rekommenderar att du utformar ditt program på ett sådant sätt att det inte genererar filter av obegränsad storlek.

> [!TIP]
> Om du använder [ `search.in` funktionen](search-query-odata-search-in-function.md) i stället för långa disjunctions av likhetsjämlikhetsjämlikhet kan du undvika filterklausulsgränsen, eftersom ett funktionsanrop räknas som en enda sats.

## <a name="examples"></a>Exempel

Hitta alla hotell med minst ett rum med ett baspris mindre än $ 200 som är klassade på eller över 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Hitta alla andra hotell än "Sea View Motel" som har renoverats sedan 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Hitta alla hotell som renoverades 2010 eller senare. Datumlitteralen innehåller tidszonsinformation för Stillahavsstandardtid:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Hitta alla hotell som har parkering ingår och där alla rum är rökfria:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-ELLER -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Hitta alla hotell som är lyx eller inkluderar parkering och har ett betyg på 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Hitta alla hotell med taggen "wifi" i minst ett rum `Collection(Edm.String)` (där varje rum har taggar lagrade i ett fält):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Hitta alla hotell med alla rum:  

    $filter=Rooms/any()

Hitta alla hotell som inte har rum:

    $filter=not Rooms/any()

Hitta alla hotell inom 10 kilometer från `Location` en given `Edm.GeographyPoint`referenspunkt (var är ett fält av typ):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Hitta alla hotell i en viss visningsport `Location` som beskrivs som en polygon (var är ett fält av typen Edm.GeographyPoint). Polygonen måste stängas, vilket innebär att de första och sista punktuppsättningarna måste vara desamma. Dessutom [måste punkterna anges i moturs ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Hitta alla hotell där fältet "Beskrivning" är null. Fältet är null om det aldrig har angetts, eller om det uttryckligen angavs till null:  

    $filter=Description eq null

Hitta alla hotell med namn som är lika med antingen "Sea View motell" eller "Budget hotel"). Dessa fraser innehåller blanksteg och blanksteg är en standardavgränsare. Du kan ange en alternativ avgränsare i enstaka citattecken som den tredje strängparametern:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Hitta alla hotell med namn som är lika med antingen "Sea View motel" eller "Budget hotel" åtskilda av |'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Hitta alla hotell där alla rum har taggen "wifi" eller "badkar":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Hitta en matchning på fraser i en samling, till exempel "handdukshängare" eller "hårtork ingår" i taggar.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Hitta dokument med ordet "vattnet". Den här filterfrågan är identisk `search=waterfront`med en [sökbegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med .

    $filter=search.ismatchscoring('waterfront')

Hitta dokument med ordet "vandrarhem" och betyg större eller lika med 4, eller dokument med ordet "motell" och betyg lika med 5. Den här begäran kunde inte `search.ismatchscoring` uttryckas utan funktionen eftersom den kombinerar `or`fulltextsökning med filteråtgärder med .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Hitta dokument utan ordet "lyx".

    $filter=not search.ismatch('luxury')

Hitta dokument med frasen "havsvy" eller betyg som är lika med 5. Frågan `search.ismatchscoring` körs endast mot fält `HotelName` `Description`och . Dokument som matchade endast den andra klausulen i disjunction `Rating` kommer att returneras också - hotell med lika med 5. Dessa dokument kommer att returneras med poäng lika med noll för att klargöra att de inte matchade någon av de poängsatta delarna av uttrycket.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Hitta hotell där termerna "hotell" och "flygplats" inte är mer än fem ord ifrån varandra i beskrivningen, och där alla rum är rökfria. Den här frågan använder det [fullständiga Lucene-frågespråket](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
