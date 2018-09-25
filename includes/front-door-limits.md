---
title: ta med fil
description: ta med fil
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006539"
---
| Resurs | Standardgräns |
| --- | --- |
| Ytterdörren resurser per prenumeration | 100 |
| Frontend-värdar, inklusive anpassade domäner per resurs | 100 |
| Routningsregler per resurs | 100 |
| Serverdelspooler per resurs | 50 |
| Serverdelar per backend-pool | 100 |
| Sökvägsmönster att matcha för en regel för vidarebefordran | 25 |
| Brandväggsregler för anpassade webb program per princip | 10 |
| Web application brandväggsprincipen per resurs | 100 |

### <a name="timeout-values"></a>Timeout-värden
#### <a name="client-to-front-door"></a>Klient till ytterdörren
- Dörren har en timeout för anslutning av inaktivitet TCP 61 sekunder.
#### <a name="front-door-to-application-backend"></a>Åtkomsten till serverdelen för programmet
- Om svaret är ett segmenterat svar, returneras 200 om / när det första segmentet tas emot.
- När HTTP-begäran vidarebefordras till serverdelen, väntar ytterdörren på 30 sekunder för det första paketet från serverdel, innan det returneras ett 503-fel till klienten.
- När det första paketet tas emot från serverdelen, väntar ytterdörren i 30 sekunder (timeout för inaktivitet) innan den returneras ett 503-fel till klienten.
- Åtkomsten till serverdelen TCP sessionstidsgränsen är 30 minuter.

### <a name="upload--download-data-limit"></a>Ladda upp / hämta datagräns

|  | Med chunked-kodning (CTE) | Utan http-storlekar |
| ---- | ------- | ------- |
| **Ladda ned** | Det finns ingen gräns för hämtningsstorleken | Det finns ingen gräns för hämtningsstorleken |
| **Ladda upp** |  Ingen gräns är så länge varje CTE ladda upp mindre än 28.6 MB | Storleken får inte vara större än 28.6 MB. |
