---
title: moreLikeThis i Azure Search (förhandsversion) – Azure Search
description: Preliminära dokumentationen för funktionen moreLikeThis (förhandsversion) visas i Azure Search REST API.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 5723f1ab7258a9e0d672b5c0fd9fd0b9c4dc8721
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522928"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis i Azure Search

> [!Note]
> moreLikeThis är i förhandsversion och inte är avsett för användning i produktion. Den [REST API-version 2019-05-06-Preview](search-api-preview.md) ger den här funktionen. Det finns inget stöd för .NET SDK just nu.

`moreLikeThis=[key]` är en frågeparameter i den [API för webbsökning dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) som söker efter dokument liknar de dokument som anges av dokumentnyckeln. När en sökbegäran görs med `moreLikeThis`, skapas en fråga med sökvillkor som extraheras från dokumentet som bäst beskriver det dokumentet. Frågan som skapas används sedan för att göra sökningen igen. Som standard, innehållet i alla sökbara fält betraktas minus eventuella begränsade fält som du angav med hjälp av den `searchFields` parametern. Den `moreLikeThis` parametern kan inte användas med parametern search `search=[string]`.

Som standard betraktas innehållet i alla översta sökbara fält. Om du vill ange vilka fält i stället kan du använda den `searchFields` parametern. 

Du kan inte använda moreLikeThis på sökbara underordnade fält i en [komplex typ](search-howto-complex-data-types.md).

## <a name="examples"></a>Exempel 

Nedan visas ett exempel på en moreLikeThis-fråga. Frågan hittar dokument vars beskrivning fälten liknar mest fältet dokumentets källa som anges av den `moreLikeThis` parametern.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Nästa steg

Du kan använda en webbplats som testar verktyget för att experimentera med den här funktionen.  Vi rekommenderar att du använder Postman för den här övningen.

> [!div class="nextstepaction"]
> [Utforska Azure Search REST API: er med Postman](search-fiddler.md)