---
title: Syntaxreferens för OData-uttryck
titleSuffix: Azure Cognitive Search
description: Formell grammatik- och syntaxspecifikation för OData-uttryck i Azure Cognitive Search-frågor.
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793238"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Syntaxreferens för OData-uttryck för Azure Cognitive Search

Azure Cognitive Search använder [OData-uttryck](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) som parametrar i hela API:et. Oftast används OData-uttryck för parametrar `$orderby` `$filter` och parametrar. Dessa uttryck kan vara komplexa och innehålla flera satser, funktioner och operatorer. Men även enkla OData-uttryck som egenskapssökvägar används i många delar av AZURE Cognitive Search REST API. Sökvägsuttryck används till exempel för att referera till underfält i komplexa fält överallt i API:et, `$select` till exempel när du listar underfält i en [förslagsman](index-add-suggesters.md), en [bedömningsfunktion](index-add-scoring-profiles.md), parametern eller till och med [fältsök i Lucene-frågor](query-lucene-syntax.md).

I den här artikeln beskrivs alla dessa former av OData-uttryck med hjälp av en formell grammatik. Det finns också ett [interaktivt diagram](#syntax-diagram) som hjälper visuellt utforska grammatiken.

## <a name="formal-grammar"></a>Formell grammatik

Vi kan beskriva delmängden av OData-språket som stöds av Azure Cognitive Search med hjälp av en EBNF-grammatik ([Extended Backus-Naur Form).](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) Regler visas "uppifrån och ned", som börjar med de mest komplexa uttrycken, och dela upp dem i mer primitiva uttryck. Högst upp finns de grammatikregler som motsvarar specifika parametrar för AZURE Cognitive Search REST API:

- [`$filter`](search-query-odata-filter.md), som `filter_expression` definieras av regeln.
- [`$orderby`](search-query-odata-orderby.md), som `order_by_expression` definieras av regeln.
- [`$select`](search-query-odata-select.md), som `select_expression` definieras av regeln.
- Fältsökvägar, definierade `field_path` av regeln. Fältsökvägar används i hela API:et. De kan referera till antingen fält på den högsta nivån i ett index eller underfält med en eller flera komplexa fältförfäder. [complex field](search-howto-complex-data-types.md)

Efter EBNF är ett browsable [syntaxdiagram](https://en.wikipedia.org/wiki/Syntax_diagram) som låter dig interaktivt utforska grammatiken och relationerna mellan dess regler.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Syntaxdiagram

Om du vill utforska OData-språk grammatiken som stöds av Azure Cognitive Search visuellt kan du prova det interaktiva syntaxdiagrammet:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Se även  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene-frågesyntax](query-lucene-syntax.md)
- [Enkel frågesyntax i Azure Cognitive Search](query-simple-syntax.md)
