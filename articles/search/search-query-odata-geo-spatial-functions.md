---
title: OData geo-spatial funktions referens – Azure Search
description: OData geo-spatiala funktioner, geo. Distance och geo. korsar, i Azure Search frågor.
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
ms.openlocfilehash: 9585a9a7ea976ed32ccb8eed1e69877339196f87
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647565"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>OData geo-spatial-funktioner i Azure Search `geo.distance` -och`geo.intersects`

Azure Search stöder geo-spatiala frågor i [OData filter](query-odata-filter-orderby-syntax.md) -uttryck `geo.distance` via `geo.intersects` -och-funktionerna. `geo.distance` Funktionen returnerar avståndet i kilo meter mellan två punkter, ett fält eller en intervall variabel, och en är en konstant som ska skickas som en del av filtret. `geo.intersects` Funktionen returnerar`true` om en viss punkt är inom en viss polygon, där punkten är en fält-eller intervall variabel och polygonen anges som en konstant som skickas som en del av filtret.

Funktionen kan också användas i [ **$OrderBy** -parametern](search-query-odata-orderby.md) för att sortera Sök Resultat efter avstånd från en viss punkt. `geo.distance` Syntaxen för `geo.distance` i **$OrderBy** är samma som i **$filter**. När du `geo.distance` använder i **$OrderBy**måste det fält som det gäller för vara av typen `Edm.GeographyPoint` och det måste också vara **sorterbart**.

## <a name="syntax"></a>Syntax

Följande ebnf ([Extended backable-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken i `geo.distance` och `geo.intersects` -funktionerna, samt de geo-spatial-värden som de fungerar på:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Se [syntax för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

### <a name="geodistance"></a>Geo. Distance

Funktionen använder två parametrar av typen `Edm.GeographyPoint` och returnerar ett `Edm.Double` värde som är avståndet mellan dem i kilo meter. `geo.distance` Detta skiljer sig från andra tjänster som stöder OData geo-spatiala åtgärder, som vanligt vis returnerar avstånd i meter.

En av parametrarna `geo.distance` måste vara en konstant med en geografisk punkt och den andra måste vara en fält Sök väg (eller en intervall variabel om ett filter ska itereras över ett fält av typen `Collection(Edm.GeographyPoint)`). Ordningen på dessa parametrar spelar ingen roll.

Geografisk punkts konstant är av formen `geography'POINT(<longitude> <latitude>)'`där longitud och latitud är numeriska konstanter.

> [!NOTE]
> När du `geo.distance` använder i ett filter måste du jämföra avståndet som returneras av funktionen med en konstant med hjälp `lt`av `le`, `gt`, eller `ge`. Operatorerna `eq` och `ne` stöds inte när du jämför avstånd. Detta är till exempel en korrekt användning av `geo.distance`:. `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>Geo. snitt

`Edm.Boolean` `Edm.GeographyPolygon` `Edm.GeographyPoint` `true` `false` Funktionen tar en variabel av typen och en konstant och returnerar en  --  om punkten är inom gränserna för polygonen, annars. `geo.intersects`

Polygonen är en tvådimensionell yta som lagras som en sekvens med punkter som definierar en avgränsnings ring (se [exemplen](#examples) nedan). Polygonen måste stängas, vilket innebär att de första och sista punkt uppsättningarna måste vara desamma. [Punkter i en polygon måste vara i moturs ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-spatiala frågor och polygoner som sträcker sig över 180th-Meri Dian

För många geo-spatiala fråge bibliotek skapas en fråga som innehåller 180th-Meri Dian (nära datum gränsen,) eller som kräver en lösning, t. ex. delning av polygonen i två, en på endera sidan av Meri Dian.

I Azure Search fungerar geo-spatiala frågor som innehåller 180 graders longitud som förväntat om frågetexten är rektangulär och koordinaterna stämmer överens med en rutnäts layout längs longitud och latitud ( `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`till exempel). Annars, för icke-rektangulära eller ej justerade former, bör du tänka på metoden dela upp polygon.  

### <a name="geo-spatial-functions-and-null"></a>Geo-spatiala funktioner och`null`

Precis som alla andra icke-samlings fält i Azure Search kan fält `Edm.GeographyPoint` av typen `null` innehålla värden. När Azure Search utvärderas `geo.intersects` för ett fält `null`, är resultatet alltid `false`. Beteendet för `geo.distance` i det här fallet beror på kontexten:

- I filter, `geo.distance` för ett `null` fält resulterar i `null`. Det innebär att dokumentet inte överensstämmer eftersom `null` det jämförs med ett värde som inte är null `false`utvärderas till.
- När du sorterar resultat med `geo.distance` hjälp av `null` **$OrderBy**, i ett fält resultat på maximalt möjligt avstånd. Dokument med sådana fält sorteras lägre än alla andra när sorterings riktningen `asc` används (standard) och högre än för alla andra när riktningen är. `desc`

## <a name="examples"></a>Exempel

### <a name="filter-examples"></a>Filter exempel

Hitta alla hotell inom 10 kilo meter från en viss referens punkt (där platsen är ett fält av `Edm.GeographyPoint`typen):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Hitta alla hotell inom ett angivet visnings område som beskrivs som en polygon (där platsen är ett fält `Edm.GeographyPoint`av typen). Observera att polygonen är stängd (de första och sista punkt uppsättningarna måste vara identiska) och [punkterna måste anges i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Ordning – exempel

Sortera hotell fallande efter `rating`, sedan stigande efter avstånd från givna koordinater:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortera hotell i fallande ordning efter `search.score` och `rating`och sedan i stigande ordning efter avstånd från givna koordinater, så att de två hotellen med identiska klassificeringar visas först:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
