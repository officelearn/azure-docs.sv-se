---
title: OData geospatial funktionsreferens – Azure Search
description: OData geospatiala funktioner, geo.distance och geo.intersects i Azure Search-frågor.
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079800"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>OData geospatiala funktioner i Azure Search - `geo.distance` och `geo.intersects`

Azure Search har stöd för geospatiala frågor i [OData-filteruttryck](query-odata-filter-orderby-syntax.md) via den `geo.distance` och `geo.intersects` funktioner. Den `geo.distance` funktionen returnerar avståndet mellan två punkter i kilometer, en som ett fält eller intervall variabeln och en som en konstant skickas som en del av filtret. Den `geo.intersects` returnerar `true` om en viss tidpunkt som ligger inom en viss polygon, där punkten är ett fält eller ett intervall variabel och polygonen har angetts som en konstant som skickas som en del av filtret.

Den `geo.distance` funktionen kan också användas i den [ **$orderby** parametern](search-query-odata-orderby.md) kan sortera sökresultat genom avståndet från en viss tidpunkt. Syntaxen för `geo.distance` i **$orderby** är densamma som den tillhör **$filter**. När du använder `geo.distance` i **$orderby**, fältet som gäller måste vara av typen `Edm.GeographyPoint` och det måste också vara **sorterbar**.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur av den `geo.distance` och `geo.intersects` funktioner, samt geospatiala värden där de fungerar:

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

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

### <a name="geodistance"></a>GEO.Distance

Den `geo.distance` funktionen tar två parametrar av typen `Edm.GeographyPoint` och returnerar ett `Edm.Double` värde som är avståndet mellan dem i kilometer. Detta skiljer sig från andra tjänster som stöder geospatial OData-åtgärder, som vanligtvis returnera avstånd i meter.

En av parametrarna till `geo.distance` måste vara en konstant för geografisk plats och andra måste vara en fältsökväg (eller en variabel för adressintervallet om det finns ett filter iterera över ett fält av typen `Collection(Edm.GeographyPoint)`). Ordningen på de här parametrarna spelar ingen roll.

Geografi punkt konstant är i formatet `geography'POINT(<longitude> <latitude>)'`, där longitud och latitud är numeriska konstanter.

> [!NOTE]
> När du använder `geo.distance` i ett filter måste du jämföra avståndet som returnerades av funktionen med ett konstant med `lt`, `le`, `gt`, eller `ge`. Operatorer `eq` och `ne` stöds inte när du jämför avstånd. Till exempel det här är en korrekt användning av `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>GEO.intersects

Den `geo.intersects` funktionen använder en variabel av typen `Edm.GeographyPoint` och en konstant `Edm.GeographyPolygon` och returnerar ett `Edm.Boolean`  --  `true` om punkten befinner sig inom gränserna för polygon, `false` annars.

Polygonen är en tvådimensionella yta som lagras som en sekvens av punkter som definierar en omgivande ring (se den [exempel](#examples) nedan). Polygonen måste stängas, vilket innebär att först och sista punkt uppsättningar måste vara samma. [Punkterna i en polygon måste vara i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geospatial frågor och polygoner utsträckning 180th kartmeridian

För många geospatial fråga bibliotek utformningen av en fråga som innehåller 180th kartmeridian (nära Datumgränsen) är antingen off-limits eller kräver en lösning, till exempel dela upp polygonen i två, en på endera sidan av kartmeridian.

I Azure Search, geospatiala frågor som innehåller 180 grader longitud fungerar som väntat om formen fråga är rektangulär och din koordinater justeras till en rutnätslayout längs longitud och latitud (till exempel `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). I annat fall du dela polygon-metoden för icke-rektangulär eller feljusterade former.  

### <a name="geo-spatial-functions-and-null"></a>Geospatiala funktioner och `null`

Som alla andra icke-samlingen fält i Azure Search, fält av typen `Edm.GeographyPoint` kan innehålla `null` värden. När Azure Search utvärderar `geo.intersects` för ett fält som är `null`, resultatet alltid kommer att `false`. Beteendet för `geo.distance` i det här fallet beror på kontexten:

- I filter, `geo.distance` av en `null` fältet resulterar i `null`. Det innebär att dokumentet inte matchar eftersom `null` jämfört med alla icke-null-värde som utvärderas till `false`.
- När du sorterar resultaten med hjälp av **$orderby**, `geo.distance` av en `null` fältet resulterar i största möjliga avstånd. Dokument med sådant fält du vill sortera lägre än alla andra när Sorteringsriktningen `asc` är används (standard), och det är högre än alla andra när riktningen är `desc`.

## <a name="examples"></a>Exempel

### <a name="filter-examples"></a>Filterexempel

Hitta alla hotell inom 10 kilometer för en viss referenspunkt (där platsen är ett fält av typen `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Hitta alla hotell inom en viss visningsområdet beskrivs som en polygon (där platsen är ett fält av typen `Edm.GeographyPoint`). Observera att polygonen är stängd (första och sista-mängder vara samma) och [punkterna måste anges i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Sortering efter exempel

Sortera fallande efter hotell `rating`, sedan stigande efter avståndet från de angivna koordinaterna:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortera hotell i fallande ordning av `search.score` och `rating`, och sedan i stigande ordning efter avståndet från de angivna koordinaterna så att mellan två hotels med identiska betyg den närmaste som visas först:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
