---
title: OData väljer referens – Azure Search
description: Språkreferens för OData för Välj syntax i Azure Search-frågor.
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079683"
---
# <a name="odata-select-syntax-in-azure-search"></a>OData $select syntax i Azure Search

 Du kan använda den [OData **$select** parametern](query-odata-filter-orderby-syntax.md) att välja vilka fält som ska ingå i sökresultat från Azure Search. Den här artikeln beskrivs syntaxen för **$select** i detalj. Mer allmän information om hur du använder **$select** när du presenterar sökresultat, se [hur du arbetar med sökning resulterar i Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Den **$select** parametern avgör vilka fält för varje dokument som returneras i frågeresultatet. Följande EBNF ([utökade Backus Naur formuläret](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar struktur för den **$select** parameter:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Det finns också ett diagram för interaktiva syntax:

> [!div class="nextstepaction"]
> [OData-syntaxdiagrammet för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Se [OData-referens för uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md) för fullständig EBNF.

Den **$select** parametern kommer på två sätt:

1. En enda stjärna (`*`), vilket betyder att alla hämtningsbara fält ska returneras, eller
1. En kommaavgränsad lista över fält sökvägar, identifiera vilka fält som ska returneras.

När du använder den andra formen, kan du bara ange hämtningsbara fält i listan.

Om du visar ett komplext fält utan att ange dess underordnade fält uttryckligen inkluderas alla hämtningsbara underordnade fält i frågeresultatet. Anta exempelvis att indexet har en `Address` med `Street`, `City`, och `Country` underordnade fält som är alla hämtningsbar. Om du anger `Address` i **$select**, resultatet av frågan innehåller alla tre underordnade fält.

## <a name="examples"></a>Exempel

Inkludera den `HotelId`, `HotelName`, och `Rating` översta fält i resultatet, samt de `City` icke fältet för `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Ett exempelresultat kan se ut så här:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Inkludera den `HotelName` översta fältet i resultatet, samt alla underordnade fält för `Address`, och `Type` och `BaseRate` underordnade fält för varje objekt i den `Rooms` samling:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Ett exempelresultat kan se ut så här:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg  

- [Hur du arbetar med sökning resulterar i Azure Search](search-pagination-page-layout.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
