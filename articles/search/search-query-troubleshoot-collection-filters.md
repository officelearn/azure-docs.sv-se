---
title: Felsöka OData-samlingsfilter
titleSuffix: Azure Cognitive Search
description: Lär dig metoder för att lösa filterfel för OData-samling i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113089"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Felsöka OData-samlingsfilter i Azure Cognitive Search

Om du vill [filtrera på](query-odata-filter-orderby-syntax.md) samlingsfält i Azure Cognitive Search kan du använda [ `any` operatorerna och `all` ](search-query-odata-collection-operators.md) tillsammans med **lambda-uttryck**. Ett lambda-uttryck är ett underfilter som används på varje element i en samling.

Inte alla funktioner i filteruttryck är tillgängliga inuti ett lambda-uttryck. Vilka funktioner som är tillgängliga skiljer sig åt beroende på datatypen för det insamlingsfält som du vill filtrera. Detta kan resultera i ett fel om du försöker använda en funktion i ett lambda-uttryck som inte stöds i den kontexten. Om du stöter på sådana fel när du försöker skriva ett komplext filter över samlingsfält hjälper den här artikeln dig att felsöka problemet.

## <a name="common-collection-filter-errors"></a>Vanliga insamlingsfilterfel

I följande tabell visas fel som kan uppstå när du försöker köra ett samlingsfilter. Dessa fel inträffar när du använder en funktion med filteruttryck som inte stöds i ett lambda-uttryck. Varje fel ger viss vägledning om hur du kan skriva om filtret för att undvika felet. Tabellen innehåller också en länk till det relevanta avsnittet i den här artikeln som innehåller mer information om hur du undviker felet.

