---
title: OData-referens för operatorn av jämförelse – Azure Search
description: OData-jämförelseoperatorerna eq, ne, gt, lt, ge och le i Azure Search-frågor.
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079930"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>OData-jämförelseoperatorer i Azure Search - `eq`, `ne`, `gt`, `lt`, `ge`, och `le`

Den mest grundläggande åtgärden i en [OData-filteruttryck](query-odata-filter-orderby-syntax.md) i Azure Search är att jämföra ett fält till ett givet värde. Två typer av jämförelse är möjliga--likhetsjämförelse och intervallet jämförelse. Du kan använda följande operatorer för att jämföra ett fält till ett konstant värde:

Likhetsoperatorer:

- `eq`: Testa om ett fält är **lika** ett konstant värde
- `ne`: Testa om ett fält är **inte lika med** ett konstant värde

Range-operatorer:

- `gt`: Testa om ett fält är **är större än** ett konstant värde
- `lt`: Testa om ett fält är **mindre än** ett konstant värde
- `ge`: Testa om ett fält är **större än eller lika med** ett konstant värde
- `le`: Testa om ett fält är **mindre än eller lika med** ett konstant värde

Du kan använda range-operatorer i kombination med den [logiska operatorer](search-query-odata-logical-operators.md) att testa om ett fält är inom ett visst intervall av värden. Se den [exempel](#examples) senare i den här artikeln.

> [!NOTE]
> Om du vill kan placera du det konstanta värdet på vänster sida av operatorn och fältnamn på höger sida. Betydelsen av jämförelsen är omvänd för range-operatorer. Om det konstanta värdet är till vänster, till exempel `gt` skulle testa om det konstanta värdet är större än fältet. Du kan också använda jämförelseoperatorerna för att jämföra resultatet av en funktion som `geo.distance`, med ett värde. För boolesk funktioner som t.ex `search.ismatch`, att jämföra resultatet ska `true` eller `false` är valfritt.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur för ett OData-uttryck som använder jämförelseoperatorerna.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Det finns två typer av jämförelseuttryck. Den enda skillnaden mellan dem är om konstanten ska visas på vänster eller right-hand-sida för operatorn. Uttrycket på den andra sidan för operatorn måste vara en **variabeln** eller ett funktionsanrop. En variabel kan vara antingen ett fältnamn eller en variabel för intervallet i fall med en [lambda-uttrycket](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Datatyper för jämförelser

Datatyper på båda sidorna av en jämförelseoperator måste vara kompatibla. Exempel: om till vänster är ett fält av typen `Edm.DateTimeOffset`, och sedan på höger sida måste vara en konstant för datum / tid. Numeriska datatyper är mer flexibla. Du kan jämföra variabler och de funktioner i alla numeriska typer med konstanter av andra numerisk typ med vissa begränsningar som beskrivs i följande tabell.

| Variabeln eller funktionen typ | Konstant värdetyp | Begränsningar |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Jämförelse är föremål för [särskilda `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstant konverteras till `Edm.Double`, vilket resulterar i förlust av precision för värden i stor omfattning |
| `Edm.Double` | `Edm.Int32` | Saknas |
| `Edm.Int64` | `Edm.Double` | Jämförelser med `NaN`, `-INF`, eller `INF` tillåts inte |
| `Edm.Int64` | `Edm.Int64` | Saknas |
| `Edm.Int64` | `Edm.Int32` | Konstant konverteras till `Edm.Int64` innan jämförelse |
| `Edm.Int32` | `Edm.Double` | Jämförelser med `NaN`, `-INF`, eller `INF` tillåts inte |
| `Edm.Int32` | `Edm.Int64` | Saknas |
| `Edm.Int32` | `Edm.Int32` | Saknas |

För jämförelser som inte är tillåtna, till exempel jämföra ett fält av typen `Edm.Int64` till `NaN`, Azure Search REST API returnerar ett ”HTTP 400: Felaktig begäran ”-fel.

> [!IMPORTANT]
> Även om numerisk typ jämförelser är flexibla, vi rekommenderar starkt att skriva jämförelser i filter så att det konstanta värdet är av samma datatyp som variabeln eller funktionen som det jämförs. Detta är särskilt viktigt när blanda med och integer-värden, där implicita konverteringar som sin precision är möjliga.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Särskilda fall för `null` och `NaN`

När du använder jämförelseoperatorer, är det viktigt att komma ihåg att alla icke-samlingen fält i Azure Search kan vara `null`. I följande tabell visas de möjliga resultaten för en jämförelseuttryck där endera sidan kan vara `null`:

| Operator | Resultatet när det fält eller variabeln är `null` | Resultatet när endast Ständiga `null` | Resultatet när fältet eller variabeln och Ständiga `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Felaktig begäran-fel | HTTP 400: Felaktig begäran-fel |
| `lt` | `false` | HTTP 400: Felaktig begäran-fel | HTTP 400: Felaktig begäran-fel |
| `ge` | `false` | HTTP 400: Felaktig begäran-fel | HTTP 400: Felaktig begäran-fel |
| `le` | `false` | HTTP 400: Felaktig begäran-fel | HTTP 400: Felaktig begäran-fel |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Sammanfattningsvis `null` är lika med själva och är inte mindre eller större än andra värdet.

Om ditt index har fält av typen `Edm.Double` och du ladda upp `NaN` värden till dessa fält, måste du redovisa som när du skriver filter. Azure Search implementerar 754 IEEE-standarden för hantering av `NaN` värden och jämförelser med sådana värden ge icke-uppenbara resultat som visas i följande tabell.

| Operator | Resultatet när minst en operand är `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Sammanfattningsvis `NaN` är inte lika med valfritt värde, inklusive själva.

### <a name="comparing-geo-spatial-data"></a>Jämföra geospatiala data

Du kan inte direkt jämföra ett fält av typen `Edm.GeographyPoint` med ett konstant värde, men du kan använda den `geo.distance` funktion. Den här funktionen returnerar ett värde av typen `Edm.Double`, så att du kan jämföra den med en numerisk konstant att filtrera utifrån avståndet från konstant geospatial koordinater. Se den [exempel](#examples) nedan.

### <a name="comparing-string-data"></a>Jämföra strängdata

Strängar kan jämföras i filter för exakta matchningar med hjälp av den `eq` och `ne` operatörer. Dessa jämförelser är skiftlägeskänsliga.

## <a name="examples"></a>Exempel

Matcha dokument där den `Rating` fältet är mellan 3 och 5, inklusive:

    Rating ge 3 and Rating le 5

Matcha dokument där den `Location` fältet är mindre än 2 kilometer från den givna latituden och longituden:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Matcha dokument där den `LastRenovationDate` fältet är större än eller lika med 1 januari 2015, midnatt UTC-tid:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Matcha dokument där den `Details/Sku` fältet är inte `null`:

    Details/Sku ne null

Matcha dokument efter hotell där minst en plats har typen ”Deluxe rummet”, där sträng med det `Rooms/Type` fältet matchar filtret som exakt:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
