---
title: Översikt över OData språk – Azure Search
description: Översikt för OData-språk för filter, Välj och ordning av för Azure Search-frågor.
ms.date: 06/13/2019
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063707"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Översikt av OData-språk för `$filter`, `$orderby`, och `$select` i Azure Search

Azure Search har stöd för en delmängd av OData-uttryckssyntax för **$filter**, **$orderby**, och **$select** uttryck. Filteruttryck utvärderas under parsning, begränsa sökningen till specifika fält eller lägga till matchar de villkor som används vid index skanningar. Order by-uttryck tillämpas via en resultatmängd att sortera de dokument som returneras som ett efterbearbetning steg. Välj uttryck avgöra vilka dokumentfält som som ingår i resultatet. Syntaxen för dessa uttryck skiljer sig från den [enkel](query-simple-syntax.md) eller [fullständig](query-lucene-syntax.md) frågesyntax som används i den **search** parameter, även om det finns vissa överlappningar i syntaxen för refererar till fält.

Den här artikeln innehåller en översikt över OData-uttrycket-språk som används i filter, sortering efter och välj uttryck. Språket visas ”nedifrån och upp”, från och med de mest grundläggande delarna och bygga på dem. Översta syntaxen för varje parameter beskrivs i en separat artikel:

- [$filter syntax](search-query-odata-filter.md)
- [$orderby syntax](search-query-odata-orderby.md)
- [$select syntax](search-query-odata-select.md)

OData-uttryck allt från enkla till mycket komplexa, men de alla delar gemensamma element. De mest grundläggande delarna i en OData-uttrycket i Azure Search är:

- **Fältet sökvägar**, som refererar till specifika fält i ditt index.
- **Konstanter**, som är litterala värden av en viss datatyp.

