---
title: Hur du övervakar och minska begränsning i Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du övervaka, diagnostisera och minska prestandaproblem med som orsakar svarstid och begränsning i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: a404eb1393f9e99c2e2932c2d23724051f1b72a0
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628495"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Övervaka och minimera begränsningar om du vill minska svarstiden i Azure Time Series Insights
När mängden inkommande data överskrider konfigurationen för din miljö kan uppstår du fördröjning eller begränsning i Azure Time Series Insights.

Du kan undvika svarstid och begränsning genom att konfigurera din miljö den mängd data som du vill analysera.

Du är mest sannolika svarstid och begränsning när du:

- Lägga till en händelsekälla som innehåller gamla data som får överstiga tilldelade ingående avgiften (Time Series Insights måste komma ikapp).
- Lägga till fler händelsekällor i en miljö, vilket resulterar i en topp från ytterligare händelser (som kan överskrida kapaciteten för din miljö).
- Skicka stora mängder historiska händelser till en händelsekälla, vilket resulterar i en fördröjning (Time Series Insights måste komma ikapp).
- Ansluta till referensdata med telemetri, vilket resulterar i större händelsestorleken.  En ingressed datapaketet paketstorleken är på 32 KB behandlas som 32 händelser från en begränsning perspektiv, var och en storlek 1 KB. Den maximala tillåtna händelsestorleken är 32 KB; datapaket som är större än 32 KB trunkeras.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Övervaka svarstid och begränsning med aviseringar

Aviseringar kan hjälpa dig att diagnostisera och förebygga problem med nätverkssvarstiden på grund av din miljö. 

1. I Azure-portalen klickar du på **mått**. 

   ![Mått](media/environment-mitigate-latency/add-metrics.png)

2. Klicka på **Lägg till metrisk varning**.  

    ![Lägg till måttavisering](media/environment-mitigate-latency/add-metric-alert.png)

Därifrån kan konfigurera du aviseringar med hjälp av följande mått:

|Mått  |Beskrivning  |
|---------|---------|
|**Ingående har tagit emot byte**     | Antal byte som läses från händelsekällan. Raw-beräkning inkluderar vanligtvis egenskapsnamn och värde.  |  
|**Ingående erhöll ett felaktigt meddelande**     | Antal felaktiga meddelanden läsa från alla Händelsehubbar i Azure eller Azure IoT Hub händelsekällor.      |
|**Ingående fått meddelanden**   | Antal meddelanden att läsa från alla Event Hubs eller IoT-hubbar händelsekällor.        |
|**Ingående lagras byte**     | Total storlek på händelser som lagras och tillgängliga för frågor. Storlek beräknas endast på egenskapens värde.        |
|**Ingående lagrade händelser**     |   Antal Flat händelser lagras där tillgängliga för frågor.      |
|**Tidsförskjutningen för Ingress mottaget meddelande**    |  Skillnad i sekunder från det att meddelandet är i kö i käll- och den tid som bearbetas i ingående.      |
|**Ingående emot Meddelandeantalet fördröjning**    |  Skillnaden mellan sekvensnumret för den sista köas meddelandet datakällan i partitionen och sekvens antal meddelande som bearbetas i ingående.      |


![Svarstid](media/environment-mitigate-latency/latency.png)

Om du har begränsats, ser du ett värde för den *ingående tog emot meddelande tidsförskjutningen*, informerar dig om hur många sekunder bakom TSI är från den faktiska tiden meddelandet når händelsekällan (exklusive indexering tidpunkten för appx. 30-60 sekunder).  *Fördröjning för inkommande antal meddelanden tas emot* bör även ha ett värde, så att du kan avgöra hur många meddelanden bakom dig som.  Det enklaste sättet att få uppdateringar är att öka kapaciteten för din miljö till en storlek som gör att du kan lösa skillnaden.  

Om du har en enda enhet S1-miljö och att det finns en fördröjning i fem miljoner meddelandet, kan du öka storleken på din miljö till sex enheter för runt en dag för att få uppdateringar.  Du kan öka även ytterligare till catch dig snabbare.  Catch-up perioden är vanligt förekommande när du först etablerar en miljö, särskilt när du ansluter den till en händelsekälla som redan innehåller händelser i den eller när du massimportera ladda upp stora mängder historiska data.

En annan metod är att ställa in en **lagras Ingångshändelser** avisering > = ett tröskelvärde något nedanför din totala kapacitet under 2 timmar.  Den här aviseringen kan hjälpa dig att förstå om du hela tiden finns på kapacitet som anger en hög sannolikhet fördröjning.  

Kan till exempel, om du har tre S1-enheter som etablerats (eller 2100 händelser per minut ingress-kapacitet) kan du ange en **lagras Ingångshändelser** avisering för > = 1900 händelser i 2 timmar. Om du är ständigt som överskrider tröskelvärdet och därför kan utlösa aviseringen, är du förmodligen under allokerad.  

Även om du misstänker att du har begränsats kan du jämföra din **ingående emot meddelanden** med din händelse är källan egressed meddelanden.  Om inkommande till din Event Hub är större än din **ingående emot meddelanden**, Time Series Insights är sannolikt begränsas.

## <a name="improving-performance"></a>Förbättra prestanda 
Om du vill minska begränsning eller upplever svarstid, är det bästa sättet att rätta till det att öka kapaciteten för din miljö. 

Du kan undvika svarstid och begränsning genom att konfigurera din miljö den mängd data som du vill analysera. Läs mer om hur du lägger till kapacitet i miljön, [skala din miljö](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Nästa steg
- För ytterligare felsökning [diagnostisera och lösa problem i miljön för Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).
- För ytterligare hjälp kan du starta en konversation på den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Du kan även kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för vilka supportalternativ.
