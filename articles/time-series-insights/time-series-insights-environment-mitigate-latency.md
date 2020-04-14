---
title: Så här övervakar och minskar du begränsning – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du övervakar, diagnostiserar och minskar prestandaproblem som orsakar svarstid och begränsning i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 2812b535c7aef7987db7106bfa6b07e15a1b61c7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263394"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Övervaka och minska begränsning för att minska svarstiden i Azure Time Series Insights

När mängden inkommande data överskrider konfigurationen av din miljö kan du uppleva svarstid eller begränsning i Azure Time Series Insights.

Du kan undvika svarstid och begränsning genom att korrekt konfigurera din miljö för den mängd data som du vill analysera.

Det är mest troligt att du drabbas av svarstid och begränsning när du:

- Lägg till en händelsekälla som innehåller gamla data som kan överskrida din tilldelade ingressfrekvens (Time Series Insights måste komma ikapp).
- Lägg till fler händelsekällor i en miljö, vilket resulterar i en topp från ytterligare händelser (som kan överskrida miljöns kapacitet).
- Skicka stora mängder historiska händelser till en händelsekälla, vilket resulterar i en fördröjning (Time Series Insights måste komma ikapp).
- Gå med i referensdata med telemetri, vilket resulterar i större händelsestorlek. Den maximala tillåtna paketstorleken är 32 KB. datapaket som är större än 32 kB trunkeras.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Läs mer om Time Series Insights datainträngningsbeteende och hur du planerar för det.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Övervaka svarstid och begränsning med aviseringar

Aviseringar kan hjälpa dig att diagnostisera och minska svarstidsproblem som uppstår i din miljö.

1. Välj din Time Series Insights-miljö i Azure-portalen. Välj sedan **Aviseringar**.

   [![Lägga till en avisering i time series insights-miljön](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Välj **+ Ny aviseringsregel**. Panelen **Skapa regel** visas sedan. Välj **Lägg till** under **VILLKOR**.

   [![Fönstret Lägg till varning](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Konfigurera sedan de exakta villkoren för signallogiken.

   [![Konfigurera signallogiken](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Därifrån kan du konfigurera aviseringar med hjälp av några av följande villkor:

   |Mått  |Beskrivning  |
   |---------|---------|
   |**Inkommande mottagna byte**     | Antal råbyte som lästs från händelsekällor. Råräkning innehåller vanligtvis egenskapsnamnet och värdet.  |  
   |**Inkommande mottagna ogiltiga meddelanden**     | Antal ogiltiga meddelanden som lästs från alla Azure Event Hubs eller Azure IoT Hub-händelsekällor.      |
   |**Inkommande mottagna meddelanden**   | Antal meddelanden som lästs från alla händelsehubbar eller IoT Hubs-händelsekällor.        |
   |**Ingående lagrade byte**     | Total storlek på händelser som lagrats och är tillgängliga för frågor. Storleken beräknas endast på egenskapsvärdet.        |
   |**Inträngning lagrade händelser**    |   Antal tillplattade händelser som lagrats och är tillgängliga för frågor.      |
   |**Inkommande mottagna meddelande tidsfördröjning**   |  Skillnad i sekunder mellan den tidpunkt då meddelandet är köat i händelsekällan och den tid då det bearbetas i Ingress.      |
   |**Fördröjning för mottagna meddelanden**   |  Skillnaden mellan sekvensnumret för det senast köade meddelandet i händelsekällpartitionen och sekvensnumret för meddelandet som bearbetas i Ingress.      |

   Välj **Done** (Klar).

1. När du har konfigurerat önskad signallogik granskar du den valda varningsregeln visuellt.

   [![Svarstidsvy och diagram](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Begränsning och ingående hantering

* Om du begränsas registreras ett värde för fördröjningen för *inkommande mottagna meddelanden* som informerar dig om hur många sekunder efter din TIme Series Insights-miljö från den faktiska tiden meddelandet når händelsekällan (exklusive indexeringstid för appx. 30-60 sekunder).  

  *Ingress Mottagna meddelanden Count Lag* bör också ha ett värde, så att du kan bestämma hur många meddelanden bakom dig är.  Det enklaste sättet att fastna är att öka din miljö kapacitet till en storlek som gör att du kan övervinna skillnaden.  

  Om din S1-miljö till exempel visar fördröjning på 5 000 000 meddelanden kan du öka storleken på din miljö till sex enheter för ungefär en dag för att fastna.  Du kan öka ännu mer för att komma ikapp snabbare. Catch-up-perioden är en vanlig företeelse när du först etablerar en miljö, särskilt när du ansluter den till en händelsekälla som redan har händelser i den eller när du massförserar massor av historiska data.

* En annan teknik är att ställa in en **ingående lagrad händelsevarning** >= ett tröskelvärde något under din totala miljökapacitet under en period av 2 timmar.  Den här aviseringen kan hjälpa dig att förstå om du ständigt har kapacitet, vilket indikerar en hög sannolikhet för svarstid. 

  Om du till exempel har etablerat tre S1-enheter (eller 2100 händelser per minuts inträngningskapacitet) kan du ställa in en **ingående lagrad händelseavisering** för >= 1900 händelser i 2 timmar. Om du ständigt överskrider den här tröskeln, och därför utlöser din avisering, är du sannolikt underetablerade.  

* Om du misstänker att du begränsas kan du jämföra dina **inkommande mottagna meddelanden** med händelsekällans utgående meddelanden.  Om inträngningen i händelsehubben är större än dina **ingående mottagna meddelanden**begränsas dina time series-insikter.

## <a name="improving-performance"></a>Förbättra prestanda

För att minska begränsningen eller uppleva svarstid, är det bästa sättet att korrigera det att öka din miljö kapacitet.

Du kan undvika svarstid och begränsning genom att korrekt konfigurera din miljö för den mängd data som du vill analysera. Mer information om hur du lägger till kapacitet i din miljö finns i [Skala din miljö](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Nästa steg

- Läs om [Diagnostisera och lös problem i din Time Series Insights-miljö](time-series-insights-diagnose-and-solve-problems.md).

- Lär dig hur du [skalar time series insights-miljön](time-series-insights-how-to-scale-your-environment.md).