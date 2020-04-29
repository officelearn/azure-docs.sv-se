---
title: ta med fil
description: ta med fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81263359"
---
Följande sammanfattar viktiga begränsningar i allmän tillgänglighet.

### <a name="sku-ingress-rates-and-capacities"></a>Pris nivåer och kapaciteter för SKU

Ingångs priser och kapaciteter för S1 och S2 SKU ger flexibilitet när du konfigurerar en ny Time Series Insightss miljö. SKU-kapaciteten anger din dagliga ingångs taxa baserat på antalet händelser eller byte som lagras, beroende på vilket som kommer först. Observera att inkommande trafik mäts *per minut*och att **begränsningen** tillämpas med hjälp av token-Bucket. Ingress mäts i block om 1 KB. Till exempel skulle en faktisk 0,8-KB-händelse mätas som en händelse, och en händelse på 2,6 KB räknas som tre händelser.

| S1 SKU-kapacitet | Ingress-pris | Maximal lagrings kapacitet
| --- | --- | --- |
| 1 | 1 GB (1 000 000 händelser) per dag | 30 GB (30 000 000 händelser) per månad |
| 10 | 10 GB (10 000 000 händelser) per dag | 300 GB (300 000 000 händelser) per månad |

| S2 SKU-kapacitet | Ingress-pris | Maximal lagrings kapacitet
| --- | --- | --- |
| 1 | 10 GB (10 000 000 händelser) per dag | 300 GB (300 000 000 händelser) per månad |
| 10 | 100 GB (100 000 000 händelser) per dag | 3 TB (3 000 000 000 händelser) per månad |

> [!NOTE]
> Kapaciteter skalas linjärt, så en S1-SKU med kapacitet 2 stöder 2 2 000 000 GB händelser per dag och 60 GB (60 000 000-händelser) per månad.

S2 SKU-miljöer stöder betydligt fler händelser per månad och har en mycket högre ingångs kapacitet.

| SKU  | Antal händelser per månad  | Antal händelser per minut | Händelse storlek per minut  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000   |  720    |  720 KB   |
 |S2     |   300 000 000   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Egenskaps gränser

Egenskaper för allmän egenskap beror på den SKU-miljö som är markerad. Angivna händelse egenskaper har motsvarande JSON-, CSV-och diagram kolumner som kan visas i [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Maximalt antal egenskaper |
| --- | --- |
| S1 | 600 egenskaper (kolumner) |
| S2 | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelse källor stöds per instans. 

* Lär dig hur du [lägger till en Event Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurera [en IoT Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>API-gränser

REST API gränser för Time Series Insights allmän tillgänglighet anges i [REST API referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).