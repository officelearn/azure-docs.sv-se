---
title: OData språköversikt
titleSuffix: Azure Cognitive Search
description: OData-språköversikt för filter, val och order-by för Azure Cognitive Search-frågor.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153884"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>OData-språköversikt `$orderby`för `$select` `$filter`, och i Azure Cognitive Search

Azure Cognitive Search stöder en delmängd av OData-uttryckssyntaxen för **$filter,** **$orderby**och **$select** uttryck. Filteruttryck utvärderas under frågetolka, begränsa sökning till specifika fält eller lägga till matchningsvillkor som används vid indexsökningar. Order-by-uttryck används som ett efterbearbetningssteg över en resultatuppsättning för att sortera de dokument som returneras. Välj uttryck avgör vilka dokumentfält som ska ingå i resultatuppsättningen. Syntaxen för dessa uttryck skiljer sig från den [enkla](query-simple-syntax.md) eller [fullständiga](query-lucene-syntax.md) frågesyntax som används i sökparametern, även om det finns en viss överlappning i syntaxen för referensfält. **search**

Den här artikeln innehåller en översikt över OData-uttrycksspråket som används i filter, ordna efter och välja uttryck. Språket presenteras "nedifrån och upp", med början i de mest grundläggande elementen och bygger på dem. Syntaxen på den översta nivån för varje parameter beskrivs i en separat artikel:

- [$filter syntax](search-query-odata-filter.md)
- [$orderby syntax](search-query-odata-orderby.md)
- [$select syntax](search-query-odata-select.md)

OData-uttryck sträcker sig från enkla till mycket komplexa, men de har alla gemensamma element. De mest grundläggande delarna av ett OData-uttryck i Azure Cognitive Search är:

- **Fältsökvägar**som refererar till specifika fält i indexet.
- **Konstanter**, som är litterala värden av en viss datatyp.

