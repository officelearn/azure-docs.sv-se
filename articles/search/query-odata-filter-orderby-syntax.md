---
title: OData-uttryckssyntax för filter och order-by-satser – Azure Search
description: Filter och order by uttryck OData-syntax för Azure Search-frågor.
ms.date: 03/27/2019
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
ms.openlocfilehash: 8445ab2c8797226b08519e2f186350a31416f049
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578415"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>OData-uttryckssyntax för filter och order by-satser i Azure Search

Azure Search har stöd för en delmängd av OData-uttryckssyntax för **$filter** och **$orderby** uttryck. Filteruttryck utvärderas under parsning, begränsa sökningen till specifika fält eller lägga till matchar de villkor som används vid index skanningar. Order by-uttryck används som ett efterbearbetning steg över en resultatmängd. Både filter och order by-uttryck som ingår i en fråga, följa en OData-syntax som är oberoende av den [enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) frågesyntax som används i en **search** parameter. Den här artikeln innehåller referensdokumentation för OData-uttryck som används i filter och sorteringsuttryck.

## <a name="filter-syntax"></a>Syntaxen för filtret

En **$filter** uttryck kan köras fristående som ett fullständigt uttrycklig fråga eller förfina en fråga som har ytterligare parametrar. I följande exempel visas några viktiga scenarier. I det första exemplet är filtret större delen av frågan.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Ett annat vanligt användningsfall är filter kombineras aspekter, där filtret minskar fråga ytan baserat på en användare-drivna aspekten navigering val:

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Filteroperatorerna  

- Logiska operatorer (och, eller inte).  

- Jämförelseuttryck (`eq, ne, gt, lt, ge, le`). Strängjämförelser är skiftlägeskänsliga.  