> [!NOTE]
> Terminologi i Azure Search skiljer sig från den [OData-standarden](https://www.odata.org/documentation/) på flera sätt. Det vi kallar ett **fältet** i Azure Search kallas en **egenskapen** i OData och på samma sätt för **fältsökväg** jämfört med **egenskapssökväg**. En **index** som innehåller **dokument** i Azure Search avses vanligtvis i OData som en **entitetsuppsättning** som innehåller **entiteter**. Azure Search-termer används i den här referensen.

## <a name="field-paths"></a>Fältet sökvägar

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatik i fältet sökvägar.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

En fältsökväg består av en eller flera **identifierare** avgränsade med snedstreck. Varje identifierare är en sekvens av tecken som måste börja med ett ASCII-bokstäver eller understreck och innehålla endast ASCII-bokstäver, siffror eller understreck. Bokstäverna som kan vara övre- eller små.

En identifierare som kan hänvisa antingen till namnet på ett fält eller till en **intervallet variabeln** i samband med en [samling uttryck](search-query-odata-collection-operators.md) (`any` eller `all`) i ett filter. En variabel för intervallet är som en loop-variabel som representerar det aktuella elementet i samlingen. För komplexa samlingar representerar variabeln ett objekt, vilket är anledningen till att du kan använda fältet sökvägar för att referera till underordnade fält för variabeln. Detta motsvarar punktnotation i många programmeringsspråk.

I följande tabell visas exempel på fältet sökvägar:

| Fältsökväg | Beskrivning |
| --- | --- |
| `HotelName` | Refererar till ett högsta fält i indexet |
| `Address/City` | Refererar till den `City` icke fältet i ett komplext fält i indexet. `Address` är av typen `Edm.ComplexType` i det här exemplet |
| `Rooms/Type` | Refererar till den `Type` icke fältet i en komplex samling fält i indexet. `Rooms` är av typen `Collection(Edm.ComplexType)` i det här exemplet |
| `Stores/Address/Country` | Refererar till den `Country` icke fältet för den `Address` icke fältet i en komplex samling fält i indexet. `Stores` är av typen `Collection(Edm.ComplexType)` och `Address` är av typen `Edm.ComplexType` i det här exemplet |
| `room/Type` | Refererar till den `Type` icke fältet för den `room` intervallet variabel, till exempel i filteruttrycket `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refererar till den `Country` icke fältet för den `Address` icke fältet för den `store` intervallet variabel, till exempel i filteruttrycket `Stores/any(store: store/Address/Country eq 'Canada')` |

Enligt en fältsökväg skiljer sig beroende på kontext. I filter, en fältsökväg refererar till värdet för en *instans* i ett fält i dokumentet. I andra sammanhang som **$orderby**, **$select**, eller i [fielded Sök i den fullständiga Lucene-syntaxen](query-lucene-syntax.md#bkmk_fields), en fältsökväg refererar till fältet själva. Den här skillnaden har vissa följder för hur du använder fältet sökvägar i filter.

Överväg att fältsökväg `Address/City`. Ett filter avser en enda stad för det aktuella dokumentet som ”San Francisco”. Däremot `Rooms/Type` refererar till den `Type` icke fältet för många rum (till exempel ”standard” för den första rummet ”deluxe” för den andra utrymme och så vidare). Eftersom `Rooms/Type` inte refererar till en *instans* underordnade fältets `Type`, den kan inte användas direkt i ett filter. I stället för att filtrera på rummet typ, använder du en [lambda-uttrycket](search-query-odata-collection-operators.md) med en variabel i intervallet, så här:

    Rooms/any(room: room/Type eq 'deluxe')

I det här exemplet är intervallet variabeln `room` visas i den `room/Type` fältsökväg. På så sätt kan `room/Type` refererar till typ av den aktuella plats i det aktuella dokumentet. Det här är en instans av den `Type` underordnade fält, så att den kan användas direkt i filtret.

### <a name="using-field-paths"></a>Med hjälp av fältet sökvägar

Fältet sökvägar som används i många parametrar för den [API för Azure Search](https://docs.microsoft.com/rest/api/searchservice/). I följande tabell visas de platser där de kan användas, plus eventuella begränsningar på deras användning:

| API | Parameternamn | Begränsningar |
| --- | --- | --- |
| [Skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatering](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `suggesters/sourceFields` | Ingen |
| [Skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatering](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `scoringProfiles/text/weights` | Kan endast referera till **sökbara** fält |
| [Skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatering](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `scoringProfiles/functions/fieldName` | Kan endast referera till **filtrerbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` När `queryType` är `full` | Kan endast referera till **sökbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Kan endast referera till **fasettbar** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Kan endast referera till **sökbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Kan endast referera till **sökbara** fält |
| [Föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) och [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Kan endast referera till fält som är en del av en [förslagsställare](index-add-suggesters.md) |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents), [föreslår](https://docs.microsoft.com/rest/api/searchservice/suggestions), och [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Kan endast referera till **filtrerbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) och [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Kan endast referera till **sorterbar** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents), [föreslår](https://docs.microsoft.com/rest/api/searchservice/suggestions), och [sökning](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Kan endast referera till **hämtningsbar** fält |

## <a name="constants"></a>Konstanter

Konstanterna i OData är litterala värden av en viss [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM)-typen. Se [datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en lista över typer som stöds i Azure Search. Konstanterna samlingstyper stöds inte.

I följande tabell visas exempel på konstanter för var och en av de datatyper som stöds av Azure Search:

| Datatyp | Exempel konstanter |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur för de flesta av konstanterna som visas i tabellen ovan. Grammatik för geospatial typer finns i [OData geospatiala funktioner i Azure Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Bygga uttryck från fältet sökvägar och konstanter

Fältet sökvägar och konstanter är den mest grundläggande del av en OData-uttrycket, men de inte redan finns fullständig uttryck själva. I själva verket den **$select** parametern i Azure Search är något annat än en kommaavgränsad lista över fält sökvägar och **$orderby** inte mycket mer komplicerat än **$select**. Om du råkar ha ett fält av typen `Edm.Boolean` i ditt index kan du även skriva ett filter som är något annat än sökvägen till det fältet. Konstanterna `true` och `false` på samma sätt är giltiga filter.

Men behöver i de flesta fall du mer komplexa uttryck som refererar till flera fält och konstant. Dessa uttryck bygger på olika sätt beroende på parametern.

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur för den **$filter**, **$orderby**, och **$select** parametrar. Dessa byggs upp av enklare uttryck som refererar till fältet sökvägar och konstanter:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Den **$orderby** och **$select** parametrar är båda kommateckenavgränsade listor över enklare uttryck. Den **$filter** parametern är ett booleskt uttryck som består av enklare underordnade uttryck. De här underordnade uttryck kombineras med logiska operatorer som [ `and`, `or`, och `not` ](search-query-odata-logical-operators.md), jämförelseoperatorer som [ `eq`, `lt`, `gt`och så vidare](search-query-odata-comparison-operators.md), och samling operatörer som [ `any` och `all` ](search-query-odata-collection-operators.md).

Den **$filter**, **$orderby**, och **$select** parametrar beskrivs i detalj i följande artiklar:

- [OData $filter syntax i Azure Search](search-query-odata-filter.md)
- [OData $orderby syntax i Azure Search](search-query-odata-orderby.md)
- [OData $select syntax i Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Se också  

- [Aspektbaserad navigering i Azure Search](search-faceted-navigation.md)
- [Filter i Azure Search](search-filters.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene-frågesyntax](query-lucene-syntax.md)
- [Enkel frågesyntax i Azure Search](query-simple-syntax.md)