> [!NOTE]
> Terminologin i Azure Cognitive Search skiljer sig från [OData-standarden](https://www.odata.org/documentation/) på några olika sätt. Det vi kallar ett **fält** i Azure Cognitive Search kallas **egenskap** i OData och på samma sätt för **fältsökväg** kontra **egenskapssökväg**. Ett **index** som innehåller **dokument** i Azure Cognitive Search refereras mer allmänt i OData som en **entitetsuppsättning** som innehåller **entiteter**. Azure Cognitive Search-terminologin används i den här referensen.

## <a name="field-paths"></a>Fältbanor

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för fältbanor.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

En fältsökväg består av en eller flera **identifierare** avgränsade med snedstreck. Varje identifierare är en sekvens av tecken som måste börja med en ASCII-bokstav eller understreck och som bara innehåller ASCII-bokstäver, siffror eller understreck. Bokstäverna kan vara versaler eller gemener.

En identifierare kan referera antingen till namnet på ett fält eller till en`any` `all` **områdesvariabel** i kontexten för ett [samlingsuttryck](search-query-odata-collection-operators.md) ( eller ) i ett filter. En intervallvariabel är som en loopvariabel som representerar det aktuella elementet i samlingen. För komplexa samlingar representerar variabeln ett objekt, vilket är anledningen till att du kan använda fältsökvägar för att referera till underfält i variabeln. Detta är analogt med punkt notation i många programmeringsspråk.

Exempel på fältsökvägar visas i följande tabell:

| Fältbana | Beskrivning |
| --- | --- |
| `HotelName` | Refererar till ett fält på den högsta nivån i indexet |
| `Address/City` | Refererar till `City` delfältet i ett komplext fält i indexet. `Address` är av `Edm.ComplexType` typen i det här exemplet |
| `Rooms/Type` | Refererar till `Type` delfältet i ett komplext samlingsfält i indexet. `Rooms` är av `Collection(Edm.ComplexType)` typen i det här exemplet |
| `Stores/Address/Country` | Refererar till `Country` delfältet i `Address` delfältet i ett komplext samlingsfält i indexet. `Stores` är av `Collection(Edm.ComplexType)` `Address` typen och `Edm.ComplexType` är av typen i det här exemplet |
| `room/Type` | Refererar till `Type` delfältet i `room` intervallvariabeln, till exempel i filteruttrycket`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refererar till `Country` delfältet i `Address` delfältet i `store` intervallvariabeln, till exempel i filteruttrycket`Stores/any(store: store/Address/Country eq 'Canada')` |

Innebörden av en fältbana skiljer sig åt beroende på sammanhanget. I filter refererar en fältsökväg till värdet för en *enskild förekomst* av ett fält i det aktuella dokumentet. I andra sammanhang, till exempel **$orderby**, **$select**eller i [fältsökning i den fullständiga Lucene-syntaxen,](query-lucene-syntax.md#bkmk_fields)refererar en fältsökväg till själva fältet. Den här skillnaden får vissa konsekvenser för hur du använder fältsökvägar i filter.

Tänk på `Address/City`fältsökvägen . I ett filter refererar detta till en enda stad för det aktuella dokumentet, till exempel "San Francisco". Däremot `Rooms/Type` hänvisar till `Type` delfältet för många rum (som "standard" för det första rummet, "deluxe" för det andra rummet, och så vidare). Eftersom `Rooms/Type` det inte refererar till en enda `Type` *instans* av underfältet kan den inte användas direkt i ett filter. Om du i stället vill filtrera efter rumstyp använder du ett [lambda-uttryck](search-query-odata-collection-operators.md) med en intervallvariabel, så här:

    Rooms/any(room: room/Type eq 'deluxe')

I det här exemplet `room` visas `room/Type` områdesvariabeln i fältsökvägen. På så `room/Type` sätt refererar du till typen av det aktuella rummet i det aktuella dokumentet. Detta är en enda `Type` instans av delfältet, så det kan användas direkt i filtret.

### <a name="using-field-paths"></a>Använda fältbanor

Fältsökvägar används i många parametrar för [AZURE Cognitive Search REST API:er](https://docs.microsoft.com/rest/api/searchservice/). I följande tabell visas alla platser där de kan användas, plus eventuella begränsningar för deras användning:

| API | Parameternamn | Begränsningar |
| --- | --- | --- |
| [Skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera](https://docs.microsoft.com/rest/api/searchservice/update-index) index | `suggesters/sourceFields` | Inget |
| [Skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera](https://docs.microsoft.com/rest/api/searchservice/update-index) index | `scoringProfiles/text/weights` | Kan bara referera till **sökbara** fält |
| [Skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera](https://docs.microsoft.com/rest/api/searchservice/update-index) index | `scoringProfiles/functions/fieldName` | Kan bara referera till **filterbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`när `queryType` är`full` | Kan bara referera till **sökbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Kan bara referera till **facetable** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Kan bara referera till **sökbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Kan bara referera till **sökbara** fält |
| [Föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) och [komplettera automatiskt](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Det går bara att referera till fält som ingår i en [förslagsman](index-add-suggesters.md) |
| [Sök,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions)och [komplettera automatiskt](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Kan bara referera till **filterbara** fält |
| [Sök](https://docs.microsoft.com/rest/api/searchservice/search-documents) och [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Kan bara referera till **sorterbara** fält |
| [Sök,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [föreslå](https://docs.microsoft.com/rest/api/searchservice/suggestions)och [uppslag](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Kan bara referera till **hämtningsbara** fält |

## <a name="constants"></a>Konstanter

Konstanter i OData är litterala värden för en viss EDM-typ [(Entity Data Model).](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) Se [Datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för en lista över typer som stöds i Azure Cognitive Search. Konstanter av samlingstyper stöds inte.

I följande tabell visas exempel på konstanter för var och en av de datatyper som stöds av Azure Cognitive Search:

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

### <a name="escaping-special-characters-in-string-constants"></a>Fly specialtecken i strängkonstanter

Strängkonstanter i OData avgränsas med enstaka citattecken. Om du behöver skapa en fråga med en strängkonstant som i sig kan innehålla enstaka citattecken kan du undkomma de inbäddade citaten genom att fördubbla dem.

Till exempel skulle en fras med en oformaterad apostrof som "Alice bil" representeras `'Alice''s car'`i OData som strängkonstant .

> [!IMPORTANT]
> När du konstruerar filter programmässigt är det viktigt att komma ihåg att komma ihåg att fly strängkonstanter som kommer från användarindata. Detta för att minska risken för [injektionsattacker](https://wikipedia.org/wiki/SQL_injection), särskilt när du använder filter för att implementera [säkerhetstrimning](search-security-trimming-for-azure-search.md).

### <a name="constants-syntax"></a>Syntaxen för konstanter

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för de flesta konstanter som visas i tabellen ovan. Grammatiken för geospatisiska typer finns i [OData geo-spatial funktioner i Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).

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

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Skapa uttryck från fältbanor och konstanter

Fältsökvägar och konstanter är den mest grundläggande delen av ett OData-uttryck, men de är redan själva fullständiga uttryck. Faktum är att **$select** parametern i Azure Cognitive Search är inget annat än en kommaavgränsad lista över fältsökvägar, och **$orderby** är inte mycket mer komplicerat än **$select**. Om du råkar ha ett `Edm.Boolean` fält av typen i indexet kan du till och med skriva ett filter som bara är sökvägen till det fältet. Konstanterna `true` och `false` är jämväl giltiga filtrerar.

Men för det mesta behöver du mer komplexa uttryck som refererar till mer än ett fält och konstant. Dessa uttryck är inbyggda på olika sätt beroende på parametern.

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för **parametrarna $filter,** **$orderby**och **$select.** Dessa är uppbyggda från enklare uttryck som refererar till fältbanor och konstanter:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Parametrarna **$orderby** och **$select** är båda kommaavgränsade listor med enklare uttryck. Parametern **$filter** är ett booleskt uttryck som består av enklare underuttryck. Dessa underuttryck kombineras med logiska operatorer som [ `and`, `or`och `not` ](search-query-odata-logical-operators.md), jämförelseoperatorer som [ `eq`, `lt`, `gt`och så vidare](search-query-odata-comparison-operators.md)och samlingsoperatorer som [ `any` och `all` ](search-query-odata-collection-operators.md).

Parametrarna **$filter**, **$orderby**och **$select** utforskas mer i detalj i följande artiklar:

- [OData $filter syntax i Azure Cognitive Search](search-query-odata-filter.md)
- [OData $orderby syntax i Azure Cognitive Search](search-query-odata-orderby.md)
- [OData $select syntax i Azure Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Se även  

- [Fasterad navigering i Azure Cognitive Search](search-faceted-navigation.md)
- [Filter i Azure Cognitive Search](search-filters.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene-frågesyntax](query-lucene-syntax.md)
- [Enkel frågesyntax i Azure Cognitive Search](query-simple-syntax.md)
