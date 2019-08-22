---
title: Referens för OData-samlings operator – Azure Search
description: OData Collection-operatorer, any-och alla-och lambda-uttryck i Azure Search frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647640"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>OData Collection-operatörer i Azure Search `any` -och`all`

När du skriver ett [OData filter-uttryck](query-odata-filter-orderby-syntax.md) som ska användas med Azure Search är det ofta användbart att filtrera på samlings fält. Du kan åstadkomma detta med `any` operatorerna och. `all`

## <a name="syntax"></a>Syntax

Följande ebnf ([Extended backal-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken i ett OData-uttryck som använder `any` eller `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Se [syntax för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Det finns tre typer av uttryck som filtrerar samlingar.

- De två första itern över ett samlings fält och använder ett predikat som anges i form av ett lambda-uttryck för varje element i mängden.
  - Ett uttryck som `all` använder `true` returnerar om predikatet är sant för varje element i mängden.
  - Ett uttryck som `any` använder `true` returnerar om predikatet är sant för minst ett element i mängden.
- Den tredje formen av samlings filtret `any` använder utan lambda-uttryck för att testa om ett samlings fält är tomt. Om samlingen innehåller element returneras `true`. Om samlingen är tom returneras `false`.

Ett **lambda-uttryck** i ett samlings filter är som bröd texten i en slinga i ett programmeringsspråk. Den definierar en variabel, som kallas **variabeln Range**, som innehåller det aktuella elementet i samlingen under iteration. Den definierar också ett annat booleskt uttryck som är de filter villkor som ska tillämpas på variabeln Range för varje element i samlingen.

## <a name="examples"></a>Exempel

Matcha dokument vars `tags` fält innehåller exakt strängen "WiFi":

    tags/any(t: t eq 'wifi')

Matcha dokument där varje element i `ratings` fältet faller mellan 3 och 5, inklusive:

    ratings/all(r: r ge 3 and r le 5)

Matcha dokument där något av de geo-koordinaterna `locations` i fältet är inom den aktuella polygonen:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Matcha dokument där `rooms` fältet är tomt:

    not rooms/any()

Matcha dokument där för alla rum, `rooms/amenities` fältet innehåller "TV" och `rooms/baseRate` är mindre än 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Begränsningar

Alla funktioner i filter uttryck är inte tillgängliga i bröd texten i ett lambda-uttryck. Begränsningarna varierar beroende på data typen för det samlings fält som du vill filtrera. I följande tabell sammanfattas begränsningarna.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Mer information om dessa begränsningar och exempel finns i [Felsöka samlings filter i Azure Search](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför dessa begränsningar finns finns [i förstå samlings filter i Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
