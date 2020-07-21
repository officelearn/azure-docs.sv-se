---
title: ta med fil
description: ta med fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: cd6ea6d4967e024ddf88fb9572d5efae8b7a7815
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495352"
---
### <a name="property-limits"></a>Egenskaps gränser

Azure Time Series Insights egenskaps gränser har ökat till 1 000 från en maximal ände på 800 i gen1. Angivna händelse egenskaper har motsvarande JSON-, CSV-och diagram kolumner som du kan visa i [Azure Time Series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximalt antal egenskaper |
| --- | --- |
| Gen2 (L1) | 1 000 egenskaper (kolumner) |
| Gen1 (S1) | 600 egenskaper (kolumner) |
| Gen1 (S2) | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelse källor stöds per instans.

* Lär dig hur du [lägger till en Event Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurera [en IoT Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Som standard [stöder Gen2-miljöer](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-throughput-limitations) ingångs hastigheter på upp till **1 megabyte per sekund (MB/s) per miljö**. Kunder kan skala sina miljöer upp till **16 MB/s** genom strömning vid behov. Det finns också en gräns per partition på **0,5 MB/s**.

### <a name="api-limits"></a>API-gränser

REST API gränser för Azure Time Series Insights Gen2 anges i [REST API referens dokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
