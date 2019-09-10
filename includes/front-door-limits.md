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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "67333394"
---
| Resource | Standard/högsta gräns |
| --- | --- |
| Azure frontend service-resurser per prenumeration | 100 |
| Klient dels värdar som innehåller anpassade domäner per resurs | 100 |
| Regler för routning per resurs | 100 |
| Server dels pooler per resurs | 50 |
| Backend-ändar per backend-pool | 100 |
| Sök vägs mönster som matchar en regel för routning | 25 |
| Anpassade brand Väggs regler för webb program per princip | 10 |
| Brand Väggs princip för webb program per resurs | 100 |
| Villkor för brand vägg för webbaserade programs matchning per anpassad regel | 10 |
| Webb program brand vägg IP-adressintervall per matchnings villkor | 600 |
| Webb programmets brand vägg sträng matchnings värden per matchnings villkor | 10 |
| Webb programmets brand vägg sträng matchnings värde längd | 256 |
| Brand vägg för webbaserade program POST, parameter namn längd | 256 |
| Webb program brand väggens HTTP-huvud namn längd | 256 |
| Cookie-namn längd för webb program brand vägg | 256 |
| Webb program brand vägg, HTTP-begärans text storlek har kontroller ATS | 128 KB |
| Webb program brand väggens anpassade svars text längd | 2 KB |

### <a name="timeout-values"></a>Timeout-värden
#### <a name="client-to-front-door"></a>Klient till front dörr
- Front dörren har en inaktiv TCP-anslutningstimeout på 61 sekunder.

#### <a name="front-door-to-application-back-end"></a>Frontend-dörr till programmets Server del
- Om svaret är ett segmenterat svar returneras en 200 om eller när det första segmentet tas emot.
- När HTTP-begäran har vidarebefordrats till Server delen väntar front dörren i 30 sekunder för det första paketet från Server delen. Sedan returneras ett 503-fel till klienten.
- När det första paketet tas emot från Server delen väntar front dörren i 30 sekunder i en tids gräns för inaktivitet. Sedan returneras ett 503-fel till klienten.
- Den främre dörren till Server delens timeout för TCP-session är 30 minuter.

### <a name="upload-and-download-data-limit"></a>Ladda upp och ladda ned data gräns

|  | Med Chunked Transfer Encoding (common Table EXPRESSIONS) | Utan HTTP-segment |
| ---- | ------- | ------- |
| **Ladda ned** | Det finns ingen gräns för nedladdnings storleken. | Det finns ingen gräns för nedladdnings storleken. |
| **Upp** |  Det finns ingen gräns så länge som varje common Table EXPRESSIONS uppladdning är mindre än 2 GB. | Storleken får inte vara större än 2 GB. |

### <a name="other-limits"></a>Andra gränser
- Maximal URL-storlek – 8 192 byte-anger den högsta tillåtna längden för den råa URL: en (schema + värdnamn + port + sökväg + frågesträng för URL)-maximal Frågesträngs storlek-4 096 byte-anger den maximala längden på frågesträngen, i byte.