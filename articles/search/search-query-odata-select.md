---
title: OData Select-referens
titleSuffix: Azure Cognitive Search
description: OData språk referens för Select-syntax i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 7786974f3d39f9cbc81e1ffea955156d623f1476
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793250"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select-syntax i Azure Kognitiv sökning

 Du kan använda [OData **$Select** -parametern](query-odata-filter-orderby-syntax.md) för att välja vilka fält som ska ingå i sök resultatet från Azure kognitiv sökning. I den här artikeln beskrivs syntaxen för **$Select** i detalj. Mer allmän information om hur du använder **$Select** när du presenterar Sök resultat finns i [så här arbetar du med Sök resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametern **$Select** avgör vilka fält för varje dokument som returneras i frågeresultatet. Följande EBNF ([Extended backable-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för parametern **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

**$Select** -parametern finns i två former:

1. En enskild stjärna (`*`) som visar att alla hämtnings bara fält ska returneras, eller
1. En kommaavgränsad lista över fält Sök vägar, som identifierar vilka fält som ska returneras.

När du använder det andra formuläret kan du bara ange hämtnings bara fält i listan.

Om du anger ett komplext fält utan att uttryckligen ange dess underordnade fält, inkluderas alla hämtnings bara underordnade fält i frågeresultatet. Anta till exempel att ditt index har ett `Address`-fält med `Street`, `City`och `Country` under fält som kan hämtas. Om du anger `Address` i **$Select**innehåller frågeresultaten alla tre under fälten.

## <a name="examples"></a>Exempel

Ta med fälten `HotelId`, `HotelName`och `Rating` översta nivån i resultaten, samt `City` under fält för `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Ett exempel på resultat kan se ut så här:

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

Ta med fältet `HotelName` översta nivån i resultaten, samt alla under fält för `Address`och `Type` och `BaseRate` under fält för varje objekt i `Rooms`-samlingen:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Ett exempel på resultat kan se ut så här:

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

- [Så här arbetar du med Sök resultat i Azure Kognitiv sökning](search-pagination-page-layout.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure-kognitiv sökning REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
