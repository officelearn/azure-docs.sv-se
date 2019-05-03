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
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024677"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis i Azure Search (förhandsversion)

`moreLikeThis=[key]` är en frågeparameter i den [API för webbsökning dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) som söker efter dokument liknar de dokument som anges av dokumentnyckeln. När en sökbegäran görs med `moreLikeThis`, skapas en fråga med sökvillkor som extraheras från dokumentet som bäst beskriver det dokumentet. Frågan som skapas används sedan för att göra sökningen igen. Som standard, innehållet i alla sökbara fält betraktas minus eventuella begränsade fält som du angav med hjälp av den `searchFields` parametern. Den `moreLikeThis` parametern kan inte användas med parametern search `search=[string]`.

Som standard betraktas innehållet i alla översta sökbara fält. Om du vill ange vilka fält i stället kan du använda den `searchFields` parametern. 

> [!NOTE]
> `moreLikeThis` Förhandsgranskning fungerar inte på sökbara delfält i en [komplex typ](search-howto-complex-data-types.md).

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

## <a name="feature-availability"></a>Funktionstillgänglighet

Den `moreLikeThis` parametern är tillgängligt i förhandsversionen REST API: er (`api-version=2019-05-06-Preview`).

## <a name="next-steps"></a>Nästa steg

Du kan använda en webbplats som testar verktyget för att experimentera med den här funktionen.  Vi rekommenderar att du använder Postman för den här övningen.

> [!div class="nextstepaction"]
> [Utforska Azure Search REST API: er med Postman](search-fiddler.md)