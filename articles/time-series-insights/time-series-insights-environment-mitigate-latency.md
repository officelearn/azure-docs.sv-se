---
title: Så här övervakar och minskar du begränsningen i Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du övervakar, diagnostiserar och minimerar prestanda problem som orsakar latens och begränsning i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 4e82cdf43f568b6415cb7cb00ce0244654559b7d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990138"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Övervaka och minimera begränsningen för att minska svars tiden i Azure Time Series Insights

När mängden inkommande data överstiger din miljös konfiguration kan du uppleva fördröjning eller begränsning i Azure Time Series Insights.

Du kan undvika svars tider och begränsningar genom att konfigurera din miljö på rätt sätt för den mängd data som du vill analysera.

Du upplever förmodligen svars tid och begränsning när du:

- Lägg till en händelse källa som innehåller gamla data som kan överskrida den tilldelade ingångs takten (Time Series Insights måste fångas upp).
- Lägg till fler händelse källor i en miljö, vilket resulterar i en ökning från ytterligare händelser (vilket kan överstiga din miljös kapacitet).
- Skicka stora mängder historiska händelser till en händelse källa, vilket resulterar i en fördröjning (Time Series Insights måste fångas upp).
- Koppla referens data med telemetri, vilket resulterar i större händelse storlek.  Från ett begränsnings perspektiv behandlas ett inträngt data paket med en paket storlek på 32 KB som 32-händelser, varje storlek 1 KB. Den största tillåtna händelse storleken är 32 KB; data paket som är större än 32 KB trunkeras.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Lär dig mer om hur du Time Series Insights data ingångs beteende och hur du planerar för det.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Övervaka svars tid och begränsning med aviseringar

Aviseringar kan hjälpa dig att diagnostisera och minimera latens problem som orsakas av din miljö.

1. I Azure Portal väljer du **aviseringar**.

   [![aviseringar](media/environment-mitigate-latency/add-alerts.png)](media/environment-mitigate-latency/add-alerts.png#lightbox)

1. Panelen **Skapa regel** visas sedan. Välj **Lägg till** under **villkor**.

   [![Lägg till avisering](media/environment-mitigate-latency/alert-pane.png)](media/environment-mitigate-latency/alert-pane.png#lightbox)

1. Konfigurera sedan de exakta villkoren för signal logiken.

   [![konfigurera signal logik](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Därifrån kan du konfigurera aviseringar med några av följande villkor:

   |Mått  |Beskrivning  |
   |---------|---------|
   |**Inkommande mottagna byte**     | Antal obehandlade byte som lästs från händelse källor. RAW Count inkluderar vanligt vis egenskaps namn och-värde.  |  
   |**Inkommande mottagna ogiltiga meddelanden**     | Antalet ogiltiga meddelanden som lästs från alla Azure Event Hubs-eller Azure IoT Hub-händelseloggen.      |
   |**Ingress mottagna meddelanden**   | Antal meddelanden som lästs från alla händelse källor för Event Hubs eller IoT Hub.        |
   |**Inkommande lagrade byte**     | Total storlek på händelser som lagras och är tillgängliga för fråga. Storleken beräknas bara för egenskap svärdet.        |
   |**Ingress lagrade händelser**     |   Antal utplattade händelser som lagras och är tillgängliga för fråga.      |
   |**Mottagnings tid för mottagna meddelanden, fördröjning**    |  Skillnaden i sekunder mellan den tid som meddelandet står i kö i händelse källan och den tid det bearbetas i ingress.      |
   |Antal ingångar **mottagna meddelanden, fördröjning**    |  Skillnaden mellan sekvensnumret för det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för det meddelande som bearbetas i ingress.      |

   Välj **Done** (Klar).

1. När du har konfigurerat den önskade signal logiken granskar du den valda aviserings regeln visuellt.

   [![ingress](media/environment-mitigate-latency/ingress.png)](media/environment-mitigate-latency/ingress.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Hantering av begränsning och drifts hantering

* Om du är begränsad kommer du att se ett värde för *fördröjningen för ingångs meddelanden*, informerar dig om hur många sekunder som ligger bakom din Time Series Insights-miljö från den faktiska tiden som meddelandet träffar händelse källan (exklusive indexerings tiden av appx. 30-60 sekunder).  

  Ingångs *antalet mottagna meddelande antals fördröjning* bör också ha ett värde, så att du kan bestämma hur många meddelanden som ligger bakom dig.  Det enklaste sättet att komma igång är att öka din miljös kapacitet till en storlek som gör det möjligt för dig att undvika skillnaden.  

  Om du till exempel ser att din S1-miljö demonstrerar fördröjningen på 5 000 000 meddelanden kan du öka storleken på din miljö till sex enheter i ungefär en dag för att komma igång.  Du kan öka ännu mer för att komma igång snabbare. Den omfångs perioden är en vanlig händelse när du först skapar en miljö, särskilt när du ansluter den till en händelse källa som redan har händelser eller när du överför massor av historiska data.

* En annan metod är att ange en varning om **inträngande lagrade händelser** > = ett tröskelvärde strax under den totala miljö kapaciteten under en period på två timmar.  Den här aviseringen kan hjälpa dig att förstå om du ständigt har kapacitet, vilket tyder på en hög sannolikhet för fördröjning. 

  Om du till exempel har tre S1-enheter etablerade (eller 2100 händelser per minut ingångs kapacitet), kan du ange en varning om **inträngande lagrade händelser** för > = 1900 händelser i 2 timmar. Om du ständigt överträffar det här tröskelvärdet och därför utlöser aviseringen är du förmodligen under etablerad.  

* Om du misstänker att du är begränsad kan du jämföra dina ingångs **meddelanden** med händelse källans utgående meddelanden.  Om ingångs steg i Händelsehubben är större än dina ingångs bara **mottagna meddelanden**, begränsas Time Series Insights troligt vis.

## <a name="improving-performance"></a>Förbättra prestanda

Det bästa sättet att åtgärda problemet är att öka din miljö kapacitet för att minska begränsningen eller fördröjningen.

Du kan undvika svars tider och begränsningar genom att konfigurera din miljö på rätt sätt för den mängd data som du vill analysera. Mer information om hur du lägger till kapacitet i din miljö finns i [skala din miljö](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Nästa steg

- Läs om [att diagnostisera och lösa problem i din Time Series Insightss miljö](time-series-insights-diagnose-and-solve-problems.md).

- Lär dig [hur du skalar din Time Series Insightss miljö](time-series-insights-how-to-scale-your-environment.md).