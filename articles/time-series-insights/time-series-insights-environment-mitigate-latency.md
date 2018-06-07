---
title: Hur du övervakar och minska begränsning i Azure tid serien insikter | Microsoft Docs
description: Den här artikeln beskriver hur du övervaka, diagnostisera och minska problem med prestanda som orsakar svarstid och begränsning i Azure tid serien insikter.
ms.service: time-series-insights
services: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: 9a7720934870818a4e13d3d6b5d391d9bd3cddcd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653679"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Övervaka och minska begränsning för att minska svarstiden i Azure tid serien insikter
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
|**Tidsförskjutningen för ingång mottagna meddelanden**    |  Skillnaden mellan att meddelandet i kö i händelsekällan och den tid som den bearbetas i ingång.      |
|**Ingång mottagna antalet meddelanden fördröjning**    |  Skillnaden mellan sekvensnumret för den sista köas meddelandet datakällan i partitionen och sekvens antal meddelanden som bearbetas i ingång.      |


![Svarstid](media/environment-mitigate-latency/latency.png)

Om du har begränsats ser du ett värde för den *ingång mottagna meddelandet tidsförskjutningen*, informerar dig om hur många minuter bakom TSD är från den faktiska tiden meddelandet träffar händelsekällan (exklusive indexering tiden för appx. 30 till 60 sekunder).  *Antalet för ingång mottagna meddelanden fördröjning* bör också ha ett värde så att du kan avgöra hur många meddelanden bakom dig.  Det enklaste sättet att få uppdateringar är att öka kapaciteten för din miljö till en storlek som gör att du kan lösa skillnaden.  

Om du har en enda enhet S1-miljö och att det finns en fördröjning på fem miljoner meddelandet kan du öka storleken på din miljö med sex units för runt en dag att få uppdateringar.  Du kan öka även ytterligare till catch upp snabbare.  Detta är en gemensam förekomst ursprungligen etablera en miljö, särskilt när du ansluter den till en händelsekälla som redan har händelser i den eller när du massimportera överför stora mängder historisk data.

En annan metod är att ange en **lagras Ingångshändelser** avisering > = ett tröskelvärde något nedanför din totala kapacitet för en period på två timmar.  Den här aviseringen kan hjälpa dig att förstå om du ständigt på kapacitet, som anger en hög sannolikhet av fördröjning.  

Till exempel om du har tre S1 enheterna etableras (eller 2100 händelser per minut ingång kapacitet) du kan ange en **lagras Ingångshändelser** varning för > = 1900 händelser i 2 timmar. Om du ofta överstiger tröskelvärdet, och därför utlösa aviseringen, etableras du förmodligen under.  

Även om du misstänker att du har begränsats, kan du jämföra din **ingång mottagna meddelanden** källa har med din händelse egressed meddelanden.  Om inkommande trafik till din Event Hub är större än din **ingång mottagna meddelanden**, dina tid serien insikter är sannolikt begränsas.

## <a name="improving-performance"></a>Förbättra prestanda 
Om du vill minska begränsning eller upplever svarstid, är det bästa sättet att åtgärda det att öka kapaciteten för din miljö. 

Du kan undvika fördröjning och begränsning genom att konfigurera din miljö mängden data som du vill analysera. Mer information om hur du lägger till kapacitet i din miljö finns [skala din miljö](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Nästa steg
- För ytterligare felsökning [diagnostisera och lösa problem i miljön tid serien insikter](time-series-insights-diagnose-and-solve-problems.md).
- Om du behöver ytterligare hjälp kan du starta ett samtal på den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Du kan också kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för assisterad supportalternativ.
