---
title: OData geografisk funktionsreferens
titleSuffix: Azure Cognitive Search
description: Syntax- och referensdokumentation för användning av OData geo-spatial funktioner, geo.distance och geo.intersects, i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113173"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData geo-rumsliga funktioner `geo.distance` i Azure Cognitive Search - och`geo.intersects`

Azure Cognitive Search stöder geo-spatiala frågor i `geo.distance` [OData-filteruttryck](query-odata-filter-orderby-syntax.md) via funktionerna och. `geo.intersects` Funktionen `geo.distance` returnerar avståndet i kilometer mellan två punkter, varav en är ett fält eller områdesvariabel och en som är en konstant som skickas som en del av filtret. Funktionen `geo.intersects` returneras `true` om en viss punkt ligger inom en viss polygon, där punkten är en fält- eller intervallvariabel och polygonen anges som en konstant som skickas som en del av filtret.

Funktionen `geo.distance` kan också användas i [ **parametern $orderby** ](search-query-odata-orderby.md) för att sortera sökresultat efter avstånd från en viss punkt. Syntaxen `geo.distance` för i **$orderby** är densamma som i **$filter**. När `geo.distance` du använder i **$orderby**måste det fält som `Edm.GeographyPoint` det gäller vara av typen och det måste också vara **sorterbart**.

> [!NOTE]
> När `geo.distance` du använder i **parametern $orderby** får fältet som du skickar till funktionen bara innehålla en enda geopunkt. Med andra ord måste det `Edm.GeographyPoint` vara `Collection(Edm.GeographyPoint)`av typ och inte . Det går inte att sortera på samlingsfält i Azure Cognitive Search.

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `geo.distance` definierar grammatiken i funktionerna och `geo.intersects` funktionerna, liksom de geospatiala värden som de arbetar på:

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

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

### <a name="geodistance"></a>geo.avstånd

Funktionen `geo.distance` tar två parametrar `Edm.GeographyPoint` av `Edm.Double` typ och returnerar ett värde som är avståndet mellan dem i kilometer. Detta skiljer sig från andra tjänster som stöder OData geo-spatial verksamhet, som vanligtvis returnerar avstånd i meter.

En av parametrarna som måste `geo.distance` vara en geografipunktskonstant och den andra måste vara en fältsökväg (eller `Collection(Edm.GeographyPoint)`en områdesvariabel när det gäller ett filter som itererar över ett fält av typ ). Ordningen på dessa parametrar spelar ingen roll.

Geografipunktskonstanten är `geography'POINT(<longitude> <latitude>)'`av formen , där longituden och latitud är numeriska konstanter.

> [!NOTE]
> När `geo.distance` du använder i ett filter måste du jämföra avståndet `lt`som `le` `gt`returneras `ge`av funktionen med en konstant med , , eller . Operatörerna `eq` och `ne` stöds inte vid jämförelse av avstånd. Detta är till exempel en `geo.distance` `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`korrekt användning av : .

### <a name="geointersects"></a>geo.skär

Funktionen `geo.intersects` tar en variabel `Edm.GeographyPoint` av `Edm.GeographyPolygon` typ och `Edm.Boolean`  --  `true` en konstant och returnerar en om `false` punkten är inom gränserna för polygonen, annars.

Polygonen är en tvådimensionell yta som lagras som en sekvens av punkter som definierar en begränsningsring (se [exemplen](#examples) nedan). Polygonen måste stängas, vilket innebär att de första och sista punktuppsättningarna måste vara desamma. [Punkterna i en polygon måste vara i moturs ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-rumsliga frågor och polygoner som spänner över den 180: e meridianen

För många geo-spatiala frågebibliotek som formulerar en fråga som innehåller den 180:e meridianen (nära datumlinjen) är antingen förbjudet eller kräver en lösning, till exempel dela polygonen i två, en på vardera sidan av meridianen.

I Azure Cognitive Search fungerar geospatisella frågor som innehåller 180-graders longitud som förväntat om frågeformen är rektangulär `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`och koordinaterna justeras mot en rutnätslayout längs longitud och latitud (till exempel ). I annat fall bör du tänka på den delade polygonmetoden för icke-rektangulära eller ojusterade former.  

### <a name="geo-spatial-functions-and-null"></a>Geo-rumsliga funktioner och`null`

Precis som alla andra fält som inte är `Edm.GeographyPoint` samling `null` i Azure Cognitive Search kan fält av typen innehålla värden. När Azure Cognitive `geo.intersects` Search utvärderar `null`för ett fält `false`som är , kommer resultatet alltid att vara . Beteendet `geo.distance` för i det här fallet beror på sammanhanget:

- I filter `geo.distance` resulterar `null` ett `null`fält i . Det innebär att dokumentet `null` inte matchar eftersom det jämfört `false`med ett värde som inte är null utvärderas till .
- När du sorterar resultat med `geo.distance` hjälp `null` av **$orderby**, i ett fält resulterar i maximalt möjligt avstånd. Dokument med ett sådant fält sorteras lägre `asc` än alla andra när sorteringsriktningen används `desc`(standard) och högre än alla andra när riktningen är .

## <a name="examples"></a>Exempel

### <a name="filter-examples"></a>Filterexempel

Hitta alla hotell inom 10 kilometer från en viss referenspunkt (där platsen är ett fält av typ): `Edm.GeographyPoint`

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Hitta alla hotell inom en viss visningsport som beskrivs `Edm.GeographyPoint`som en polygon (där platsen är ett fält av typen). Observera att polygonen är stängd (de första och sista punktuppsättningarna måste vara desamma) och [att punkterna måste anges i moturs ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Exempel på order efter

Sortera hotell fallande efter `rating`, sedan stigande efter avstånd från de angivna koordinaterna:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortera hotell i fallande ordning `search.score` efter och `rating`, och sedan i stigande ordning efter avstånd från de angivna koordinaterna så att mellan två hotell med identiska betyg, den närmaste listas först:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
