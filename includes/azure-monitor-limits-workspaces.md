---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305338"
---
**Samling datavolymer och kvarhållning** 

| Nivå | Begränsa per dag | Datakvarhållning | Kommentar |
|:---|:---|:---|:---|
| Nuvarande Per-GB-prisnivå<br>(presenteras April 2018) | Obegränsad | 30 - 730 dagar | Kvarhållning av data efter 31 dagar är tillgänglig för extra avgifter. Läs mer om priser för Azure Monitor. |
| Äldre kostnadsfria nivåer<br>(introduced April 2016) | 500 MB | 7 dagar | När din arbetsyta når 500 MB per dag gränsen, stoppar datainmatning och återupptas i början av nästa dag. En dag baseras på UTC. Observera att data som samlas in av Azure Security Center inte är ingår i den här 500 MB per dag gränsen och fortsätter att vara insamlade ovanför den här gränsen.  |
| Äldre fristående Per GB-nivå<br>(introduced April 2016) | Obegränsad | 30 till 730 dagar | Kvarhållning av data efter 31 dagar är tillgänglig för extra avgifter. Läs mer om priser för Azure Monitor. |
| Äldre Per nod (OMS)<br>(introduced April 2016) | Obegränsad | 30 till 730 dagar | Kvarhållning av data efter 31 dagar är tillgänglig för extra avgifter. Läs mer om priser för Azure Monitor. |
| Äldre Standard-nivån | Obegränsad | 30 dagar  | Det går inte att justera kvarhållning |
| Äldre Premium-nivån | Obegränsad | 365 dagar  | Det går inte att justera kvarhållning |

**Antal arbetsytor per prenumeration.**

| Prisnivå    | Gräns för arbetsytan | Kommentar
|:---|:---|:---|
| Gratis nivå  | 10 | Den här gränsen kan inte höjas. |
| Alla andra nivåer | Obegränsad | Du är begränsad av antalet resurser i en resursgrupp och antalet resursgrupper per prenumeration. |

**Azure Portal**

| Category | Limits | Kommentar |
|:---|:---|:---|
| Maximalt antal poster som returneras av en loggfråga | 10 000 | Minska resultat med hjälp av omfång, tidsintervall och filter i frågan. |


**API för datainsamling**

| Category | Limits | Kommentar |
|:---|:---|:---|
| Maximal storlek för ett enskilt inlägg | 30 MB | Dela större volymer i flera olika inlägg. |
| Maximal storlek för fältvärden  | 32 KB | Fält som är längre än 32 kB trunkeras. |

**API för webbsökning**

| Category | Limits | Kommentar |
|:---|:---|:---|
| Maximalt antal poster som returneras för ej sammanräknade data | 5,000 | |
| Maximalt antal poster för sammanräknade data | 500,000 | Sammanräknade data är en sökning som innefattar de `summarize` kommando. |
| Maximal storlek på data som returneras | 64,000,000 byte (~ 61 MiB)| |
| Maximal körtid fråga | 10 minuter | Se [tidsgränser](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) mer information.  |
| Maximala hastigheten | 200 begäranden per 30 sekunder per AAD användar- eller klientens IP-adress | Se [hastighetsbegränsningar](https://dev.loganalytics.io/documentation/Using-the-API/Limits) mer information. |

**Allmän arbetsytan gränser**

| Category | Limits | Kommentar |
|:---|:---|:---|
| Maximalt antal kolumner i en tabell         | 500 | |
| Högsta antal tecken för kolumnnamn | 500 | |
| Regionerna kapacitet | Västra centrala USA | Du kan inte just nu skapa en ny arbetsyta i den här regionen eftersom det är på tillfälliga kapacitetsgränsen. Den här gränsen är planerat åtgärdas slutet av September 2019. |
| Export av data | Inte tillgänglig för tillfället | Använda Azure-funktion eller Logikapp för att sammanställa och exportera data. | 

>[!NOTE]
>Beroende på hur länge du har använt Log Analytics, kanske du har åtkomst till äldre prisnivåer. Läs mer om [Log Analytics äldre prisnivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 