---
title: "Hur du övervakar prestanda, minska begränsning och förhindra svarstid i Azure tid serien insikter | Microsoft Docs"
description: "Den här artikeln beskriver hur du övervaka, diagnostisera och minska problem med prestanda som orsakar svarstid och begränsning i Azure tid serien insikter."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 5af5589922ded802703b9ba8f3b0aa8c5524bbad
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="improve-performance-reduce-latency-and-mitigate-throttling-in-azure-time-series-insights"></a>Förbättra prestanda minska svarstiden och undvika begränsning i Azure tid serien insikter
När mängden inkommande data överskrider din miljö konfiguration, kan det uppstå fördröjningar eller begränsning i Azure tid serien insikter.

Du kan undvika fördröjning och begränsning genom att konfigurera din miljö mängden data som du vill analysera.

Du är mest sannolikt att uppstår fördröjning och begränsning när du:

- Lägg till en källa som innehåller gamla data som kan överskrida tilldelade ingång vilken takt dina (tid serien insikter måste fånga upp).
- Lägga till flera händelsekällor i en miljö, vilket resulterar i en insamling av ytterligare händelser (som kan överstiga kapacitet för din miljö).
- Skicka stora mängder historisk händelser till en händelsekälla, vilket resulterar i en fördröjning (tid serien insikter måste fånga upp).
- Anslut referensdata med telemetri, vilket resulterar i större storlek.  Bandbreddsbegränsning ur ett ingressed datapaketet paketstorleken på 32 KB behandlas som 32-händelser, varje storlek 1 KB. Den maximala tillåtna händelsestorleken är 32 KB; datapaket som är större än 32 KB trunkeras.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Övervaka svarstid och begränsning med aviseringar

Aviseringar kan hjälpa dig att diagnostisera och åtgärda problem med nätverkssvarstiden beror på din miljö. 

1. I Azure-portalen klickar du på **mått**. 

   ![Mått](media/environment-mitigate-latency/add-metrics.png)

2. Klicka på **Lägg till mått avisering**.  

    ![Lägg till mått varning](media/environment-mitigate-latency/add-metric-alert.png)

Därifrån kan konfigurera du aviseringar via följande mått:

|Mått  |Beskrivning  |
|---------|---------|
|**Ingång mottagna byte**     | Antal byte som har lästs från händelsekällor. Raw-beräkning inkluderar vanligtvis egenskapsnamn och värde.  |  
|**Ingång emot ogiltiga meddelanden**     | Antalet ogiltiga meddelanden läsa från alla Händelsehubbar i Azure eller Azure IoT Hub händelsekällor.      |
|**Ingång mottagna meddelanden**   | Antalet meddelanden som har lästs från alla Händelsehubbar eller IoT-hubbar händelsekällor.        |
|**Ingång lagras byte**     | Total storlek på händelser som lagras och tillgängliga för frågor. Storlek beräknas endast på egenskapens värde.        |
|**Ingång lagras händelser**     |   Antal sammanslagna händelser lagrade och tillgängliga för frågor.      |

![Svarstid](media/environment-mitigate-latency/latency.png)

En metod är att ange en **lagras Ingångshändelser** avisering > = ett tröskelvärde något nedanför din totala kapacitet för en period på två timmar.  Den här aviseringen kan hjälpa dig att förstå om du ständigt på kapacitet, som anger en hög sannolikhet av fördröjning.  

Till exempel om du har tre S1 enheterna etableras (eller 2100 händelser per minut ingång kapacitet) du kan ange en **lagras Ingångshändelser** varning för > = 1900 händelser i 2 timmar. Om du ofta överstiger tröskelvärdet, och därför utlösa aviseringen, etableras du förmodligen under.  

Även om du misstänker att du har begränsats, kan du jämföra din **ingång mottagna meddelanden** källa har med din händelse egressed meddelanden.  Om inkommande trafik till din Event Hub är större än din **ingång mottagna meddelanden**, dina tid serien insikter är sannolikt begränsas.

## <a name="improving-performance"></a>Förbättra prestanda 
Om du vill minska begränsning eller upplever svarstid, är det bästa sättet att åtgärda det att öka kapaciteten för din miljö. 

Du kan undvika fördröjning och begränsning genom att konfigurera din miljö mängden data som du vill analysera. Mer information om hur du lägger till kapacitet i din miljö finns [skala din miljö](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Nästa steg
- För ytterligare felsökning [diagnostisera och lösa problem i miljön tid serien insikter](time-series-insights-diagnose-and-solve-problems.md).
- Om du behöver ytterligare hjälp kan du starta ett samtal på den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Du kan också kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för assisterad supportalternativ.
