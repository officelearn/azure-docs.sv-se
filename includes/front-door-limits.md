---
title: ta med fil
description: ta med fil
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333394"
---
| Resource | Standard-/ maxgräns |
| --- | --- |
| Azure ytterdörren Service-resurser per prenumeration | 100 |
| Frontend-värdar som innehåller anpassade domäner per resurs | 100 |
| Routningsregler per resurs | 100 |
| Serverdelspooler per resurs | 50 |
| Serverdelar per backend-poolen | 100 |
| Sökvägsmönster att matcha för en regel för vidarebefordran | 25 |
| Brandväggsregler för anpassade webb program per princip | 10 |
| Web application brandväggsprincipen per resurs | 100 |
| Web application firewall matchningsvillkor per anpassad regel | 10 |
| Web application firewall IP-adressintervall per matchningsvillkor | 600 |
| Web application firewall matchning strängvärden per matchningsvillkor | 10 |
| Web application firewall sträng matchning värdets längd | 256 |
| Brandvägg för webbaserade program efter brödtext parametern namnlängd | 256 |
| Brandvägg för webbaserade program namnlängd för HTTP-huvud | 256 |
| Namnlängd för Web application firewall cookie | 256 |
| Web application firewall HTTP begäran textstorleken kontrolleras | 128 KB |
| Web application firewall anpassade brödtext svarslängd | 2 KB |

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
| **Ladda upp** |  Det finns ingen gräns så länge varje CTE-överföring är mindre än 2 GB. | Storleken får inte vara större än 2 GB. |

### <a name="other-limits"></a>Andra gränser
- Maxstorleken till URL - 8 192 byte - anger maxlängden för raw-URL (schema + värdnamn + port + sökväg + frågesträng URL) - maximal frågesträng storlek – 4 096 byte - anger den maximala längden för frågesträngen, i byte.