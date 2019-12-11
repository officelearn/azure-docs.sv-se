---
title: ta med fil
description: ta med fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/06/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: be46a0dda24dc990e895a3e983c730337f9cf31d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981480"
---
### <a name="general-availability-and-preview-comparison"></a>Allmän tillgänglighet och för hands versions jämförelse

I följande tabell sammanfattas flera viktiga skillnader mellan Azure Time Series Insights allmän tillgänglighet (GA) och för hands versions instanser.

| | Allmänt tillgänglig | Förhandsversion |
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

### <a name="api-limits"></a>API-gränser

REST API gränser för Time Series Insights för hands versionen anges i [REST API referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).