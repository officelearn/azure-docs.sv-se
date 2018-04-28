---
title: moreLikeThis i Azure Search (förhandsversion) | Microsoft Docs
description: Preliminär dokumentation för funktionen moreLikeThis (förhandsgranskning), visas i Azure Search REST API.
authors: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 29d9a478ca2e91e658d7d0f52e7a193ba694bc16
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis i Azure Search (förhandsgranskning)

`moreLikeThis=[key]` är en frågeparameter i den [Sök API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Genom att ange den `moreLikeThis` parameter i en sökning som du kan hitta dokument som liknar det dokument som anges av dokumentnyckeln. När en sökning har begärts med `moreLikeThis`, en fråga genereras med sökord som extraheras från dokumentet som bäst beskriver det dokumentet. Genererade frågan används sedan för att göra sökningen igen. Som standard innehållet i alla `searchable` fält anses såvida inte den `searchFields` används för att begränsa fälten. Den `moreLikeThis` parametern kan inte användas med parametern Sök `search=[string]`.

## <a name="examples"></a>Exempel 

Nedan visas ett exempel på en moreLikeThis fråga. Frågan hittar dokument vars beskrivningsfält liknar mest fältet källdokument som anges av den `moreLikeThis` parameter.

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

Funktionen moreLikeThis är för närvarande under förhandsgranskning och stöds endast i förhandsversionen api-versioner, `2015-02-28-Preview` och `2016-09-01-Preview`. Eftersom API-version anges i begäran, är det möjligt att kombinera allmänt tillgänglig (GA) och förhandsgranska API: er i samma app. Dock kan preview API: er inte är under SLA och funktioner ändras, så vi inte rekommenderar att använda dem i program i produktion.