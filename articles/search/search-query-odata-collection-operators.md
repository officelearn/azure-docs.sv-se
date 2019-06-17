---
title: OData-referens för operatorn av samling – Azure Search
description: OData-samling ansvariga för alla, och lambda-uttryck i Azure Search-frågor.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079943"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>OData-samling operatörer i Azure Search - `any` och `all`

När du skriver en [OData-filteruttryck](query-odata-filter-orderby-syntax.md) för att använda med Azure Search måste det vara bra att filtrera på samlingen fält. Du kan åstadkomma detta med hjälp av den `any` och `all` operatörer.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur för ett OData-uttryck som använder `any` eller `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Det finns tre typer av uttryck som filtrera samlingar.

- De första två iterera över en samling fält, tillämpa ett predikat som anges i form av ett lambda-uttryck för att varje element i samlingen.
  - Ett uttryck med hjälp av `all` returnerar `true` om predikatet är sant för varje element i samlingen.
  - Ett uttryck med hjälp av `any` returnerar `true` om predikatet är sant för minst ett element i mängden.
- Tredje form av samling filtrera använder `any` utan ett lambda-uttryck för att testa om en samling fältet är tomt. Om har alla element i samlingen, returnerar `true`. Om samlingen är tom, returneras `false`.

En **lambda-uttrycket** i en samling filter liknar brödtexten i en loop i ett programmeringsspråk. Den definierar en variabel, kallas de **intervallet variabeln**, som innehåller det aktuella elementet i mängden under iteration. Den definierar även en annan booleskt uttryck som filtervillkoren som ska tillämpas på intervallet variabeln för varje element i samlingen.

## <a name="examples"></a>Exempel

Matcha dokument vars `tags` fältet innehåller exakt strängen ”wifi”:

    tags/any(t: t eq 'wifi')

Matcha dokument där varje element i den `ratings` fält ingår mellan 3 och 5, inklusive:

    ratings/all(r: r ge 3 and r le 5)

Matcha dokument där någon av geografiskt koordinater i den `locations` fältet ligger inom den angivna polygonen:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Matcha dokument där den `rooms` fältet är tomt:

    not rooms/any()

Matcha dokument för alla rum i `rooms/amenities` fältet innehåller ”-tv” och `rooms/baseRate` är mindre än 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Begränsningar

Inte alla funktioner i filteruttryck finns inuti innehållet i ett lambda-uttryck. Begränsningarna varierar beroende på datatypen för fältet samling som du vill filtrera. I följande tabell sammanfattas begränsningarna.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Mer information om dessa begränsningar samt exempel finns i [felsökning samling filter i Azure Search](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför dessa begränsningar finns, finns i [förstå samling filter i Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
