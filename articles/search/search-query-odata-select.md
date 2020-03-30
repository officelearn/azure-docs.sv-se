---
title: OData välj referens
titleSuffix: Azure Cognitive Search
description: Syntax och språkreferens för explicit urval av fält som ska returneras i sökresultaten för Azure Cognitive Search-frågor.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113095"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select syntax i Azure Cognitive Search

 Du kan använda [parametern OData **$select** ](query-odata-filter-orderby-syntax.md) för att välja vilka fält som ska inkluderas i sökresultaten från Azure Cognitive Search. I den här artikeln beskrivs syntaxen för **$select** i detalj. Mer allmän information om hur du använder **$select** när du presenterar sökresultat finns [i Så här arbetar du med sökresultat i Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametern **$select** avgör vilka fält för varje dokument som returneras i frågeresultatuppsättningen. Följande EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för **parametern $select:**

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Det finns också ett interaktivt syntaxdiagram:

> [!div class="nextstepaction"]
> [OData-syntaxdiagram för Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Se [OData-uttryckssyntaxreferens för Azure Cognitive Search](search-query-odata-syntax-reference.md) för hela EBNF.

Parametern **$select** finns i två former:

1. En enda`*`stjärna ( ) som anger att alla hämtningsbara fält ska returneras, eller
1. En kommaavgränsad lista över fältsökvägar som identifierar vilka fält som ska returneras.

När du använder det andra formuläret kan du bara ange fält som kan hämtas i listan.

Om du anger ett komplext fält utan att uttryckligen ange dess underfält inkluderas alla underfält som kan hämtas i frågeresultatuppsättningen. Anta till exempel att `Address` indexet `Street`har `City`ett `Country` fält med , och underfält som alla kan hämtas. Om du `Address` anger i **$select**innehåller frågeresultatet alla tre underfälten.

## <a name="examples"></a>Exempel

Inkludera `HotelId`fälten `HotelName` `Rating` , och den översta nivån i `City` resultatet samt `Address`delfältet i :

    $select=HotelId, HotelName, Rating, Address/City

Ett exempel resultat kan se ut så här:

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

Inkludera `HotelName` fältet på den översta nivån i resultatet, samt `Address`alla `Type` underfält i och underfälten för `BaseRate` varje objekt i `Rooms` samlingen:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Ett exempel resultat kan se ut så här:

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

- [Så här arbetar du med sökresultat i Azure Cognitive Search](search-pagination-page-layout.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
