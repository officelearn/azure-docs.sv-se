---
title: Referens för OData-filter – Azure Search
description: OData-Språkreferens för filter-syntax i Azure Search frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8817ce075409a3f166b82404767697dc1326cc89
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647595"
---
# <a name="odata-filter-syntax-in-azure-search"></a>OData-$filter syntax i Azure Search

Azure Search använder [OData filter-uttryck](query-odata-filter-orderby-syntax.md) för att tillämpa ytterligare villkor för en Sök fråga förutom full texts öknings termer. I den här artikeln beskrivs syntaxen för filter i detalj. Mer allmän information om vilka filter som är och hur du använder dem för att realisera vissa fråge scenarier finns i [filter i Azure Search](search-filters.md).

## <a name="syntax"></a>Syntax

Ett filter i OData-språket är ett booleskt uttryck som i sin tur kan vara ett av flera typer av uttryck, som visas i följande EBNF ([Extended backable-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Se [syntax för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Typerna av booleska uttryck är:

- Samlings filter uttryck `any` med `all`eller. Dessa använder filter villkor i samlings fält. Mer information finns i [OData Collection-operatörer i Azure Search](search-query-odata-collection-operators.md).
- Logiska uttryck som kombinerar andra booleska uttryck med operatorerna `and`, `or`och `not`. Mer information finns i avsnittet [om logiska OData-operatörer i Azure Search](search-query-odata-logical-operators.md).
- Jämförelse uttryck, som jämför fält eller intervall-variabler med konstanta värden med `eq`operatorerna `gt` `ge` `ne` `lt`,,,, `le`och. Mer information finns i [OData-jämförelse operatorer i Azure Search](search-query-odata-comparison-operators.md). Jämförelse uttryck används också för att jämföra avstånd mellan geo-spatiala koordinater `geo.distance` med funktionen. Mer information finns i [OData geo-spatial-funktioner i Azure Search](search-query-odata-geo-spatial-functions.md).
- Booleska litteraler `true` och `false`. Dessa konstanter kan vara användbara ibland när du skapar filter på ett programmerings sätt, men annars inte brukar användas i praktiken.
- Anrop till booleska funktioner, inklusive:
  - `geo.intersects`, som testar om en viss punkt är inom en viss polygon. Mer information finns i [OData geo-spatial-funktioner i Azure Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, som jämför en fält-eller intervall variabel med varje värde i en lista med värden. Mer information finns i [OData `search.in` -funktionen i Azure Search](search-query-odata-search-in-function.md).
  - `search.ismatch`och `search.ismatchscoring`, som kör full texts öknings åtgärder i en filter kontext. Mer information finns i [OData full text search Functions i Azure Search](search-query-odata-full-text-search-functions.md).
- Fält Sök vägar eller intervall-variabler `Edm.Boolean`av typen. Om ditt index till exempel har ett Boolean-fält som `IsEnabled` heter och du vill returnera alla dokument där det här fältet `true`är, kan filter uttrycket bara vara namnet `IsEnabled`.
- Booleska uttryck inom parentes. Med hjälp av parenteser kan du uttryckligen bestämma ordningen på åtgärderna i ett filter. Mer information om OData-operatörernas standard prioritet finns i nästa avsnitt.

### <a name="operator-precedence-in-filters"></a>Operator prioritet i filter

Om du skriver ett filter uttryck utan parentes runt under uttryck, kommer Azure Search att utvärdera det enligt en uppsättning av operatorernas prioritets regler. Dessa regler baseras på vilka operatorer som används för att kombinera under uttryck. I följande tabell visas en lista över operatorer i ordning från högsta till lägsta prioritet:

| Grupp | Operator (er) |
| --- | --- |
| Logiska operatorer | `not` |
| Jämförelseoperatorer | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logiska operatorer | `and` |
| Logiska operatorer | `or` |

En operator som är högre i tabellen ovan blir "bind mer tätt" till dess operander än andra operatorer. Till exempel `and` är högre prioritet än `or`och jämförelse operatorer har högre prioritet än någon av dem, så följande två uttryck är likvärdiga:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` Operatorn har högst prioritet för alla – ännu högre än jämförelse operatorerna. Det är därför om du försöker skriva ett filter så här:

    not Rating gt 5

Du får det här fel meddelandet:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Det här felet beror på att operatorn bara `Rating` är kopplad till fältet, vilket är av typen `Edm.Int32`och inte med hela jämförelse uttrycket. Korrigeringen är att ge operanden `not` i parentes:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Begränsningar för filter storlek

Det finns gränser för storlek och komplexitet för filter uttryck som du kan skicka till Azure Search. Gränserna baseras ungefär på antalet satser i filter uttrycket. En lämplig rikt linje är att om du har hundratals satser, riskerar du att överskrida gränsen. Vi rekommenderar att du utformar ditt program på ett sådant sätt att det inte genererar filter av obegränsad storlek.

> [!TIP]
> Om du använder [ funktioneniställetförlångadisknutaravlikhetsjämförelserkanduundvikafiltersatsgränsen,eftersomettfunktionsanropräknassomenenskildsats.`search.in` ](search-query-odata-search-in-function.md)

## <a name="examples"></a>Exempel

Hitta alla hotell med minst ett rum med en bas taxa som är mindre än $200 som klassificeras enligt eller över 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Hitta alla andra hotell än "sto View Motel" som har renovated sedan 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Hitta alla hotell som var renovated i 2010 eller senare. Datetime-literalen innehåller tids zons information för Pacific, normal tid:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Hitta alla hotell som har parkera och där alla rum är icke-rökning:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-ELLER  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Hitta alla hotell som är lyxen eller innehåller parkering och har en klassificering på 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Hitta alla hotell med taggen "WiFi" i minst ett rum (där varje rum har Taggar lagrade i ett `Collection(Edm.String)` fält):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Hitta alla hotell med alla rum:  

    $filter=Rooms/any()

Hitta alla hotell som inte har några rum:

    $filter=not Rooms/any()

Hitta alla hotell inom 10 kilo meter från en viss referens punkt ( `Location` där är ett fält av `Edm.GeographyPoint`typen):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Hitta alla hotell inom ett angivet visnings område som beskrivs som en polygon `Location` (där är ett fält av typen EDM. GeographyPoint). Polygonen måste vara stängd, vilket innebär att de första och sista punkt uppsättningarna måste vara desamma. Dessutom [måste punkterna visas i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Hitta alla hotell där fältet Beskrivning är null. Fältet kommer att vara null om det aldrig har angetts, eller om det uttryckligen har angetts till null:  

    $filter=Description eq null

Hitta alla hotell med namn som motsvarar antingen "Sea View Motel" eller "budget hotell"). Dessa fraser innehåller blank steg och blank steg är en standard avgränsare. Du kan ange en alternativ avgränsare i enkla citat tecken som den tredje sträng parametern:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Hitta alla hotell med namn som är lika med "Sea View Motel" eller "budget hotellet", separerade med "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Hitta alla hotell där alla rum har taggen "WiFi" eller "tub":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Hitta en matchning på fraser i en samling, t. ex. "uppvärmd hand duks-rack" eller "Hairdryer ingår" i taggar.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Hitta dokument med ordet "Waterfront". Den här filter frågan är identisk med en search- `search=waterfront` [begäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med.

    $filter=search.ismatchscoring('waterfront')

Hitta dokument med ordet "Hostel" och klassificera större eller lika med 4 eller dokument med ordet "Motel" och betyget är lika med 5. Den här begäran kunde inte uttryckas utan `search.ismatchscoring` funktionen eftersom den kombinerar full texts ökning med filter åtgärder med hjälp av. `or`

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Hitta dokument utan ordet "lyxen".

    $filter=not search.ismatch('luxury')

Hitta dokument med frasen "oceanen" eller klassificeringen lika med 5. Frågan körs bara mot fält `HotelName` och `Description`. `search.ismatchscoring` Dokument som matchade bara den andra satsen i disknuten returneras för--hotell med `Rating` lika med 5. De dokumenten kommer att returneras med poängen lika med noll för att det ska vara uppenbart att de inte matchar någon av de förväntade delarna i uttrycket.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Hitta hotell där villkoren "hotell" och "flyg plats" inte innehåller fler än fem ord i beskrivningen och där alla rum är icke-rökning. Den här frågan använder det [fullständiga Lucene-frågespråket](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
