---
title: moreLikeThis i Azure Search (förhandsversion) – Azure Search
description: Preliminära dokumentationen för funktionen moreLikeThis (förhandsversion) visas i Azure Search REST API.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569050"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis i Azure Search (förhandsversion)

`moreLikeThis=[key]` är en frågeparameter i den [API: et Search](https://docs.microsoft.com/rest/api/searchservice/search-documents). Genom att ange den `moreLikeThis` parameter i en sökfråga kan du hitta dokument som liknar de dokument som anges av dokumentnyckeln. När en sökbegäran görs med `moreLikeThis`, skapas en fråga med sökvillkor som extraheras från dokumentet som bäst beskriver det dokumentet. Frågan som skapas används sedan för att göra sökningen igen. Som standard innehållet i alla `searchable` fält betraktas såvida inte den `searchFields` används för att begränsa fält. Den `moreLikeThis` parametern kan inte användas med parametern search `search=[string]`.

## <a name="examples"></a>Exempel 

Nedan visas ett exempel på en moreLikeThis-fråga. Frågan hittar dokument vars beskrivning fälten liknar mest fältet dokumentets källa som anges av den `moreLikeThis` parametern.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>Funktionstillgänglighet

MoreLikeThis-funktionen är för närvarande preliminärt och stöds endast i förhandsversionen api-versioner, `2015-02-28-Preview` och `2016-09-01-Preview`. Eftersom API-version anges i begäran, är det möjligt att kombinera allmänt tillgänglig (GA) och förhandsgranska API: er i samma app. Dock kan förhandsversion API: er inte står under serviceavtal och funktioner ändras, så vi inte rekommenderar att använda dem i produktionsprogram.
