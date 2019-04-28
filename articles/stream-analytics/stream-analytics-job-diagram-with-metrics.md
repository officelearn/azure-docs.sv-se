---
title: Datadrivna felsökning i Azure Stream Analytics
description: Den här artikeln beskriver hur du felsöker ditt Azure Stream Analytics-jobb med hjälp av jobbdiagram och mått i Azure-portalen.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 4a6d359b27b9a2e52d71ed5f8547041645147605
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479974"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Datadrivna felsökning med hjälp av jobbdiagrammet

Jobbdiagram på den **övervakning** bladet i Azure-portalen kan hjälpa dig att visualisera din pipeline för jobbet. Den visar steg för indata, utdata och frågor. Du kan använda jobbdiagrammet för att undersöka mätvärden för varje steg så att du snabbt kan isolera källan till ett problem när du felsöker.

## <a name="using-the-job-diagram"></a>Med hjälp av jobbdiagrammet

I Azure-portalen när den är i ett Stream Analytics-jobb **stöd + felsökning**väljer **jobbdiagram**:

![Jobbdiagram med mått – plats](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Välj varje frågesteg till finns i motsvarande avsnitt i en fråga som Redigera fönstret. Ett måttdiagram för steg visas i en nedre rutan på sidan.

![Jobbdiagram med mått – grundläggande jobb](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Om du vill se partitionerna i Azure Event Hubs-indata **...** En snabbmeny visas. Du kan också se inkommande sammanslagning.

![Jobbdiagram med mått – Utöka partition](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Välj partitionsnoden om du vill se måttdiagram för endast en partition. Mått som visas längst ned på sidan.

![Jobbdiagram med mått – mått](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Om du vill se måttdiagram för en fusion, väljer du noden sammanslagning. Följande diagram visar att inga händelser har tappade eller justerade.

![Jobbdiagram med mått – rutnät](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Om du vill se information om måttvärde och tid, pekar du på diagrammet.

![Jobb-diagram med mått – hovra](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Felsöka med mått

Den **QueryLastProcessedTime** måttet indikerar när ett specifikt steg tar emot data. Genom att titta på topologin, kan du arbeta bakåt från utdataprocessorn för att se vilka steg tar inte emot data. Om ett steg inte hämtar data, gå till frågesteg innan. Kontrollera om det föregående steget i fråga har ett tidsintervall och om tillräckligt med tid har förflutit för att mata ut data. (Observera att den tiden windows är delas upp i timmar.)
 
Om föregående frågesteg är en ingångsprocessor Använd indatamått för att få svar på följande riktade frågor. De kan hjälpa dig avgöra om ett jobb får data från sina inmatningskällor. Granska varje partition om frågan är partitionerad.
 
### <a name="how-much-data-is-being-read"></a>Hur mycket data läses?

*   **InputEventsSourcesTotal** är antalet enheter läsa. Till exempel antal blobbar.
*   **InputEventsTotal** är antalet lästa händelser. Det här måttet är tillgängligt per partition.
*   **InputEventsInBytesTotal** är antalet lästa byte.
*   **InputEventsLastArrivalTime** uppdateras med varje mottagen händelses Kötid.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Rör sig tiden framåt? Om faktiska händelser läses kanske inte skiljetecken utfärdas.

*   **InputEventsLastPunctuationTime** anger när ett skiljetecken utfärdades för att se till att tiden går framåt. Om skiljetecken inte utfärdas kan dataflöde blockeras.
 
### <a name="are-there-any-errors-in-the-input"></a>Finns det några fel i indata?

*   **InputEventsEventDataNullTotal** är ett antal händelser som har null-data.
*   **InputEventsSerializerErrorsTotal** är ett antal händelser som inte kunde deserialiseras korrekt.
*   **InputEventsDegradedTotal** antal händelser som har haft problem än med deserialisering.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Är att tappade eller justerade händelser?

*   **InputEventsEarlyTotal** är antalet händelser som har en Programtidsstämpel före högvattenmärket.
*   **InputEventsLateTotal** är antalet händelser som har en Programtidsstämpel efter högvattenmärket.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** är antalet händelser tas bort innan jobbets starttid.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Vi ordentlig summa läsning av data?

*   **Ange eftersläpande händelser (totalt)** berättas hur många fler meddelanden som måste läsas för Event Hubs och Azure IoT Hub-indata. När det här antalet är större än 0, innebär det att jobbet inte kan bearbeta data så fort det kommande i. I det här fallet kan du behöva öka antalet enheter för strömning och/eller kontrollera att jobbet kan parallelliseras. Du kan se mer information om detta på den [fråga parallellisering sidan](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Få hjälp
För ytterligare hjälp prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
