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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263359"
---
Följande sammanfattar viktiga gränser i allmän tillgänglighet.

### <a name="sku-ingress-rates-and-capacities"></a>SKU-ingångshastigheter och kapacitet

S1 och S2 SKU ingångshastigheter och kapacitet ger flexibilitet när du konfigurerar en ny Time Series Insights-miljö. Din SKU-kapacitet anger din dagliga inträngningsfrekvens baserat på antalet händelser eller antal som lagras, beroende på vilket som inträffar först. Observera att inträngning mäts *per minut*och **begränsning** tillämpas med hjälp av token bucket-algoritmen. Inträngning mäts i 1 KB block. Till exempel en 0,8 KB faktisk händelse skulle mätas som en händelse, och en 2,6 KB händelse räknas som tre händelser.

| S1 SKU kapacitet | Inträngningshastighet | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 1 GB (1 miljon händelser) per dag | 30 GB (30 miljoner händelser) per månad |
| 10 | 10 GB (10 miljoner händelser) per dag | 300 GB (300 miljoner händelser) per månad |

| S2 SKU kapacitet | Inträngningshastighet | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 10 GB (10 miljoner händelser) per dag | 300 GB (300 miljoner händelser) per månad |
| 10 | 100 GB (100 miljoner händelser) per dag | 3 TB (3 miljarder händelser) per månad |

> [!NOTE]
> Kapaciteterna skalas linjärt, så en S1 SKU med kapacitet 2 stöder 2 GB (2 miljoner) händelser per dag inträngningshastighet och 60 GB (60 miljoner händelser) per månad.

S2 SKU-miljöer stöder betydligt fler händelser per månad och har en betydligt högre inträngningskapacitet.

| SKU  | Antal händelser per månad  | Antal händelser per minut | Händelsestorlek per minut  |
|---------|---------|---------|---------|---------|
| S1     |   30 miljoner   |  720    |  720 KB   |
 |S2     |   300 miljoner   | 7,200   | 7 200 KB  |

### <a name="property-limits"></a>Begränsningar för egenskaper

Ga-egenskapsgränser beror på vilken SKU-miljö som har valts. Medföljande händelseegenskaper har motsvarande JSON-, CSV- och diagramkolumner som kan visas i [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Maximala egenskaper |
| --- | --- |
| S1 | 600 egenskaper (kolumner) |
| S2 | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelsekällor per instans stöds. 

* Läs om hur du [lägger till en händelsehubbkälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurera [en IoT-hubbkälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>API-gränser

REST API-gränser för Time Series Insights Allmän tillgänglighet anges i [REST API-referensdokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).