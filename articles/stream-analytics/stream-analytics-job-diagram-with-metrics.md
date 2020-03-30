---
title: Datadriven felsökning i Azure Stream Analytics
description: I den här artikeln beskrivs felsÃ¶kning av ditt Azure Stream Analytics-jobb med hjälp av jobbdiagrammet och måtten i Azure-portalen.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426079"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Datadriven felsökning med hjälp av jobbdiagrammet

Jobbdiagrammet på **övervakningsbladet** i Azure-portalen kan hjälpa dig att visualisera din jobbpipeline. Den visar steg för indata, utdata och frågor. Du kan använda jobbdiagrammet för att undersöka mätvärden för varje steg så att du snabbt kan isolera källan till ett problem när du felsöker.

## <a name="using-the-job-diagram"></a>Använda jobbdiagrammet

I Azure-portalen, medan du är i ett Stream Analytics-jobb under **SUPPORT + FELSÖKNING,** väljer du **Jobbdiagram:**

![Jobbdiagram med mått - plats](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Markera varje frågesteg om du vill visa motsvarande avsnitt i ett frågeredigeringsfönster. Ett måttdiagram för steget visas i ett nedre fönster på sidan.

![Jobbdiagram med mått - grundläggande jobb](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Om du vill visa partitioner för indata för Azure Event Hubs väljer du **. . .** En snabbmeny visas. Du kan också se sammanslagningen av indata.

![Jobbdiagram med mått - expandera partition](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Om du bara vill visa måttdiagrammet för en enskild partition markerar du partitionsnoden. Måtten visas längst ned på sidan.

![Jobbdiagram med mått - fler mått](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Om du vill visa måttdiagrammet för en sammanslagning väljer du fusionsnoden. Följande diagram visar att inga händelser har tappats eller justerats.

![Jobbdiagram med mått - rutnät](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Om du vill se information om måttvärdet och mättiden pekar du på diagrammet.

![Jobbdiagram med mått - hovra](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Felsöka med hjälp av mått

**QueryLastProcessedTime-måttet** anger när ett visst steg tog emot data. Genom att titta på topologin kan du arbeta bakåt från utdataprocessorn för att se vilket steg som inte tar emot data. Om ett steg inte hämtar data går du till frågesteget precis innan det. Kontrollera om föregående frågesteg har ett tidsfönster och om det har gått tillräckligt med tid för att mata ut data. (Observera att tidsfönster fästs till timmen.)
 
Om föregående frågesteg är en indataprocessor använder du indatamåtten för att svara på följande riktade frågor. De kan hjälpa dig att avgöra om ett jobb hämtar data från dess indatakällor. Granska varje partition om frågan är partitionerad.
 
### <a name="how-much-data-is-being-read"></a>Hur mycket data läss?

*   **InputEventsSourcesTotal** är antalet dataenheter som läss. Till exempel antalet blobbar.
*   **InputEventsTotal** är antalet läsningar av händelser. Det här måttet är tillgängligt per partition.
*   **InputEventsInBytesTotal** är antalet lästa byte.
*   **InputEventsLastArrivalTime** uppdateras med varje mottagen händelse enqueued tid.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Går tiden framåt? Om faktiska händelser läses kanske inte skiljetecken utfärdas.

*   **InputEventsLastPunctuationTime** anger när ett skiljetecken utfärdades för att se till att tiden går framåt. Om interpunktion inte utfärdas kan dataflödet blockeras.
 
### <a name="are-there-any-errors-in-the-input"></a>Finns det några fel i indata?

*   **InputEventsEventDataNullTotal** är ett antal händelser som har null-data.
*   **InputEventsSerializerErrorsTotal** är ett antal händelser som inte kunde deserialiseras korrekt.
*   **InputEventsDegradedTotal** är ett antal händelser som hade ett annat problem än med deserialisering.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Är händelser som tappas eller justeras?

*   **InputEventsEarlyTotal** är antalet händelser som har en programtidsstämpel före den höga vattenstämpeln.
*   **InputEventsLateTotal** är antalet händelser som har en programtidsstämpel efter den höga vattenstämpeln.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** är antalet händelser som har tagits bort före jobbets starttid.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Halkar vi efter när det gäller att läsa data?

*   **Indatahändelser Eftersläpade (Totalt)** visar hur många fler meddelanden som behöver läsas för eventhubbar och Azure IoT Hub-indata. När det här numret är större än 0 betyder det att ditt jobb inte kan bearbeta data så snabbt som det kommer in. I det här fallet kan du behöva öka antalet strömningsenheter och/eller se till att jobbet kan parallelliseras. Du kan se mer information om detta på [sidan frågeparallellisering](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Få hjälp
Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Webbplatsreferens för Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST-api-referens för Strömanalyshantering](https://msdn.microsoft.com/library/azure/dn835031.aspx)
