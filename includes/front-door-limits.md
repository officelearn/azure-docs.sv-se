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
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335089"
---
| Resurs | Gräns |
| --- | --- |
| Azure Front Door-resurser per prenumeration | 100 |
| Front-end värdar, som innehåller anpassade domäner per resurs | 500 |
| Routningsregler per resurs | 500 |
| Backend-pooler per resurs | 50 |
| Backdelar per backend-pool | 100 |
| Banmönster som matchar en routningsregel | 25 |
| Url:er i ett enda cacherensningsanrop | 100 |
| Regler för brandvägg för anpassade webbprogram per princip | 100 |
| Brandväggsprincipen för webbprogram per prenumeration | 100 |
| Brandvägg för webbprogram matchar villkor per anpassad regel | 10 |
| IP-adressintervall för webbprogram brandvägg per matchningsvillkor | 600 |
| Brandväggssträngen för webbprogram matchar värden per matchningsvillkor | 10 |
| Brandväggssträngen för webbprogram matchar värdelängd | 256 |
| Webbprogram brandvägg POST brödtext parameter namnlängd | 256 |
| HTTP-huvudnamnslängd för webbprogram | 256 |
| Namnlängd för brandväggstill brandvägg för webbapplikation | 256 |
| Webbprogram brandvägg HTTP begäran kroppsstorlek inspekteras | 128 kB |
| Anpassad svarslängd för webbprogramsbrandgens | 2 KB |

### <a name="timeout-values"></a>Timeout-värden
#### <a name="client-to-front-door"></a>Klient till ytterdörren
* Ytterdörren har en tomgång TCP-anslutningsutgång på 61 sekunder.

#### <a name="front-door-to-application-back-end"></a>Ytterdörr till ansökan back-end
* Om svaret är ett segmenterat svar returneras en 200 om eller när det första segmentet tas emot.
* När HTTP-begäran har vidarebefordrats till den bakre delen väntar ytterdörren i 30 sekunder på det första paketet från den bakre delen. Sedan returnerar ett 503-fel till klienten. Det här värdet kan konfigureras via fältet sendRecvTimeoutSeconds i API:et.
    * För cachelagringsscenarier kan den här timeouten inte konfigureras och därför returneras ett 504-fel till klienten om en begäran cachelagras och det tar mer än 30 sekunder för det första paketet från ytterdörren eller från serverdelen. 
* När det första paketet har tagits emot från den bakre delen väntar ytterdörren i 30 sekunder i en inaktiv timeout. Sedan returnerar ett 503-fel till klienten. Det här timeout-värdet kan inte konfigureras.
* Timeout för TCP-sessionen i backend är 90 sekunder.

### <a name="upload-and-download-data-limit"></a>Gränsen för ladda upp och ladda ned data

|  | Med segmenterad överföringskodning (CTE) | Utan HTTP-segmentering |
| ---- | ------- | ------- |
| **Ladda ned** | Det finns ingen gräns för nedladdningsstorleken. | Det finns ingen gräns för nedladdningsstorleken. |
| **Överför** |    Det finns ingen gräns så länge varje CTE-uppladdning är mindre än 2 GB. | Storleken får inte vara större än 2 GB. |

### <a name="other-limits"></a>Andra gränser
* Maximal URL-storlek - 8 192 byte - Anger maximal längd på den råa WEBBADRESSEN (schema + värdnamn + port + sökväg + frågesträng för WEBBADRESSEN)
* Maximal frågesträngstorlek - 4 096 byte - Anger frågesträngens maximala längd i byte.
* Maximal HTTP-svarshuvudstorlek från hälsoavsöknings-URL - 4 096 byte - Anger den maximala längden på alla svarshuvuden för hälsoavsökningar. 
