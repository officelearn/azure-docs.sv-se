---
title: inkludera fil
description: inkludera fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 864b37c9e59786546ad2c29faf8457cfc3a21f6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82161163"
---
**Data insamlings volym och kvarhållning** 

| Nivå | Gräns per dag | Datakvarhållning | Kommentar |
|:---|:---|:---|:---|
| Aktuell pris nivå per GB<br>(lanserades april 2018) | Obegränsad | 30-730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Äldre kostnads fria nivåer<br>(lanserades april 2016) | 500 MB | 7 dagar | När din arbets yta når gränsen på 500 MB per dag stoppas data inmatningen och återupptas i början av nästa dag. En dag baseras på UTC. Observera att data som samlas in av Azure Security Center inte ingår i den här gränsen på 500 MB per dag och fortsätter att samlas in över den här gränsen.  |
| Äldre fristående per GB-nivå<br>(lanserades april 2016) | Obegränsad | 30 till 730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Bakåtkompatibelt per nod (OMS)<br>(lanserades april 2016) | Obegränsad | 30 till 730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Äldre standard nivå | Obegränsad | 30 dagar  | Det går inte att justera kvarhållning |
| Legacy Premium-nivå | Obegränsad | 365 dagar  | Det går inte att justera kvarhållning |

**Antal arbets ytor per prenumeration.**

| Prisnivå    | Arbets ytans gräns | Kommentarer
|:---|:---|:---|
| Kostnadsfri nivå  | 10 | Det går inte att öka den här gränsen. |
| Alla andra nivåer | Obegränsad | Du är begränsad till antalet resurser i en resurs grupp och antalet resurs grupper per prenumeration. |

**Azure Portal**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal poster som returneras av en logg fråga | 10 000 | Minska resultatet med fråge omfånget, tidsintervallet och filtren i frågan. |


**API för data insamling**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximal storlek för ett enskilt inlägg | 30 MB | Dela upp större volymer i flera inlägg. |
| Maximal storlek för fält värden  | 32 KB | Fält som är längre än 32 kB trunkeras. |

**Sök API**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal poster som returneras i en enskild fråga | 500 000 | |
| Maximal storlek för returnerade data | 64 000 000 byte (~ 61 MiB)| |
| Maximal kör tid för fråga | 10 minuter | Se [timeout](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) för mer information.  |
| Högsta begär ande frekvens | 200 förfrågningar per 30 sekunder per AAD-användare eller klient-IP-adress | Mer information finns i avsnittet om [hastighets begränsningar](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Gränser för allmän arbets yta**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal kolumner i en tabell         | 500 | |
| Maximalt antal tecken för kolumn namn | 500 | |
| Dataexport | Inte tillgänglig för närvarande | Använd Azure Function eller Logic app för att aggregera och exportera data. | 

**Volym pris för data inmatning**


Azure Monitor är en hög skalbar data tjänst som tjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Standard inläsnings volymens volym hastighet för data som skickas från Azure-resurser med hjälp av [diagnostikinställningar](../articles/azure-monitor/platform/diagnostic-settings.md) är cirka **6 GB/min** per arbets yta. Detta är ett ungefärligt värde eftersom den faktiska storleken kan variera mellan olika data typer beroende på logg längden och dess komprimerings förhållande. Den här begränsningen gäller inte för data som skickas från agenter eller [API för data insamling](../articles/azure-monitor/platform/data-collector-api.md).

Om du skickar data till ett högre pris till en enskild arbets yta, släpps vissa data och en händelse skickas till *Åtgärds* tabellen i arbets ytan var 6: e timme medan tröskelvärdet fortsätter att överskridas. Om din inmatnings volym fortsätter att överskrida hastighets gränsen eller om du förväntar dig att få en stund snart, kan du begära en ökning av din arbets yta genom att skicka ett e-postmeddelande till LAIngestionRate@microsoft.com eller öppna en support förfrågan.
 
Om du vill bli informerad om en sådan händelse i arbets ytan skapar du en [logg aviserings regel](../articles/azure-monitor/platform/alerts-log.md) med hjälp av följande fråga med aviserings logik basen för antalet resultat som är större än noll.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Beroende på hur länge du har använt Log Analytics kan du ha åtkomst till äldre pris nivåer. Läs mer om [Log Analytics äldre pris nivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
