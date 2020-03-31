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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123174"
---
### <a name="general-availability-and-preview-comparison"></a>Allmän tillgänglighet och förhandsgranskningsjämförelse

I följande tabell sammanfattas flera viktiga skillnader mellan Azure Time Series Insights allmänna tillgänglighet (GA) och förhandsversionsinstanser.

| | Allmän tillgänglighet (GA) | Förhandsversion |
| --- | --- | ---|
| Första klassens medborgare | Händelsecentrerad | Tid-serie-centrerad |
| Semantiska resonemang | Låg nivå (referensdata) | Hög nivå (modeller) |
| Datakontextualisering | Nivå som inte är enhet | Enhets- och icke-enhetsnivå |
| Lagring av beräkningslogik | Inga | Lagras i typvariabler del av modellen |
| Lagrings- och åtkomstkontroll | Inga | Aktiverad via modell |
| Aggregeringar/Provtagning | Inga | Viktad händelse och tidsvägd |
| Signalrekonstruktion | Inga | Interpolation |
| Produktion av härledda tidsserier | Inga | Ja, sammanslagningar och kopplingar |
| Språkflexibilitet | Icke-komposterbar | Komposterbar |
| Uttrycksspråk | Predikatsträng | Tidsserieuttryck (predikatsträngar, värden, uttryck och funktioner) |

### <a name="property-limits"></a>Begränsningar för egenskaper

Time Series Insights fastighetsgränser har ökat till 1000 från ett maximalt tak på 800 i GA. Medföljande händelseegenskaper har motsvarande JSON-, CSV- och diagramkolumner som du kan visa i [utforskaren för förhandsversionen av Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

| SKU | Maximala egenskaper |
| --- | --- |
| Förhandsgranska PAYG | 1 000 egenskaper (kolumner) |
| GA S1 | 600 egenskaper (kolumner) |
| GA S2 | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelsekällor per instans stöds. 

* Läs om hur du [lägger till en händelsehubbkälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurera [en IoT-hubbkälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Som standard [stöder förhandsgranskningsmiljöer inträngningshastigheter](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) på upp till **1 MB/s per sekund (MB/s) per miljö**. Kunder kan skala sina förhandsversionsmiljöer upp till **16 MB/s** dataflöde om det behövs. Det finns också en gräns per partition på **0,5 MB/s**. 

### <a name="api-limits"></a>API-gränser

REST API-gränser för förhandsversionen av Time Series Insights anges i [rest API-referensdokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
