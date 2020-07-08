---
title: Introduktion till Azure Stream Analytics fönster funktioner
description: I den här artikeln beskrivs fyra fönster funktioner (rullande, hoppande, glidning, session) som används i Azure Stream Analytics-jobb.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 872eec62e7a629d76533aa6c9906cbdb64c32236
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80745554"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introduktion till Stream Analytics fönster funktioner

I tids strömnings scenarier är det ett vanligt mönster att utföra åtgärder på data som finns i temporala fönster. Stream Analytics har inbyggt stöd för fönster funktioner, vilket gör det möjligt för utvecklare att skapa komplexa data bearbetnings jobb med minimal ansträngning.

Det finns fyra typer av temporala fönster att välja mellan: [**rullande**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**hoppande**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**glidande**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)och [**session**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) Windows.  Du använder fönster funktionerna i [**Group by**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) -satsen i frågesyntaxen i Stream Analytics jobben. Du kan också aggregera händelser över flera fönster med funktionen [ **Windows ()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Alla [fönster](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) åtgärdar resultatet i **slutet** av fönstret. Utdata från fönstret är en enskild händelse baserad på den mängd funktion som används. Händelsen utdata kommer att ha tidstämpeln för slutet av fönstret och alla fönster funktioner definieras med en fast längd. 

![Stream Analyticss fönster funktions metoder](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Rullande-fönster
Rullande fönster funktioner används för att segmentera en data ström i olika tids segment och utföra en funktion mot dem, till exempel i exemplet nedan. Viktiga egenskaper i ett rullande fönster är att de upprepas i stället för att överlappa varandra, samt att en händelse inte kan tillhöra mer än ett rullande fönster.

![Stream Analytics rullande-fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hoppande-fönster
Hoppande fönster hoppar framåt i tid med en fast period. Det kan vara enklare om vi tänker att de är rullande fönster som kan överlappa, vilket innebär att händelser kan tillhöra mer än ett hoppande fönster. Om du vill göra ett hoppande-fönster detsamma som ett rullande-fönster anger du att hopp storleken ska vara samma som fönstrets storlek. 

![Stream Analytics hoppande-fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Glidande fönster
Glidande fönster funktioner, till skillnad från rullande-eller hoppande-fönster, genererar **endast** utdata när en händelse inträffar. Varje fönster kommer att ha minst en händelse och fönstret flyttas kontinuerligt framåt med en ε (Epsilon). Precis som i hoppande fönster kan händelser tillhöra mer än ett skjutfönster.

![Stream Analytics glidande fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Session-fönster
Session-fönstret funktions grupperar händelser som kommer vid liknande tidpunkter, filtrering av tids perioder där det inte finns några data. Den har tre huvudsakliga parametrar: timeout, maximal tidslängd och partitioneringsnyckel (valfritt).

![Fönstret Stream Analytics session](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Ett sessions fönster börjar när den första händelsen inträffar. Om en annan händelse inträffar inom den angivna tids gränsen från den senaste inmatade händelsen, utökas fönstret så att det innehåller den nya händelsen. Annars stängs fönstret vid tids gränsen om inga händelser inträffar inom tids gränsen.

Om händelserna fortsätter inom den angivna tids gränsen, fortsätter sessions fönstret att utökas tills den maximala varaktigheten uppnås. De maximala intervallet för varaktighets kontroll har angetts till samma storlek som den angivna max längden. Om den maximala varaktigheten till exempel är 10, kommer kontrollen på om fönstret överskrider den maximala varaktigheten att inträffa vid t = 0, 10, 20, 30 osv.

När du har angett en partitionsnyckel grupperas händelserna tillsammans av fönstret för nyckel-och-session-fönstret, som tillämpas på varje grupp oberoende av varandra. Denna partitionering är användbar i fall där du behöver olika sessions-fönster för olika användare eller enheter.


## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

