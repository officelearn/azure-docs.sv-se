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
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334850"
---
**Volym och lagring av datainsamling** 

| Nivå | Gräns per dag | Datakvarhållning | Kommentar |
|:---|:---|:---|:---|
| Aktuell prisnivå per GB<br>(infördes i april 2018) | Obegränsad | 30 - 730 dagar | Datalagring efter 31 dagar är tillgänglig för extra avgifter. Läs mer om Azure Monitor-priser. |
| Äldre kostnadsfria nivåer<br>(infördes i april 2016) | 500 MB | 7 dagar | När arbetsytan når gränsen på 500 MB per dag stoppas och återupptas datainmatningen i början av nästa dag. En dag baseras på UTC. Observera att data som samlas in av Azure Security Center inte ingår i den här gränsen på 500 MB per dag och kommer att fortsätta att samlas in över den här gränsen.  |
| Äldre fristående per GB-nivå<br>(infördes i april 2016) | Obegränsad | 30 till 730 dagar | Datalagring efter 31 dagar är tillgänglig för extra avgifter. Läs mer om Azure Monitor-priser. |
| Äldre per nod (OMS)<br>(infördes i april 2016) | Obegränsad | 30 till 730 dagar | Datalagring efter 31 dagar är tillgänglig för extra avgifter. Läs mer om Azure Monitor-priser. |
| Äldre standardnivå | Obegränsad | 30 dagar  | Kvarhållning kan inte justeras |
| Äldre Premium-nivå | Obegränsad | 365 dagar  | Kvarhållning kan inte justeras |

**Antal arbetsytor per prenumeration.**

| Prisnivå    | Gräns för arbetsyta | Kommentarer
|:---|:---|:---|
| Kostnadsfri nivå  | 10 | Den här gränsen kan inte höjas. |
| Alla andra nivåer | Obegränsad | Du begränsas av antalet resurser inom en resursgrupp och antalet resursgrupper per prenumeration. |

**Azure-portal**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Högsta poster som returneras av en loggfråga | 10 000 | Minska resultaten med hjälp av frågeomfattning, tidsintervall och filter i frågan. |


**API för datainsamlare**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximal storlek för ett enda inlägg | 30 MB | Dela upp större volymer i flera inlägg. |
| Maximal storlek för fältvärden  | 32 KB | Fält som är längre än 32 kB trunkeras. |

**Sök-API**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Högsta poster som returneras i en enskild fråga | 500 000 | |
| Maximal storlek på data som returneras | 64.000.000 byte (~61 MiB)| |
| Maximal körningstid för frågan | 10 minuter | Mer information finns i [Timeouts.](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)  |
| Högsta begärstränte | 200 begäranden per 30 sekunder per AAD-användare eller klient-IP-adress | Mer information finns i [Prisgränser.](https://dev.loganalytics.io/documentation/Using-the-API/Limits) |

**Allmänna begränsningar för arbetsytan**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximala kolumner i en tabell         | 500 | |
| Maximala tecken för kolumnnamn | 500 | |
| Dataexport | Inte tillgänglig för tillfället | Använd Azure Function eller Logic App för att aggregera och exportera data. | 

**Volymhastighet för datainmatning**


Azure Monitor är en storskalig datatjänst som betjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Standardgränsen för inmatningsvolymhastighet för data som skickas från Azure-resurser med [diagnostikinställningar](../articles/azure-monitor/platform/diagnostic-settings.md) är cirka **6 GB/min** per arbetsyta. Detta är ett ungefärligt värde eftersom den faktiska storleken kan variera mellan datatyper beroende på logglängden och dess kompressionsförhållande. Den här gränsen gäller inte för data som skickas från agenter eller [datainsamlar-API](../articles/azure-monitor/platform/data-collector-api.md).

Om du skickar data med en högre hastighet till en enda arbetsyta tas vissa data bort och en händelse skickas till *tabellen Operation* på arbetsytan var sjätte timme medan tröskelvärdet fortsätter att överskridas. Om din intagsvolym fortsätter att överskrida hastighetsgränsen eller om du förväntar dig att nå den någon gång snart kan du begära en ökning av arbetsytan genom att öppna en supportbegäran.
 
Om du vill meddelas om en sådan händelse på arbetsytan skapar du en [loggaviseringsregel](../articles/azure-monitor/platform/alerts-log.md) med följande fråga med varningslogikbas på antalet resultatgaller än noll.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Beroende på hur länge du har använt Log Analytics kan du ha åtkomst till äldre prisnivåer. Läs mer om [äldre prisnivåer i Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 