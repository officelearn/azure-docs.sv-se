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
ms.openlocfilehash: e3fa5616518675d8475937ec63afdd8e1742e8c6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553845"
---
| Resurs | Standardgräns |
| --- | --- |
| Azure ytterdörren Service-resurser per prenumeration | 100 |
| Frontend-värdar som innehåller anpassade domäner per resurs | 100 |
| Routningsregler per resurs | 100 |
| Serverdelspooler per resurs | 50 |
| Serverdelar per backend-poolen | 100 |
| Sökvägsmönster att matcha för en regel för vidarebefordran | 25 |
| Brandväggsregler för anpassade webb program per princip | 10 |
| Web application brandväggsprincipen per resurs | 100 |

### <a name="timeout-values"></a>Timeout-värden
#### <a name="client-to-front-door"></a>Klient till ytterdörren
- Dörren har en timeout för anslutning av inaktivitet TCP 61 sekunder.

#### <a name="front-door-to-application-back-end"></a>Åtkomsten till programmet serverdel
- Om svaret är ett segmenterat svar, returneras 200 om eller när det första segmentet tas emot.
- När HTTP-begäran vidarebefordras till backend-server, väntar ytterdörren i 30 sekunder för det första paketet från backend-servern. Sedan returnerar den ett 503-fel till klienten.
- När det första paketet tas emot från backend-servern, väntar ytterdörren på 30 sekunder på en timeout för inaktivitet. Sedan returnerar den ett 503-fel till klienten.
- Åtkomsten till backend-TCP-tidsgränsen för sessionen är 30 minuter.

### <a name="upload-and-download-data-limit"></a>Ladda upp och hämta datagräns

|  | Med chunked-kodning (CTE) | Utan http-storlekar |
| ---- | ------- | ------- |
| **Ladda ned** | Det finns ingen gräns för storleken. | Det finns ingen gräns för storleken. |
| **Ladda upp** |  Det finns ingen gräns så länge varje CTE-överföring är mindre än 28.6 MB. | Storleken får inte vara större än 28.6. MB. |
