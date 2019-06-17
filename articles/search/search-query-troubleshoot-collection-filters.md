---
title: Felsökning av OData-samling filter – Azure Search
description: Felsökning av OData-samling filterfel i Azure Search-frågor.
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079631"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Felsökning av OData-samling filter i Azure Search

Att [filter](query-odata-filter-orderby-syntax.md) på samlingen fält i Azure Search kan du använda den [ `any` och `all` operatörer](search-query-odata-collection-operators.md) tillsammans med **lambda-uttryck**. En lambda-uttrycket är ett underordnade filter som tillämpas på varje element i en samling.

Inte alla funktioner i filteruttryck är tillgängligt i ett lambda-uttryck. Vilka funktioner som är tillgängliga varierar beroende på datatypen för fältet samling som du vill filtrera. Detta kan resultera i ett fel om du försöker använda en funktion i ett lambda-uttryck som inte stöds i den aktuella kontexten. Om du får sådana fel vid försök att skriva ett komplexa filter över samling fält, den här artikeln hjälper dig att felsöka problemet.

## <a name="common-collection-filter-errors"></a>Vanliga samling filterfel

I följande tabellen finns de felmeddelanden som kan uppstå när du försöker köra ett samlingsfilter. Dessa fel kan inträffa när du använder en funktion i filteruttryck som inte stöds i ett lambda-uttryck. Varje fel ger lite vägledning i hur du kan skriva om dina filter om du vill undvika fel. Tabellen innehåller också en länk till relevanta avsnitt av den här artikeln innehåller mer information om hur du undviker felet.

