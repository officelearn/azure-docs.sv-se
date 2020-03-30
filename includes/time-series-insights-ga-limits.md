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
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013760"
---
Följande sammanfattar viktiga gränser i allmän tillgänglighet.

### <a name="sku-ingress-rates-and-capacities"></a>SKU-ingångshastigheter och kapacitet

S1 och S2 SKU ingångshastigheter och kapacitet ger flexibilitet när du konfigurerar en ny Time Series Insights-miljö.

| S1 SKU kapacitet | Inträngningshastighet | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 1 GB (1 miljon händelser) | 30 GB (30 miljoner händelser) per månad |
| 10 | 10 GB (10 miljoner händelser) | 300 GB (300 miljoner händelser) per månad |

| S2 SKU kapacitet | Inträngningshastighet | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 10 GB (10 miljoner händelser) | 300 GB (300 miljoner händelser) per månad |
| 10 | 100 GB (100 miljoner händelser) | 3 TB (3 miljarder händelser) per månad |

> [!NOTE]
> Kapaciteterna skalas linjärt, så en S1 SKU med kapacitet 2 stöder 2 GB (2 miljoner) händelser per dag inträngningshastighet och 60 GB (60 miljoner händelser) per månad.

S2 SKU-miljöer stöder betydligt fler händelser per månad och har en betydligt högre inträngningskapacitet.

| SKU  | Antal händelser per månad  | Händelsestorlek per månad  | Antal händelser per minut | Händelsestorlek per minut  |
|---------|---------|---------|---------|---------|
| S1     |   30 miljoner     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 miljoner    |   300 GB   | 7,200   | 7 200 KB  |

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