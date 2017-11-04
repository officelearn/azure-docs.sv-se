---
title: "moreLikeThis i Azure Search (förhandsversion) | Microsoft Docs"
description: "Preliminär dokumentation för funktionen moreLikeThis (förhandsgranskning), visas i Azure Search REST API."
services: search
documentationCenter: na
authors: mhko
manager: jlembicz
editor: na
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 20f6a1166d47799f15275cb5b6a9ea4934ae57e2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis i Azure Search (förhandsgranskning)

`moreLikeThis=[key]`är en frågeparameter i den [Sök API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Genom att ange den `moreLikeThis` parameter i en sökning som du kan hitta dokument som liknar det dokument som anges av dokumentnyckeln. När en sökning har begärts med `moreLikeThis`, en fråga genereras med sökord som extraheras från dokumentet som bäst beskriver det dokumentet. Genererade frågan används sedan för att göra sökningen igen. Som standard innehållet i alla `searchable` fält anses såvida inte den `searchFields` används för att begränsa fälten. Den `moreLikeThis` parametern kan inte användas med parametern Sök `search=[string]`.

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