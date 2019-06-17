---
title: OData-referens för uttryckssyntax – Azure Search
description: Grammatik och syntax formella specifikation för OData-uttryck i Azure Search-frågor.
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
ms.openlocfilehash: cccfb749af07d1deeeda6e94de9c2cd5ce5396f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079670"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Referens för OData-uttryckssyntax för Azure Search

Använder Azure Search [OData uttryck](http://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) som parametrar i hela API: et. Oftast OData-uttryck används för den `$orderby` och `$filter` parametrar. Dessa uttryck kan vara komplexa, som innehåller flera satser, funktioner och operatorer. Men som även enkla OData-uttryck egenskapen sökvägar används i många delar av Azure Search REST API. Till exempel sökvägsuttryck används för att referera till underordnade fält av komplexa fält var som helst i API, till exempel när lista icke fält i en [förslagsställare](index-add-suggesters.md), ett [bedömning funktionen](index-add-scoring-profiles.md), `$select` parameter , eller till och med [fielded sökning i Lucene frågor](query-lucene-syntax.md).

Den här artikeln beskrivs alla dessa typer av OData-uttryck med hjälp av en formell grammatik. Det finns också en [interaktiva diagram](#syntax-diagram) för att visuellt utforska grammatik.

## <a name="formal-grammar"></a>Formella grammatik

Beskriver vi delmängd av OData-språk som stöds av Azure Search med hjälp av en EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) grammatik. Reglerna visas ”uppifrån och ned”, börjar med de mest komplexa uttryck och analysera dem till mer primitiva uttryck. Högst upp är grammatik som motsvarar specifika parametrar för Azure Search REST API:

- [`$filter`](search-query-odata-filter.md), definieras av den `filter_expression` regeln.
- [`$orderby`](search-query-odata-orderby.md), definieras av den `order_by_expression` regeln.
- [`$select`](search-query-odata-select.md), definieras av den `select_expression` regeln.
- Fältet sökvägarna som definieras av den `field_path` regeln. Fältet sökvägar används i API: et. De kan hänvisa till antingen översta fälten i ett index eller underordnade fält med en eller flera [komplexa fältet](search-howto-complex-data-types.md) överordnade element.

När EBNF är en bläddringsbar [syntaxdiagrammet](https://en.wikipedia.org/wiki/Syntax_diagram) som gör det möjligt att interaktivt utforska grammatik- och relationerna mellan reglerna.

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

## <a name="syntax-diagram"></a>Syntaxdiagrammet

Prova interaktiva syntaxdiagrammet för att visuellt utforska OData språk grammatik som stöds av Azure Search:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Se också  

- [Filter i Azure Search](search-filters.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene-frågesyntax](query-lucene-syntax.md)
- [Enkel frågesyntax i Azure Search](query-simple-syntax.md)