| Felmeddelande | Situation | Mer information finns i |
| --- | --- | --- |
| Funktionen 'ismatch' har inga parametrar som är bundet till variabeln intervallet ”. Bara kopplat fältet referenser stöds i lambda-uttryck ('valfritt' eller ”alla”). Ändra filtret så att funktionen 'ismatch' ligger utanför lambda-uttrycket och försök igen. | Med hjälp av `search.ismatch` eller `search.ismatchscoring` inuti ett lambda-uttryck | [Regler för att filtrera komplexa samlingar](#bkmk_complex) |
| Ogiltig lambda-uttryck. Hitta ett test för likhet eller ojämlikhet där motsatsen förväntades i ett lambda-uttryck som itererar över ett fält av typen Collection(Edm.String). Använd uttryck av formuläret 'x eq y' eller 'search.in(...)' för 'any'. För ”alla”, Använd uttryck av formuläret 'x ne y', 'inte (x eq y)' eller 'inte search.in(...)'. | Filtrera efter ett fält av typen `Collection(Edm.String)` | [Regler för att filtrera sträng samlingar](#bkmk_strings) |
| Ogiltig lambda-uttryck. Hitta ett formulär som inte stöds av komplexa booleskt uttryck. Använd uttryck som är ”ORs av ANDs”, även kallat Disjunktivt Normal formuläret för ”alla”. Till exempel: '(a and b) eller (c och d), där a, b, c och d är jämförelse eller likhet underuttryck. För ”alla”, Använd uttryck som är ”ANDs av ORs”, även kallat Conjunctive Normal formuläret. Till exempel: '(a or b) och (c eller d), där a, b, c och d är jämförelse eller ojämlikhet underuttryck. Exempel på jämförelseuttryck: ' x gt 5', ' x le 2. Exempel på ett uttryck för likhet: ' x eq 5'. Exempel på ett ojämlikhet-uttryck: ' x ne 5'. | Filtrera efter fält av typen `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, eller `Collection(Edm.Int64)` | [Regler för att filtrera jämförbara samlingar](#bkmk_comparables) |
| Ogiltig lambda-uttryck. Hitta en användande av geo.distance() eller geo.intersects() i ett lambda-uttryck som itererar över ett fält av typen Collection(Edm.GeographyPoint). Kontrollera att du jämför geo.distance() använda operatorerna 'lt' eller 'le ”och se till att all användning av geo.intersects() inte negated för” alla ”. För ”alla”, kontrollera att du jämför geo.distance() använda operatorerna 'gt' eller 'ge ”och se till att all användning av geo.intersects() är negated. | Filtrera efter ett fält av typen `Collection(Edm.GeographyPoint)` | [Regler för att filtrera GeographyPoint samlingar](#bkmk_geopoints) |
| Ogiltig lambda-uttryck. Komplexa booleskt uttryck stöds inte i lambda-uttryck som kan itererar över fält av typen Collection(Edm.GeographyPoint). För ”alla” Häng underuttryck med 'eller'; ”och” stöds inte. För ”alla”, Häng underuttryck med 'och'; 'eller' stöds inte. | Filtrera efter fält av typen `Collection(Edm.String)` eller `Collection(Edm.GeographyPoint)` | [Regler för att filtrera sträng samlingar](#bkmk_strings) <br/><br/> [Regler för att filtrera GeographyPoint samlingar](#bkmk_geopoints) |
| Ogiltig lambda-uttryck. Hitta en jämförelseoperator (en av 'lt', 'le', 'gt' eller 'ge ”). Endast likhet operatörer är tillåtna i lambda-uttryck som kan itererar över fält av typen Collection(Edm.String). Använd uttryck i formatet ”x eq y” för ”alla”. För ”alla”, Använd uttryck i formatet 'x ne y' eller 'inte (x eq y) ”. | Filtrera efter ett fält av typen `Collection(Edm.String)` | [Regler för att filtrera sträng samlingar](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Hur du skriver giltig samling filter

Regler för att skriva giltig samling filter är olika för varje datatyp. I följande avsnitt beskrivs reglerna genom att visa exempel på vilka filter funktioner som stöds och som inte är:

- [Regler för att filtrera sträng samlingar](#bkmk_strings)
- [Regler för att filtrera booleskt samlingar](#bkmk_bools)
- [Regler för att filtrera GeographyPoint samlingar](#bkmk_geopoints)
- [Regler för att filtrera jämförbara samlingar](#bkmk_comparables)
- [Regler för att filtrera komplexa samlingar](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regler för att filtrera sträng samlingar

Inuti lambda-uttryck för sträng-samlingar, bara jämförelseoperatorerna som kan användas är `eq` och `ne`.

> [!NOTE]
> Azure Search stöder inte den `lt` / `le` / `gt` / `ge` operatörer för strängar, om du i eller utanför ett lambda-uttryck.

Brödtexten i en `any` kan bara testa likhet när innehållet i en `all` kan bara testa olikhet.

Det är också möjligt att kombinera flera uttryck via `or` i brödtexten i en `any`, och via `and` i brödtexten i en `all`. Eftersom den `search.in` funktionen motsvarar kombinera likhet kontroller med `or`, det är också tillåtet i brödtexten i en `any`. Däremot `not search.in` tillåts i brödtexten i en `all`.

Till exempel tillåts dessa uttryck:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

även om dessa uttryck inte tillåts:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regler för att filtrera booleskt samlingar

Typen `Edm.Boolean` stöder endast den `eq` och `ne` operatörer. Det innebär det inte så mycket att kombinera dessa satser som kontrollerar samma intervall variabel med `and` / `or` eftersom som alltid skulle leda till tautologies eller motsägelser.

Här följer några exempel på filter på booleskt samlingar som tillåts:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Till skillnad från strängen samlingar, booleskt samlingar med direktregler utan gränser som du kan använda operatorn i vilken typ av lambda-uttryck. Båda `eq` och `ne` kan användas i brödtexten i `any` eller `all`.

Uttryck, till exempel följande tillåts inte för booleska samlingar:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regler för att filtrera GeographyPoint samlingar

Värden av typen `Edm.GeographyPoint` i en samling kan jämföras direkt med varandra. I stället de måste användas som parametrar till den `geo.distance` och `geo.intersects` funktioner. Den `geo.distance` funktion i sin tur måste jämföras med ett värde för avståndet med hjälp av en jämförelseoperator som `lt`, `le`, `gt`, eller `ge`. Dessa regler gäller även för icke-samling Edm.GeographyPoint fält.

Som sträng samlingar `Edm.GeographyPoint` samlingar har vissa regler för hur geospatiala funktioner kan användas och kombineras i de olika typerna av lambda-uttryck:

- Vilka jämförelseoperatorer som du kan använda med den `geo.distance` funktion beror på vilken typ av lambda-uttryck. För `any`, du kan bara använda `lt` eller `le`. För `all`, du kan bara använda `gt` eller `ge`. Du kan negera uttryck som rör `geo.distance`, men du måste ändra jämförelseoperatorn (`geo.distance(...) lt x` blir `not (geo.distance(...) ge x)` och `geo.distance(...) le x` blir `not (geo.distance(...) gt x)`).
- I brödtexten i en `all`, `geo.intersects` funktion måste negeras. Däremot i brödtexten i en `any`, `geo.intersects` funktion måste inte negeras.
- I brödtexten i en `any`, geospatial uttryck kan kombineras med `or`. I brödtexten i en `all`, sådana uttryck kan kombineras med `and`.

Ovanstående begränsningar finns för liknande orsaker som likhet/olikhet begränsningen på samlingar som sträng. Se [förstå OData samling filter i Azure Search](search-query-understand-collection-filters.md) för en närmare titt på dessa skäl.

Här följer några exempel på filter på `Edm.GeographyPoint` samlingar som tillåts:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Uttryck, till exempel följande tillåts inte för `Edm.GeographyPoint` samlingar:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regler för att filtrera jämförbara samlingar

Det här avsnittet gäller för alla följande datatyper:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Datatyper som `Edm.Int32` och `Edm.DateTimeOffset` stöder alla sex jämförelseoperator: `eq`, `ne`, `lt`, `le`, `gt`, och `ge`. Lambda-uttryck över samlingar av dessa typer kan innehålla enkla uttryck med hjälp av någon av de här operatorerna. Detta gäller för både `any` och `all`. Till exempel tillåts de här filtren:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Men finns det begränsningar för hur sådana jämförelseuttryck kan kombineras till mer komplexa uttryck i ett lambda-uttryck:

- Regler för `any`:
  - Enkel ojämlikhet uttryck kan inte kombineras med andra uttryck fördel. Till exempel tillåts det här uttrycket:
    - `ratings/any(r: r ne 5)`

    men inte det här uttrycket:
    - `ratings/any(r: r ne 5 and r gt 2)`

    och även om det här uttrycket tillåts, det är inte användbart eftersom villkor som överlappar varandra:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Enkel jämförelseuttryck som rör `eq`, `lt`, `le`, `gt`, eller `ge` kan kombineras med `and` / `or`. Exempel:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Jämförelseuttryck kombineras med `and` (konjunktioner) kan kombineras ytterligare med `or`. Det här formuläret är känd i boolesk logik som ”[Disjunktivt Normal formuläret](https://en.wikipedia.org/wiki/Disjunctive_normal_form)” (DNF). Exempel:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regler för `all`:
  - Enkel likhet uttryck kan inte kombineras med andra uttryck fördel. Till exempel tillåts det här uttrycket:
    - `ratings/all(r: r eq 5)`

    men inte det här uttrycket:
    - `ratings/all(r: r eq 5 or r le 2)`

    och även om det här uttrycket tillåts, det är inte användbart eftersom villkor som överlappar varandra:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Enkel jämförelseuttryck som rör `ne`, `lt`, `le`, `gt`, eller `ge` kan kombineras med `and` / `or`. Exempel:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Jämförelseuttryck kombineras med `or` (disjunctions) kan kombineras ytterligare med `and`. Det här formuläret är känd i boolesk logik som ”[Conjunctive Normal formuläret](https://en.wikipedia.org/wiki/Conjunctive_normal_form)” (CNF). Exempel:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regler för att filtrera komplexa samlingar

Lambda-uttryck över komplexa samlingar hantera en syntax som är mycket mer flexibel än lambda-uttryck i samlingar av primitiva typer. Du kan använda alla filter-konstruktion i sådana ett lambda-uttryck som du kan använda utanför en med endast två undantag.

Funktionerna First, de `search.ismatch` och `search.ismatchscoring` stöds inte i lambda-uttryck. Mer information finns i [förstå OData samling filter i Azure Search](search-query-understand-collection-filters.md).

Andra refererar till fält som inte är *bunden* till variabeln intervall (så kallade *kostnadsfria variabler*) är inte tillåten. Titta exempelvis på följande två motsvarande OData-filteruttryck:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Det första uttrycket som ska tillåtas, medan andra formuläret kommer att avvisas eftersom `details/margin` är inte bunden till variabeln intervallet `s`.

Den här regeln utökar också för uttryck som har variabler bunden i en yttre omfattning. Dessa variabler är kostnadsfria avseende omfattningen som de visas. Till exempel det första uttrycket tillåts, medan andra motsvarande uttrycket inte är tillåten eftersom `s/name` är kostnadsfritt med avseende på omfånget för intervallet variabeln `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Den här begränsningen bör inte vara ett problem i praktiken eftersom det alltid går att konstruera filter så att lambda-uttryck innehåller endast bundna variabler.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Facit för filterregler för samlingen

I följande tabell sammanfattas reglerna för att konstruera giltiga filter för varje samlingsdatatyp.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Exempel på hur du skapar giltiga filter för varje scenario finns [hur du skriver giltig samling filter](#bkmk_examples).

Om du skrivfilter ofta och en förståelse för reglerna från första principer hjälper dig att se mer än bara memorera dem, [förstå OData samling filter i Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Förstå OData samling filter i Azure Search](search-query-understand-collection-filters.md)
- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
