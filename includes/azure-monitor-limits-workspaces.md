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
ms.openlocfilehash: 58a741b369231a353a6b8e282a6e604a63a5727d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210349"
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
| Dataexport | Inte tillgänglig för närvarande | Använd Azure Function eller Logic app för att aggregera och exportera data. | 

**Data inmatnings takt**

Azure Monitor är en hög skalbar data tjänst som tjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Standard tröskelvärdet för inmatnings frekvens är inställt på **500 MB/min** per arbets yta. Om du skickar data till ett högre pris till en enskild arbets yta, släpps vissa data och en händelse skickas till *Åtgärds* tabellen i arbets ytan var 6: e timme medan tröskelvärdet fortsätter att överskridas. Om din inmatnings volym fortsätter att överskrida hastighets gränsen eller om du förväntar dig att få en stund snart, kan du begära en ökning av arbets ytan genom att öppna en support förfrågan.
 
Om du vill bli informerad om en sådan händelse i arbets ytan skapar du en [logg aviserings regel](../articles/azure-monitor/platform/alerts-log.md) med hjälp av följande fråga med aviserings logik basen för antalet resultat som är större än noll.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Beroende på hur länge du har använt Log Analytics kan du ha åtkomst till äldre pris nivåer. Läs mer om [Log Analytics äldre pris nivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 