---
title: Introduktion till fönsterfunktioner i Azure Stream Analytics
description: I den här artikeln beskrivs fyra fönsterfunktioner (tumlande, hoppande, glidande, session) som används i Azure Stream Analytics-jobb.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369430"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introduktion till fönsterfunktioner i Stream Analytics

I tidsströmningsscenarier är det ett vanligt mönster att utföra åtgärder på data som finns i tidsfönster. Stream Analytics har inbyggt stöd för fönsterfunktioner, vilket gör det möjligt för utvecklare att skapa komplexa dataflödesbearbetningsjobb med minimal ansträngning.

Det finns fyra typer av tidsfönster att välja mellan: [**Tumlande,**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) [**Hopping,**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics) [**Sliding**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)och [**Session**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) fönster.  Du använder fönsterfunktionerna i [**SATS GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) för frågesyntaxen i dina Stream Analytics-jobb. Du kan också aggregera händelser över flera fönster med hjälp av funktionen [ **Windows().** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)

Alla [fönsteroperationer](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) utdata resultat i **slutet** av fönstret. Utdata från fönstret kommer att vara en enda händelse baserat på den mängdfunktion som används. Utdatahändelsen kommer att ha tidsstämpeln i slutet av fönstret och alla fönsterfunktioner definieras med en fast längd. 

![Begrepp för fönsterfunktioner i Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Tumlande fönster
Tumbling fönsterfunktioner används för att segmentera en dataström till olika tidssegment och utföra en funktion mot dem, till exempel exemplet nedan. Viktiga egenskaper i ett rullande fönster är att de upprepas i stället för att överlappa varandra, samt att en händelse inte kan tillhöra mer än ett rullande fönster.

![Tumlande fönster för Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hoppande fönster
Hoppande fönster hoppar framåt i tid med en fast period. Det kan vara enklare om vi tänker att de är rullande fönster som kan överlappa, vilket innebär att händelser kan tillhöra mer än ett hoppande fönster. Om du vill göra ett hoppningsfönster samma som ett Tumbling-fönster anger du att hoppstorleken ska vara densamma som fönsterstorleken. 

![Hoppfönster för Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Skjutfönster
Skjutbara fönsterfunktioner, till skillnad från Tumbling- eller Hopping-fönster, ger **bara** en utdata när en händelse inträffar. Varje fönster innehåller minst en händelse och fönstret flyttas kontinuerligt framåt med en epsilon (€). Precis som i hoppande fönster kan händelser tillhöra mer än ett skjutfönster.

![Skjutfönster för Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Sessionsfönster
Sessionsfönsterfunktioner grupphändelser som anländer vid liknande tidpunkter och filtrerar bort tidsperioder där det inte finns några data. Den har tre huvudsakliga parametrar: timeout, maximal tidslängd och partitioneringsnyckel (valfritt).

![Fönstret Stream Analytics-session](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Ett sessionsfönster börjar när den första händelsen inträffar. Om en annan händelse inträffar inom den angivna tidsgränsen från den senast intjesterade händelsen, utökas fönstret till att omfatta den nya händelsen. Annars om inga händelser inträffar inom timeouten stängs fönstret vid tidsgränsen.

Om händelser fortsätter att inträffa inom den angivna tidsgränsen fortsätter sessionsfönstret att förlängas tills den maximala varaktigheten har uppnåtts. De maximala varaktighetskontrollintervallen är inställda på att vara samma storlek som den angivna maxtiden. Till exempel, om max varaktigheten är 10, då kontrollerna på om fönstret överstiger maximal varaktighet kommer att ske vid t = 0, 10, 20, 30, etc.

När en partitionsnyckel anges grupperas händelserna tillsammans med nyckeln och sessionsfönstret tillämpas på varje grupp oberoende av varandra. Den här partitioneringen är användbar för fall där du behöver olika sessionsfönster för olika användare eller enheter.


## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

