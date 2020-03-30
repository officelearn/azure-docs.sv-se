---
title: OData-samlingsoperatörsreferens
titleSuffix: Azure Cognitive Search
description: När du skapar filteruttryck i Azure Cognitive Search-frågor använder du operatorer "any" och "alla" i lambda-uttryck när filtret finns i ett samlingsfält eller komplext samlingsfält.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113229"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData-samlingsoperatörer i Azure `any` Cognitive Search – och`all`

När du skriver ett [OData-filteruttryck](query-odata-filter-orderby-syntax.md) som ska användas med Azure Cognitive Search är det ofta användbart att filtrera i samlingsfält. Du kan uppnå `any` detta `all` med hjälp av och operatörer.

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken `any` `all`för ett OData-uttryck som använder eller .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Det finns tre uttrycksformer som filtrerar samlingar.

- De två första iterera över ett samlingsfält, tillämpa ett predikat som ges i form av en lambda uttryck till varje del av samlingen.
  - Ett uttryck `all` `true` som använder returnerar om predikatet är sant för varje element i samlingen.
  - Ett uttryck `any` `true` som använder returnerar om predikatet är sant för minst ett element i samlingen.
- Den tredje formen av `any` samlingsfilter använder utan lambda-uttryck för att testa om ett samlingsfält är tomt. Om samlingen har några element `true`returneras den . Om samlingen är tom `false`returneras den .

Ett **lambda-uttryck** i ett samlingsfilter är som kroppen av en slinga i ett programmeringsspråk. Den definierar en variabel, som kallas **intervallvariabeln**, som innehåller det aktuella elementet i samlingen under iteration. Det definierar också ett annat booleskt uttryck som är de filtervillkor som ska tillämpas på intervallvariabeln för varje element i samlingen.

## <a name="examples"></a>Exempel

Matcha dokument `tags` vars fält innehåller exakt strängen "wifi":

    tags/any(t: t eq 'wifi')

Matcha dokument där varje `ratings` del av fältet ligger mellan 3 och 5, inklusive:

    ratings/all(r: r ge 3 and r le 5)

Matcha dokument där någon av geokoordinaterna `locations` i fältet ligger inom den angivna polygonen:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Matcha dokument `rooms` där fältet är tomt:

    not rooms/any()

Matcha dokument där för `rooms/amenities` alla rum, fältet `rooms/baseRate` innehåller "TV" och är mindre än 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Begränsningar

Inte alla funktioner i filteruttryck är tillgängliga inuti kroppen av en lambda uttryck. Begränsningarna varierar beroende på datatypen för det insamlingsfält som du vill filtrera. I följande tabell sammanfattas begränsningarna.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Mer information om dessa begränsningar samt exempel finns [i Felsöka samlingsfilter i Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför dessa begränsningar finns finns i [Förstå samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
