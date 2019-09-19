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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "70392904"
---
**Data insamlings volym och kvarhållning** 

| Nivå | Gräns per dag | Datakvarhållning | Kommentar |
|:---|:---|:---|:---|
| Aktuell pris nivå per GB<br>(lanserades april 2018) | Ingen gräns | 30-730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Äldre kostnads fria nivåer<br>(lanserades april 2016) | 500 MB | 7 dagar | När din arbets yta når gränsen på 500 MB per dag stoppas data inmatningen och återupptas i början av nästa dag. En dag baseras på UTC. Observera att data som samlas in av Azure Security Center inte ingår i den här gränsen på 500 MB per dag och fortsätter att samlas in över den här gränsen.  |
| Äldre fristående per GB-nivå<br>(lanserades april 2016) | Ingen gräns | 30 till 730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Bakåtkompatibelt per nod (OMS)<br>(lanserades april 2016) | Ingen gräns | 30 till 730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Äldre standard nivå | Ingen gräns | 30 dagar  | Det går inte att justera kvarhållning |
| Legacy Premium-nivå | Ingen gräns | 365 dagar  | Det går inte att justera kvarhållning |

**Antal arbets ytor per prenumeration.**

| Prisnivå    | Arbets ytans gräns | Kommentar
|:---|:---|:---|
| Kostnadsfri nivå  | 10 | Det går inte att öka den här gränsen. |
| Alla andra nivåer | Ingen gräns | Du är begränsad till antalet resurser i en resurs grupp och antalet resurs grupper per prenumeration. |

**Azure Portal**

| Category | Begränsningar | Kommentar |
|:---|:---|:---|
| Maximalt antal poster som returneras av en logg fråga | 10 000 | Minska resultatet med fråge omfånget, tidsintervallet och filtren i frågan. |


**API för data insamling**

| Category | Begränsningar | Kommentar |
|:---|:---|:---|
| Maximal storlek för ett enskilt inlägg | 30 MB | Dela upp större volymer i flera inlägg. |
| Maximal storlek för fält värden  | 32 KB | Fält som är längre än 32 kB trunkeras. |

**Sök-API**

| Category | Begränsningar | Kommentar |
|:---|:---|:---|
| Maximalt antal poster som returneras i en enskild fråga | 500,000 | |
| Maximal storlek för returnerade data | 64 000 000 byte (~ 61 MiB)| |
| Maximal kör tid för fråga | 10 minuter | Se [timeout](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) för mer information.  |
| Högsta begär ande frekvens | 200 förfrågningar per 30 sekunder per AAD-användare eller klient-IP-adress | Mer information finns i avsnittet om [hastighets begränsningar](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Gränser för allmän arbets yta**

| Category | Begränsningar | Kommentar |
|:---|:---|:---|
| Maximalt antal kolumner i en tabell         | 500 | |
| Maximalt antal tecken för kolumn namn | 500 | |
| Regioner med kapacitet | Västra centrala USA | Du kan för närvarande inte skapa en ny arbets yta i det här området eftersom den har en tillfällig kapacitets gräns. Den här gränsen planeras för att åtgärdas i slutet av oktober 2019. |
| Data export | Inte tillgänglig för närvarande | Använd Azure Function eller Logic app för att aggregera och exportera data. | 

>[!NOTE]
>Beroende på hur länge du har använt Log Analytics kan du ha åtkomst till äldre pris nivåer. Läs mer om [Log Analytics äldre pris nivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 