---
title: Introduktion till Azure Stream Analytics fönsterhantering funktioner
description: Den här artikeln beskriver fyra fönsterhantering funktioner (rullande, hopping, glidande, session) som används i Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 2650058e277bc0338c779655ce381be046fb120a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893658"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introduktion till Stream Analytics fönsterhantering funktioner
Utför åtgärder på data i den temporala windows är vanliga mönstret i tid-direktuppspelning scenarier. Stream Analytics har inbyggt stöd för fönsterhantering funktioner, så att utvecklare kan författare komplexa dataströmmen bearbetar jobb med minsta möjliga ansträngning.

Det finns fyra typer av temporala windows att välja mellan: [ **rullande**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **Hopping**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [  **Glidande**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), och [ **Session** ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) windows.  Du använder Windows-funktioner i den [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) -satsen i frågesyntaxen i Stream Analytics-jobb.

Alla de [fönsterhantering](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) operations utdata resultat på den **end** i fönstret. Utdata från fönstret blir enskild händelse baserat på mängdfunktionen används. Händelsen utdata har tidsstämpeln för slutet av fönstret och alla Windows-funktioner har definierats med en fast längd. 

![Stream Analytics fönstret fungerar begrepp](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Rullande fönster
Rullande fönster funktioner som används för att segmentera en dataström i distinkta tid segment och utföra en funktion mot dem, till exempel i exemplet nedan. Viktiga skillnaderna i en rullande fönster är att de upprepar, inte överlappar varandra och en händelse inte kan hör till mer än en rullande fönster.

![Stream Analytics rullande fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hoppande fönster
Hoppande fönster funktioner hopp framåt genom en bestämd tid. Det kan vara enkelt att se dem som rullande fönster som kan överlappa så händelser kan tillhöra mer än en resultatmängd i Hopping-fönstret. Om du vill göra en Hopping fönstret Ange samma som en rullande fönster hoppstorlek ska vara samma som fönsterstorleken. 

![Stream Analytics Hoppande fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Hoppande fönster
Glidande fönstrets funktioner, till skillnad från rullande eller Hopping windows, skapa ett utgående **endast** när en händelse inträffar. Alla fönster har minst en händelse och fönstret flyttar kontinuerligt fram en € (epsilon). Som hopping windows kan händelser tillhöra mer än en skjutfönster.

![Stream Analytics skjutfönster](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window-preview"></a>Sessionsfönstret (förhandsgranskning)
Sessionen fönstrets funktioner gruppera händelser som kommer till samma tid filtrera tidsperioder, där det finns inga data. Den har tre huvudsakliga parametrar: tidsgräns, tidsgräns och partitionsnyckel (valfritt).

![Stream Analytics session fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Ett fönster för sessionen startar när den första händelsen inträffar. Om en annan händelse inträffar inom den angivna tidsgränsen från den senaste infogade händelsen utökar fönstret för att inkludera den nya händelsen. Annars om inga händelser sker inom tidsgränsen, stängs sedan fönstret vid tidsgränsen.

Om händelser hålla sker inom den angivna tidsgränsen förlängts sessionsfönstret tills maximal varaktighet har uppnåtts. Maximal varaktighet kontrollerar intervall som ska samma storlek som den angivna maximala längden. Till exempel om den maximala längd är 10, kontrollerar om fönstret överskrider maximal varaktighet ska hända vid t = 0, 10, 20, 30, osv.

När en partitionsnyckel har angetts händelserna grupperas tillsammans med nyckeln och sessionsfönstret tillämpas på varje grupp oberoende av varandra. Den här partitionering är användbart i fall där du behöver olika session windows för olika användare eller enheter.


## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

