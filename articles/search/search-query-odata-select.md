---
title: OData Select Reference-Azure Search
description: OData-Språkreferens för Select-syntax i Azure Search-frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647880"
---
# <a name="odata-select-syntax-in-azure-search"></a>OData-$select syntax i Azure Search

 Du kan använda [OData **$Select** -parametern](query-odata-filter-orderby-syntax.md) för att välja vilka fält som ska ingå i Sök resultaten från Azure Search. I den här artikeln beskrivs syntaxen för **$Select** i detalj. Mer allmän information om hur du använder **$Select** när du presenterar Sök resultat finns [i så här arbetar du med sök resultat i Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametern **$Select** avgör vilka fält för varje dokument som returneras i frågeresultatet. Följande EBNF ([Extended backable-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för parametern **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Se [syntax för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

**$Select** -parametern finns i två former:

1. En enda stjärna (`*`) som visar att alla hämtnings bara fält ska returneras, eller
1. En kommaavgränsad lista över fält Sök vägar, som identifierar vilka fält som ska returneras.

När du använder det andra formuläret kan du bara ange hämtnings bara fält i listan.

Om du anger ett komplext fält utan att uttryckligen ange dess underordnade fält, inkluderas alla hämtnings bara underordnade fält i frågeresultatet. Anta till exempel att indexet har ett `Address` fält med `Street`, `City`och `Country` under fält som alla kan hämtas. Om du anger `Address` i **$Select**kommer frågeresultatet att innehålla alla tre under fält.

## <a name="examples"></a>Exempel

`HotelName` `Address`Inkludera fälten `HotelId`, och`Rating` den`City` översta nivån i resultaten, samt under fältet för:

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

`BaseRate` `Address` `Type` Ta med fältet `Rooms` översta nivån i resultaten, samt alla under fält i och under fälten för varje objekt i samlingen: `HotelName`

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

- [Så här arbetar du med Sök resultat i Azure Search](search-pagination-page-layout.md)
- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
