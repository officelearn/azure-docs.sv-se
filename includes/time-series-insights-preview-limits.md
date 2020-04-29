---
title: ta med fil
description: ta med fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77123174"
---
### <a name="general-availability-and-preview-comparison"></a>Allmän tillgänglighet och för hands versions jämförelse

I följande tabell sammanfattas flera viktiga skillnader mellan Azure Time Series Insights allmän tillgänglighet (GA) och för hands versions instanser.

| | Allmän tillgänglighet (GA) | Förhandsversion |
| --- | --- | ---|
| Medborgare i första klass | Händelse-centrerad | Tidsserien – centrerad |
| Semantiska orsaker | Lågnivå (referens data) | Hög nivå (modeller) |
| Data contextualization | Ej enhets nivå | Enhets-och icke-enhets nivå |
| Beräkning av logik lagring | Nej | Lagrad i typen variabler del av modellen |
| Lagring och åtkomst kontroll | Nej | Aktive rad via modell |
| Agg regeringar/sampling | Nej | Event viktad och tids viktad |
| Återuppbyggnad av signal | Nej | Interpol |
| Produktion av härledd tids serie | Nej | Ja, sammanslagningar och kopplingar |
| Språk flexibilitet | Ej sammanställnings bar | Sammansättnings bar |
| Uttrycksspråk | Predikat-sträng | Tids serie uttryck (predikaa strängar, värden, uttryck och funktioner) |

### <a name="property-limits"></a>Egenskaps gränser

Time Series Insights egenskaps gränser har ökat till 1 000 från en maximal ände på 800 i GA. Angivna händelse egenskaper har motsvarande JSON-, CSV-och diagram kolumner som du kan visa i [Time Series Insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximalt antal egenskaper |
| --- | --- |
| Förhandsgranska PAYG | 1 000 egenskaper (kolumner) |
| GA S1 | 600 egenskaper (kolumner) |
| GA S2 | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelse källor stöds per instans. 

* Lär dig hur du [lägger till en Event Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurera [en IoT Hub-källa](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Som standard har [förhands gransknings miljöer stöd](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) för **ingångs hastigheter på upp till 1 megabyte per sekund (MB/s) per miljö**. Kunder kan skala sina för hands versioner upp till **16 MB/s** genom strömning vid behov. Det finns också en gräns per partition på **0,5 MB/s**. 

### <a name="api-limits"></a>API-gränser

REST API gränser för Time Series Insights för hands versionen anges i [REST API referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
