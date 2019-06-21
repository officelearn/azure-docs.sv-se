---
title: ta med fil
description: ta med fil
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293457"
---
Följande begränsningar gäller för varje Log Analytics-arbetsyta i den aktuella förbrukningsbaserad prisnivån lanserades i April 2018:

|     | Per GB 2018 |
| --- | --- | 
| Datavolym som samlas in per dag | Ingen |
| Datakvarhållningstid | 30 till 730 dagar<sup>1</sup> |

Följande begränsningar gäller för varje Log Analytics-arbetsyta senaste äldre prisnivåer:

|  | Lediga | Fristående (Per GB) | Per nod (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| Datavolym som samlas in per dag |500 MB<sup>2</sup> |Ingen |Ingen |
| Datakvarhållningstid |7 dagar | 30 till 730 dagar<sup>1</sup> | 30 till 730 dagar<sup>1</sup> |

Följande begränsningar gäller för varje Log Analytics-arbetsyta äldsta äldre prisnivåer:

|  | Standard | Premium | 
| --- | --- | --- | --- | --- | --- |--- |
| Datavolym som samlas in per dag | Ingen | Ingen | 
| Datakvarhållningstid |30 dagar | 365 dagar |

<sup>1</sup>datakvarhållning efter 31 dagar är tillgänglig för extra avgifter. Läs mer om [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

<sup>2</sup>när din arbetsyta når den dagliga dataöverföringsgränsen i 500 MB, dataanalys stoppar och återupptas i början av nästa dag. En dag baseras på UTC.

>[!NOTE]
>Beroende på hur länge du har använt Log Analytics, kanske du har åtkomst till äldre prisnivåer. Läs mer om [Log Analytics äldre prisnivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
>

Följande begränsningar gäller för Azure Log Analytics-resurser (arbetsytor) per prenumeration.

| Prisnivå    | Antal arbetsytor per prenumeration | Kommentar
| --- | --- | --- |
| Gratis nivå  | 10 | Den här gränsen kan inte höjas. |
| Alla nivåer än kostnadsfri | Gäller inte | Du är begränsad av antalet resurser i en resursgrupp och antalet resursgrupper per prenumeration. | 

Följande begränsningar gäller för Log Analytics-API: er:

| Category | Limits | Kommentar
| --- | --- | --- |
| API för datainsamling | Maximal storlek för ett enskilt inlägg är 30 MB.<br>Maximal storlek för fältvärden är 32 KB. | Dela större volymer i flera olika inlägg.<br>Fält som är längre än 32 kB trunkeras. |
| Sök-API | 5 000 poster returneras för ej sammanräknade data.<br>500 000 poster för sammanräknade data. | Sammanräknade data är en sökning som innefattar de `summarize` kommando.
 