| Felmeddelande | Situationen | Mer information finns i |
| --- | --- | --- |
| Funktionen 'ismatch' har inga parametrar bundna till intervallvariabeln 's'. Endast bundna fältreferenser stöds inuti lambda-uttryck ("alla" eller "alla"). Ändra filtret så att funktionen "ismatch" ligger utanför lambda-uttrycket och försök igen. | Använda `search.ismatch` `search.ismatchscoring` eller inuti ett lambda-uttryck | [Regler för filtrering av komplexa samlingar](#bkmk_complex) |
| Ogiltigt lambda-uttryck. Hittade ett test för jämlikhet eller ojämlikhet där motsatsen förväntades i ett lambda-uttryck som itererar över ett fält av typen Collection(Edm.String). För "alla" ska du använda uttryck för formuläret "x eq y" eller "search.in(...)". För "alla" ska du använda uttryck för formuläret "x ne y", "inte (x eq y)" eller "inte search.in(...)". | Filtrering på ett fält av typ`Collection(Edm.String)` | [Regler för filtrering av strängsamlingar](#bkmk_strings) |
| Ogiltigt lambda-uttryck. Hittade en form av komplext booleskt uttryck som inte stöds. För "alla" kan du använda uttryck som är "ORs of ANDs", även känd som Disjunctive Normal Form. Till exempel: "a och b) eller (c och d)" där a, b, c och d är deluttryck för jämförelser eller jämlikhet. För alla, använd uttryck som är "ANDs of ORs", även känd som Konjunktiv normal form. Till exempel: "a eller b) och (c eller d)" där a, b, c och d är deluttryck för jämförelse eller ojämlikhet. Exempel på jämförelseuttryck: "x gt 5", "x le 2". Exempel på ett jämställdhetsuttryck: "x eq 5". Exempel på ett ojämlikhetsuttryck: "x ne 5". | Filtrering på `Collection(Edm.DateTimeOffset)`fält `Collection(Edm.Double)` `Collection(Edm.Int32)`av typen , , eller`Collection(Edm.Int64)` | [Regler för filtrering av jämförbara samlingar](#bkmk_comparables) |
| Ogiltigt lambda-uttryck. Hittade en användning av geo.distance() eller geo.intersects() som inte stöds och som itererar över ett fält av typen Samling(Edm.GeographyPoint). För "alla", se till att du jämför geo.distance() med hjälp av operatorerna "lt" eller "le" och se till att all användning av geo.intersects() inte förnekas. För alla ska du jämföra geo.distance() med operatorerna "gt" eller "ge" och se till att all användning av geo.intersects() är förnekad. | Filtrering på ett fält av typ`Collection(Edm.GeographyPoint)` | [Regler för filtrering av GeographyPoint-samlingar](#bkmk_geopoints) |
| Ogiltigt lambda-uttryck. Komplexa booleska uttryck stöds inte i lambda-uttryck som itererar över fält av typen Samling(Edm.GeographyPoint). För "alla", vänligen förena underuttryck med "eller"; "och" stöds inte. För "alla", vänligen förena underuttryck med "och"; "eller" stöds inte. | Filtrering på `Collection(Edm.String)` fält av typ eller`Collection(Edm.GeographyPoint)` | [Regler för filtrering av strängsamlingar](#bkmk_strings) <br/><br/> [Regler för filtrering av GeographyPoint-samlingar](#bkmk_geopoints) |
| Ogiltigt lambda-uttryck. Hittade en jämförelseoperatör (en av "lt", "le", "gt" eller "ge"). Endast likhetsoperatorer är tillåtna i lambda-uttryck som itererar över fält av typen Samling (Edm.String). För "alla", använd uttryck för formuläret "x eq y". För "alla", använd uttryck för formuläret "x ne y" eller "inte (x eq y)". | Filtrering på ett fält av typ`Collection(Edm.String)` | [Regler för filtrering av strängsamlingar](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Så här skriver du giltiga samlingsfilter

Reglerna för att skriva giltiga insamlingsfilter är olika för varje datatyp. I följande avsnitt beskrivs reglerna genom att exempel på vilka filterfunktioner som stöds och vilka som inte stöds:

- [Regler för filtrering av strängsamlingar](#bkmk_strings)
- [Regler för filtrering av booleska samlingar](#bkmk_bools)
- [Regler för filtrering av GeographyPoint-samlingar](#bkmk_geopoints)
- [Regler för filtrering av jämförbara samlingar](#bkmk_comparables)
- [Regler för filtrering av komplexa samlingar](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regler för filtrering av strängsamlingar

Inuti lambda uttryck för sträng samlingar, den enda jämförelse `eq` `ne`operatorer som kan användas är och .

> [!NOTE]
> Azure Cognitive Search stöder `lt` / `le` / `gt` / `ge` inte operatorerna för strängar, vare sig inuti eller utanför ett lambda-uttryck.

Kroppen av `any` en kan bara testa för `all` jämställdhet medan kroppen av en kan bara testa för ojämlikhet.

Det är också möjligt att kombinera `or` flera uttryck `any`via i `and` kroppen av `all`en , och via i kroppen av en . Eftersom `search.in` funktionen motsvarar att kombinera jämställdhetskontroller med `or`, är det `any`också tillåtet i kroppen av en . Omvänt är `not search.in` tillåtet i kroppen `all`av en .

Dessa uttryck är till exempel tillåtna:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

dessa uttryck är inte tillåtna:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regler för filtrering av booleska samlingar

Typen `Edm.Boolean` stöder endast `eq` `ne` operatorerna och. Som sådan, det är inte mycket meningsfullt att tillåta att kombinera sådana `and` / `or` klausuler som kontrollerar samma intervall variabel med eftersom det alltid skulle leda till tautologies eller motsägelser.

Här är några exempel på filter på booleska samlingar som är tillåtna:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Till skillnad från strängsamlingar har booleska samlingar inga gränser för vilken operatör som kan användas i vilken typ av lambda-uttryck. Båda `eq` `ne` och kan användas i `any` `all`kroppen av eller .

Uttryck som följande är inte tillåtna för booleska samlingar:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regler för filtrering av GeographyPoint-samlingar

Värden av `Edm.GeographyPoint` typen i en samling kan inte jämföras direkt med varandra. I stället måste de användas `geo.distance` som `geo.intersects` parametrar för funktionerna och. Funktionen `geo.distance` i sin tur måste jämföras med ett avståndsvärde `gt`med `ge`hjälp av en av jämförelseoperatorerna `lt`, `le`, eller . Dessa regler gäller även för edm.GeographyPoint-fält som inte samlas in.

Liksom strängsamlingar har `Edm.GeographyPoint` samlingar vissa regler för hur de geospatiala funktionerna kan användas och kombineras i de olika typerna av lambda-uttryck:

- Vilka jämförelseoperatorer du kan `geo.distance` använda med funktionen beror på vilken typ av lambda-uttryck. För `any`kan du `lt` bara `le`använda eller . För `all`kan du `gt` bara `ge`använda eller . Du kan förneka uttryck `geo.distance`som involverar, men du måste`geo.distance(...) lt x` `not (geo.distance(...) ge x)` ändra `geo.distance(...) le x` `not (geo.distance(...) gt x)`jämförelseoperatorn ( blir och blir ).
- I kroppen av `all`en `geo.intersects` måste funktionen förnekas. Omvänt, i kroppen av `any`en `geo.intersects` , får funktionen inte förnekas.
- I kroppen av `any`ett kan geo-rumsliga `or`uttryck kombineras med . I kroppen av `all`en kan sådana uttryck `and`kombineras med .

Ovanstående begränsningar finns av liknande skäl som begränsning av jämlikhet/ojämlikhet för strängsamlingar. Se [Förstå OData-samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md) för en djupare titt på dessa orsaker.

Här är några exempel `Edm.GeographyPoint` på filter för samlingar som är tillåtna:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Uttryck som följande är inte `Edm.GeographyPoint` tillåtna för samlingar:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regler för filtrering av jämförbara samlingar

Det här avsnittet gäller för alla följande datatyper:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typer som `Edm.Int32` `Edm.DateTimeOffset` och `eq`stöder alla sex jämförelseoperatorerna: `gt`, `ge` `ne`, `lt`, `le`, och . Lambda uttryck över samlingar av dessa typer kan innehålla enkla uttryck med någon av dessa operatorer. Detta gäller `any` både `all`och . Dessa filter är till exempel tillåtna:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Det finns dock begränsningar för hur sådana jämförelseuttryck kan kombineras till mer komplexa uttryck inuti ett lambda-uttryck:

- Regler `any`för :
  - Enkla ojämlikhetsuttryck kan inte kombineras med några andra uttryck på ett användbart sätt. Det här uttrycket är till exempel tillåtet:
    - `ratings/any(r: r ne 5)`

    men det här uttrycket är inte:
    - `ratings/any(r: r ne 5 and r gt 2)`

    Och även om det här uttrycket är tillåtet är det inte användbart eftersom villkoren överlappar:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Enkla jämförelseuttryck `eq` `lt`som `le` `gt`involverar `ge` , , `and` / `or`, eller kan kombineras med . Ett exempel:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Jämförelseuttryck i `and` kombination med (konjunktioner) kan kombineras ytterligare med . `or` Denna form är känd i boolesk logik som "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Ett exempel:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regler `all`för :
  - Enkla likhetsuttryck kan inte kombineras med andra uttryck på ett användbart sätt. Det här uttrycket är till exempel tillåtet:
    - `ratings/all(r: r eq 5)`

    men det här uttrycket är inte:
    - `ratings/all(r: r eq 5 or r le 2)`

    Och även om det här uttrycket är tillåtet är det inte användbart eftersom villkoren överlappar:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Enkla jämförelseuttryck `ne` `lt`som `le` `gt`involverar `ge` , , `and` / `or`, eller kan kombineras med . Ett exempel:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Jämförelseuttryck i `or` kombination med (disjunctions) `and`kan kombineras ytterligare med . Denna form är känd i boolesk logik som "[Konjunktiv normalform](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Ett exempel:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regler för filtrering av komplexa samlingar

Lambda uttryck över komplexa samlingar stöder en mycket mer flexibel syntax än lambda uttryck över samlingar av primitiva typer. Du kan använda valfri filterkonstruktion inuti ett sådant lambda-uttryck som du kan använda utanför ett, med bara två undantag.

För det `search.ismatch` första, funktionerna och `search.ismatchscoring` stöds inte inuti lambda uttryck. Mer information finns [i Förstå OData-samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md).

För det andra är det inte tillåtet att referera till fält som inte är *bundna* till intervallvariabeln (så kallade *fria variabler).* Tänk dig till exempel följande två likvärdiga OData-filteruttryck:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Det första uttrycket tillåts, medan det `details/margin` andra formuläret avvisas eftersom `s`det inte är bundet till intervallvariabeln .

Den här regeln omfattar även uttryck som har variabler bundna i ett yttre omfång. Sådana variabler är fria med avseende på det tillämpningsområde i vilket de förekommer. Det första uttrycket tillåts till exempel, medan det `s/name` andra motsvarande uttrycket inte tillåts `a`eftersom det är fritt med avseende på intervallvariabelns omfång:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Den här begränsningen bör inte vara ett problem i praktiken eftersom det alltid är möjligt att konstruera filter så att lambda-uttryck endast innehåller bundna variabler.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Fuska blad för regler insamling filter

I följande tabell sammanfattas reglerna för att konstruera giltiga filter för varje insamlingsdatatyp.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Exempel på hur du skapar giltiga filter för varje ärende finns i [Så här skriver du giltiga samlingsfilter](#bkmk_examples).

Om du skriver filter ofta och förstår reglerna från de första principerna skulle hjälpa dig mer än att bara memorera dem, se [Förstå OData-samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Förstå OData-samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md)
- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
