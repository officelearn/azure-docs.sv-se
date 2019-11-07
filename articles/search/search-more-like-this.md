---
title: moreLikeThis (för hands version) fråga-funktionen
titleSuffix: Azure Cognitive Search
description: Beskriver funktionen moreLikeThis (för hands version), som är tillgänglig i för hands versioner av Azure Kognitiv sökning REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721729"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (för hands version) i Azure Kognitiv sökning

> [!IMPORTANT] 
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller .NET SDK.

`moreLikeThis=[key]` är en frågeparameter i [Sökdokument-API: et](https://docs.microsoft.com/rest/api/searchservice/search-documents) som hittar dokument som liknar det dokument som anges av dokument nyckeln. När en sökbegäran görs med `moreLikeThis`genereras en fråga med Sök villkor som extraheras från det dokument som beskriver det dokumentet bäst. Den genererade frågan används sedan för att göra sökningen. Som standard beaktas innehållet i alla sökbara fält, minus eventuella begränsade fält som du har angett med hjälp av parametern `searchFields`. Det går inte att använda parametern `moreLikeThis` med Sök parametern `search=[string]`.

Som standard beaktas innehållet i alla sökbara fält på den översta nivån. Om du vill ange specifika fält i stället kan du använda `searchFields`-parametern. 

Du kan inte använda moreLikeThis i sökbara underordnade fält i en [komplex typ](search-howto-complex-data-types.md).

## <a name="examples"></a>Exempel 

Nedan visas ett exempel på en moreLikeThis-fråga. Frågan söker efter dokument vars beskrivnings fält liknar det fält i käll dokumentet som anges i parametern `moreLikeThis`.

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
> [Utforska Azure Kognitiv sökning REST-API: er med Postman](search-get-started-postman.md)