---
title: moreLikeThis i Azure Search (förhandsversion) | Microsoft Docs
description: Preliminära dokumentationen för funktionen moreLikeThis (förhandsversion) visas i Azure Search REST API.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: d8b7dd754700a5b8cc781a0b13bd1b3ffecb2806
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468372"
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