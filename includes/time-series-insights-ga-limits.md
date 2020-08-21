---
title: inkludera fil
description: inkludera fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 1aff5780aa5f4cf21d862a5cee3b140179dac03c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703327"
---
Följande sammanfattar viktiga gränser i Azure Time Series Insights gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Pris nivåer och kapaciteter för SKU

Ingångs priser och kapaciteter för S1 och S2 SKU ger flexibilitet när du konfigurerar en ny Azure Time Series Insightss miljö. SKU-kapaciteten anger din dagliga ingångs taxa baserat på antalet händelser eller byte som lagras, beroende på vilket som kommer först. Observera att inkommande trafik mäts *per minut*och att **begränsningen** tillämpas med hjälp av token-Bucket. Ingress mäts i block om 1 KB. Till exempel skulle en faktisk 0,8-KB-händelse mätas som en händelse, och en händelse på 2,6 KB räknas som tre händelser.

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

Gen1 egenskaps gränser beror på den SKU-miljö som är markerad. Angivna händelse egenskaper har motsvarande JSON-, CSV-och diagram kolumner som kan visas i [Azure Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Maximalt antal egenskaper |
| --- | --- |
| S1 | 600 egenskaper (kolumner) |
| S2 | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelse källor stöds per instans.

* Lär dig hur du [lägger till en Event Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurera [en IoT Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>API-gränser

REST API gränser för Azure Time Series Insights gen1 anges i [REST API referens dokumentation](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability).