---
title: OData jämförelse operatör referens
titleSuffix: Azure Cognitive Search
description: Syntax- och referensdokumentation för att använda OData-jämförelseoperatorer (eq, ne, gt, lt, ge och le) i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113220"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData-jämförelseoperatorer i Azure `eq` `ne`Cognitive `gt` `lt`Search `ge`- , , , och`le`

Den mest grundläggande åtgärden i ett [OData-filteruttryck](query-odata-filter-orderby-syntax.md) i Azure Cognitive Search är att jämföra ett fält med ett visst värde. Två typer av jämförelser är möjliga - jämlikhet jämförelse, och intervall jämförelse. Du kan använda följande operatorer för att jämföra ett fält med ett konstant värde:

Jämställdhetsaktörer:

- `eq`: Testa om ett fält är **lika med** ett konstant värde
- `ne`: Testa om ett fält inte är **lika med** ett konstant värde

Range operatörer:

- `gt`: Testa om ett fält är **större än** ett konstant värde
- `lt`: Testa om ett fält är **mindre än** ett konstant värde
- `ge`: Testa om ett fält är **större än eller lika med** ett konstant värde
- `le`: Testa om ett fält är **mindre än eller lika med** ett konstant värde

Du kan använda intervalloperatorerna i kombination med de [logiska operatorerna](search-query-odata-logical-operators.md) för att testa om ett fält ligger inom ett visst värdeintervall. Se [exemplen](#examples) senare i den här artikeln.

> [!NOTE]
> Om du vill kan du placera konstantvärdet till vänster om operatorn och fältnamnet på höger sida. För intervalloperatorer återförs jämförelsens innebörd. Om konstantvärdet till exempel finns till `gt` vänster testar du om konstantvärdet är större än fältet. Du kan också använda jämförelseoperatorerna för att jämföra `geo.distance`resultatet av en funktion, till exempel med ett värde. För booleska `search.ismatch`funktioner som att `true` `false` jämföra resultatet med eller är valfritt.

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för ett OData-uttryck som använder jämförelseoperatorerna.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Det finns två former av jämförelseuttryck. Den enda skillnaden mellan dem är om konstanten visas på vänster- eller höger sida av operatören. Uttrycket på andra sidan av operatorn måste vara en **variabel** eller ett funktionsanrop. En variabel kan vara antingen ett fältnamn eller en intervallvariabel när det gäller ett [lambda-uttryck](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Datatyper för jämförelser

Datatyperna på båda sidor av en jämförelseoperator måste vara kompatibla. Om till exempel den vänstra sidan `Edm.DateTimeOffset`är ett fält av typen måste den högra sidan vara en datumkonstant. Numeriska datatyper är mer flexibla. Du kan jämföra variabler och funktioner av valfri numerisk typ med konstanter av någon annan numerisk typ, med några begränsningar, enligt beskrivningen i följande tabell.

| Variabel- eller funktionstyp | Konstant värdetyp | Begränsningar |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Jämförelsen omfattas av [särskilda regler för `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstanten konverteras `Edm.Double`till , vilket resulterar i en förlust av precision för värden av stor magnitud |
| `Edm.Double` | `Edm.Int32` | Saknas |
| `Edm.Int64` | `Edm.Double` | Jämförelser med `NaN` `-INF`, `INF` eller är inte tillåtna |
| `Edm.Int64` | `Edm.Int64` | Saknas |
| `Edm.Int64` | `Edm.Int32` | Konstant konverteras `Edm.Int64` till före jämförelse |
| `Edm.Int32` | `Edm.Double` | Jämförelser med `NaN` `-INF`, `INF` eller är inte tillåtna |
| `Edm.Int32` | `Edm.Int64` | Saknas |
| `Edm.Int32` | `Edm.Int32` | Saknas |

För jämförelser som inte är tillåtna, till `Edm.Int64` `NaN`exempel att jämföra ett fält av typ med , returnerar AZURE Cognitive Search REST API ett "HTTP 400: Bad Request"-fel.

> [!IMPORTANT]
> Även om numeriska typjämnlagningar är flexibla rekommenderar vi starkt att du skriver jämförelser i filter så att konstantvärdet är av samma datatyp som variabeln eller funktionen som den jämförs med. Detta är särskilt viktigt när du blandar flyttals- och heltalsvärden, där implicita konverteringar som förlorar precision är möjliga.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Särskilda fall `null` för och`NaN`

När du använder jämförelseoperatorer är det viktigt att komma ihåg att alla `null`fält som inte är samling i Azure Cognitive Search potentiellt kan vara . I följande tabell visas alla möjliga resultat för ett `null`jämförelseuttryck där båda sidor kan vara:

| Operator | Resultat när endast fältet eller variabeln är`null` | Resultat när endast konstanten är`null` | Resultat när både fältet eller variabeln och konstanten`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Fel på felaktig begäran | HTTP 400: Fel på felaktig begäran |
| `lt` | `false` | HTTP 400: Fel på felaktig begäran | HTTP 400: Fel på felaktig begäran |
| `ge` | `false` | HTTP 400: Fel på felaktig begäran | HTTP 400: Fel på felaktig begäran |
| `le` | `false` | HTTP 400: Fel på felaktig begäran | HTTP 400: Fel på felaktig begäran |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Sammanfattningsvis `null` är lika med sig själv och är inte mindre eller större än något annat värde.

Om ditt index har `Edm.Double` fält `NaN` av typ och du överför värden till dessa fält måste du ta hänsyn till det när du skriver filter. Azure Cognitive Search implementerar IEEE 754-standarden för hantering av `NaN` värden och jämförelser med sådana värden ger icke-uppenbara resultat, vilket visas i följande tabell.

| Operator | Resultat när minst en operand är`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Sammanfattningsvis `NaN` är inte lika med något värde, inklusive sig själv.

### <a name="comparing-geo-spatial-data"></a>Jämföra geospatisiska data

Du kan inte direkt jämföra `Edm.GeographyPoint` ett fält av typen med `geo.distance` ett konstant värde, men du kan använda funktionen. Den här funktionen returnerar ett värde av typen `Edm.Double`, så att du kan jämföra det med en numerisk konstant för att filtrera baserat på avståndet från konstanta geospatialska koordinater. Se [exemplen](#examples) nedan.

### <a name="comparing-string-data"></a>Jämföra strängdata

Strängar kan jämföras i filter för `eq` `ne` exakta matchningar med operatorerna och. Dessa jämförelser är skiftlägeskänsliga.

## <a name="examples"></a>Exempel

Matcha dokument `Rating` där fältet är mellan 3 och 5, inklusive:

    Rating ge 3 and Rating le 5

Matcha dokument `Location` där fältet är mindre än 2 kilometer från den angivna latitud och longitud:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Matcha dokument `LastRenovationDate` där fältet är större än eller lika med den 1 januari 2015, midnatt UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Matcha dokument `Details/Sku` där fältet `null`inte är:

    Details/Sku ne null

Matcha dokument för hotell där minst ett rum har typen "Deluxe Room", där strängen på `Rooms/Type` fältet matchar filtret exakt:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
