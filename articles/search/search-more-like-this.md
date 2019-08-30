---
title: moreLikeThis i Azure Search (för hands version) – Azure Search
description: Preliminär dokumentation för funktionen moreLikeThis (för hands version) som visas i Azure Search REST API.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182324"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis i Azure Search

> [!Note]
> moreLikeThis är en för hands version och är inte avsedd för produktions användning. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.

`moreLikeThis=[key]`är en frågeparameter i [search Documents-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents) som hittar dokument som liknar det dokument som anges av dokument nyckeln. När en sökbegäran görs med `moreLikeThis`, genereras en fråga med Sök villkor som extraheras från det dokument som beskriver det dokumentet bäst. Den genererade frågan används sedan för att göra sökningen. Som standard beaktas innehållet i alla sökbara fält, minus eventuella begränsade fält som du har angett med `searchFields` parametern. Parametern kan inte användas med Sök parametern, `search=[string]`. `moreLikeThis`

Som standard beaktas innehållet i alla sökbara fält på den översta nivån. Om du vill ange specifika fält i stället kan du använda `searchFields` -parametern. 

Du kan inte använda moreLikeThis i sökbara underordnade fält i en [komplex typ](search-howto-complex-data-types.md).

## <a name="examples"></a>Exempel 

Nedan visas ett exempel på en moreLikeThis-fråga. Frågan söker efter dokument vars beskrivnings fält liknar det fält i käll dokumentet som anges av `moreLikeThis` parametern.

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

Du kan använda valfritt verktyg för webbtest för att experimentera med den här funktionen.  Vi rekommenderar att du använder Postman för den här övningen.

> [!div class="nextstepaction"]
> [Utforska Azure Search REST API: er med Postman](search-get-started-postman.md)