- Konstanter stöds [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) typer (se [datatyper som stöds &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en lista över typer som stöds). Konstanterna samlingstyper stöds inte.  

- Referenser till fältnamn. Endast `filterable` fält kan användas i filteruttryck.  

- `any` utan parametrar. Detta testar om ett fält av typen `Collection(Edm.String)` innehåller alla element.  

- `any` och `all` med stöd för begränsade lambda-uttryck. 
    
  -   `any/all` stöds på fält av typen `Collection(Edm.String)`. 
    
  -   `any` kan bara användas med enkla likhet uttryck eller en `search.in` funktion. Enkla uttryck består av en jämförelse mellan ett fält och ett exakt värde, t.ex. `Title eq 'Magna Carta'`.
    
  -   `all` kan bara användas med enkla ojämlikhet uttryck eller en `not search.in`.   

- Geospatiala funktioner `geo.distance` och `geo.intersects`. Den `geo.distance` funktionen returnerar avståndet mellan två punkter i kilometer, en som ett fält och en som en konstant skickas som en del av filtret. Den `geo.intersects` funktionen returnerar true om en viss tidpunkt som ligger inom en viss polygon, där det är ett fält och polygonen har angetts som en konstant som skickas som en del av filtret.  

  Polygonen är en tvådimensionella yta som lagras som en sekvens med punkter definierar en avgränsar ringbuffertens (se exemplet nedan). Polygonen måste stängas, vilket innebär att först och sista punkt uppsättningar måste vara samma. [Punkterna i en polygon måste vara i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance` Returnerar avståndet i kilometer i Azure Search. Detta skiljer sig från andra tjänster som stöder OData geospatiala åtgärder, som vanligtvis returnera avstånd i meter.  

  > [!NOTE]  
  >  När du använder geo.distance i ett filter, måste du jämföra avståndet som returnerades av funktionen med ett konstant med `lt`, `le`, `gt`, eller `ge`. Operatorer `eq` och `ne` stöds inte när du jämför avstånd. Till exempel det här är en korrekt användning av geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

- Den `search.in` funktion testar om ett visst strängfält är lika med ett av en specifik lista med värden. Det kan också användas i någon eller samtliga för att jämföra ett värde av ett strängfält samling med en specifik lista med värden. Likheten mellan fälten och varje värde i listan bestäms på ett skiftlägeskänsligt sätt, på samma sätt som för den `eq` operator. Därför ett uttryck som `search.in(myfield, 'a, b, c')` motsvarar `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, förutom att `search.in` resulterar i mycket bättre prestanda. 

  Den första parametern för den `search.in` funktionen är fältreferensen sträng (eller en variabel för intervallet över ett strängfält samling i fall där `search.in` används inuti en `any` eller `all` uttryck). Den andra parametern är en sträng som innehåller listan med värden, avgränsade med blanksteg och/eller kommatecken. Om du vill använda avgränsarna än blanksteg och kommatecken dina värden innehåller de tecken du kan ange ett valfritt tredje parameter ska `search.in`. 

  Den här tredje parametern är en sträng där varje tecken i strängen eller delmängd av den här strängen behandlas som avgränsare vid parsning av listan över värden i den andra parametern.

  > [!NOTE]   
  > Vissa scenarier kräver att jämföra ett fält mot ett stort antal konstanta värden. Till exempel kan implementera säkerhetsoptimering med filter kräva att jämföra dokumentfältet-ID: T mot en lista med ID som den begärande användaren beviljas läsbehörighet. I scenarier som detta rekommenderar vi starkt med hjälp av den `search.in` funktionen i stället för en mer komplicerad disjunktion av likhet uttryck. Till exempel använda `search.in(Id, '123, 456, ...')` i stället för `Id eq 123 or Id eq 456 or ....`. 
  >
  > Om du använder `search.in`, du kan förvänta dig under en sekund svarstid när den andra parametern innehåller en lista över hundratals eller tusentals värden. Observera att det finns ingen explicit gräns för hur många objekt du kan skicka till `search.in`, även om du fortfarande är begränsad av tillåtna storleken. Svarstiden kommer dock att växa när antalet värden växer.

- Den `search.ismatch` evaluerar sökfråga som en del av ett filteruttryck. Dokument som matchar sökfrågan returneras i resultatet. Följande överlagringar av den här funktionen är tillgängliga:
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  Där: 
  
  - `search`: sökfrågan (antingen [enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) frågesyntax). 
  - `queryType`: ”enkla” eller ”full” standard ”Simple”. Anger vilka frågespråk som användes i den `search` parametern.
  - `searchFields`: kommaavgränsad lista över sökbara fält att söka i, som standard alla sökbara fält i indexet.    
  - `searchMode`: ”alla” eller ”alla” standard ”alla”. Anger om ett eller flera av sökvillkor måste matcha för att kunna räkna dokumentet som en matchning.

  Alla ovanstående parametrar är likvärdiga med motsvarande [Sök parametrarna](https://docs.microsoft.com/rest/api/searchservice/search-documents).

- Den `search.ismatchscoring` fungerar som den `search.ismatch` funktionen, returnerar true för dokument som matchar sökfrågan skickas som en parameter. Skillnaden mellan dem är att upplevelsen poäng dokument som matchar den `search.ismatchscoring` frågan kommer att bidra till den övergripande dokument poängen, medan i fallet med `search.ismatch`, dokumentet poängen ändras inte. Följande överlagringar av den här funktionen är tillgängliga med parametrar som är identiska med de av `search.ismatch`:
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  Den `search.ismatch` och `search.ismatchscoring` funktioner är fullständigt rätvinkliga med varandra och resten av filtret algebra. Det innebär att båda funktionerna kan användas i samma filteruttrycket. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geospatiala frågor och polygoner utsträckning 180th kartmeridian  
 För många geospatiala frågor bibliotek utformningen av en fråga som innehåller 180th kartmeridian (nära Datumgränsen) är antingen off-limits eller kräver en lösning, till exempel dela upp polygonen i två, en på endera sidan av kartmeridian.  

 I Azure Search, geospatiala frågor som innehåller 180 grader longitud fungerar som väntat om formen fråga är rektangulär och din koordinater justeras till en rutnätslayout längs longitud och latitud (till exempel `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). I annat fall du dela polygon-metoden för icke-rektangulär eller feljusterade former.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Storleksbegränsningar för filter 

 Det finns begränsningar för storlek och komplexitet filteruttryck som du kan skicka till Azure Search. Gränserna är ungefär utifrån antalet satser i filteruttrycket. En bra tumregel är att om du har hundratals satser du riskerar körs till gränsen. Vi rekommenderar att du designa programmet så att den inte genererar filter med obegränsad storlek.  


## <a name="filter-examples"></a>Filterexempel  

 Hitta alla hotels med en basavgift mindre än 200 USD som är klassificerade vid eller över 4:  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 Hitta alla hotell än ”Roach Motel” som har varit renoverade sedan 2010:  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Hitta alla hotell med en basavgift mindre än 200 USD som har varit renoverade sedan 2010 med en datetime-literalen som innehåller information om tidszonen för Pacific Standard Time:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 Hitta alla hotell som har parkeringssidans ingår och inte tillåter hälsan:  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- ELLER –  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Hitta alla hotell som är Lyxig eller inkludera parkering och har en klassificering på 5:  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Hitta alla hotels med taggen ”wifi” (där varje hotell har taggar som lagras i ett Collection(Edm.String) fält):  

```
$filter=tags/any(t: t eq 'wifi')
```

 Hitta alla hotels utan taggen ”motel”:  

```
$filter=tags/all(t: t ne 'motel')
```

 Hitta alla hotels med alla taggar:  

```
$filter=tags/any()
```

Hitta alla hotell som inte har taggar:  

```
$filter=not tags/any()
```


 Hitta alla hotels inom 10 kilometer för en viss referenspunkt (där platsen är ett fält av typen Edm.GeographyPoint):  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Hitta alla hotels inom en viss visningsområdet beskrivs som en polygon (där platsen är ett fält av typen Edm.GeographyPoint). Observera att polygonen är stängd (första och sista-mängder vara samma) och [punkterna måste anges i motsols ordning](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Hitta alla hotell som antingen inte har något värde i fältet ”Beskrivning”, eller att värdet uttryckligen anges till null:  

```
$filter=description eq null
```

Hitta alla hotels med namnet 'Roach motel ”eller” Budget hotell'). Fraser innehåller blanksteg, vilket är en standard-avgränsare. Om du vill ange en avgränsare åsidosättning omger du den nya avgränsaren med enkla citattecken som en del av filteruttryck:  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Hitta alla hotell med namn som är lika med antingen Roach motel ”eller” Budget hotell, avgränsade med ' |'):  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Hitta alla hotels med taggen ”wifi' eller 'pool”:  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Hitta en matchning på flera taggar, 'värmas upp handdukar rack' eller 'hairdryer ingår ”. Kom ihåg att ange ett alternativt avgränsare när utrymme saknas används fungerar då inte. 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

Hitta alla hotels utan taggen ”motel” eller ”utrustning”:  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Hitta dokument med ordet ”Hamnområde”. Den här filterfrågan är identisk med en [sökbegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) med `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Hitta dokument med ordet ”hostel” och omdöme större eller lika med 4 eller dokument med ordet ”motel” och omdöme lika med 5. Observera att denna begäran inte kan uttryckas utan den `search.ismatchscoring` funktion.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Hitta dokument utan ordet ”Lyxig”.

```
$filter=not search.ismatch('luxury') 
```

Hitta dokument med frasen ”ocean view” eller klassificering som motsvarar 5. Den `search.ismatchscoring` frågan körs bara mot fält `hotelName` och `description`.
Observera att dokument som matchade endast andra delen av disjunktion som ska returneras för – hotels med klassificering lika med 5. För att klargöra dokumenten matchar inte någon av de poängsatta delarna av uttrycket, kommer att returneras med poäng som är lika med noll.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Hitta dokument där villkoren ”hotell” och ”flygplats” är inom 5 ord från varandra i beskrivningen av hotellet och där hälsan tillåts inte. Den här frågan använder den [fullständig Lucene frågespråk](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Ordna efter-syntax

Den **$orderby** parametern accepterar en kommaavgränsad lista med upp till 32 uttryck i formatet `sort-criteria [asc|desc]`. Villkor för sortering kan antingen vara namnet på en `sortable` fält eller ett anrop till antingen den `geo.distance` eller `search.score` funktioner. Du kan använda antingen `asc` eller `desc` uttryckligen ange sorteringsordning. Är stigande ordning.

Om flera dokument har samma villkor för sortering och `search.score` funktionen används inte (till exempel om du sorterar efter ett numeriskt `rating` fält och tre dokument alla har en klassificering på 4), kommer att brytas ties dokumentet poäng i fallande ordning. När du poäng för dokumentet är desamma (till exempel när det finns ingen fulltextsökning-fråga som anges i begäran), sedan är relativa ordning bundet dokument obestämd.
 
Du kan ange flera villkor för sortering. Ordningen på uttryck anger sista sorteringsordning. Till exempel om du vill sortera fallande efter poäng, följt av klassificering, syntax skulle vara `$orderby=search.score() desc,rating desc`.

Syntaxen för `geo.distance` i **$orderby** är densamma som den tillhör **$filter**. När du använder `geo.distance` i **$orderby**, fältet som gäller måste vara av typen `Edm.GeographyPoint` och det måste också vara `sortable`.  

Syntaxen för `search.score` i **$orderby** är `search.score()`. Funktionen `search.score` inte tar några parametrar.  
 

## <a name="order-by-examples"></a>Sortering efter exempel

Sortera stigande efter baspriset hotels:

```
$orderby=baseRate asc
```

Sortera hotels fallande efter klassificering och sedan stigande efter baspriset (Kom ihåg att stigande är standard):

```
$orderby=rating desc,baseRate
```

Sortera fallande efter klassificering och sedan stigande efter avståndet från de angivna koordinaterna hotels:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Sortera hotell i fallande ordning genom att search.score och klassificering och sedan i stigande ordning efter avståndet från de angivna koordinaterna så att mellan två hotels med identiska betyg den närmaste som visas först:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>OData-syntax som inte stöds

-   Aritmetiska uttryck  

-   Functions (förutom avståndet och skär geospatiala funktioner)  

-   `any/all` med godtyckliga lambda-uttryck  

## <a name="see-also"></a>Se också  

+ [Aspektbaserad navigering i Azure Search](search-faceted-navigation.md) 
+ [Filter i Azure Search](search-filters.md) 
+ [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [Enkel frågesyntax i Azure Search](query-simple-syntax.md)   
