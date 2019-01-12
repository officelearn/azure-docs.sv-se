---
title: Introduktion till Azure Stream Analytics-fönsterfunktioner
description: Den här artikeln beskriver fyra fönsterfunktioner (rullande, hoppar, glidande, session) som används i Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 04c19e7e51777db4c59bfab3d5a8a7598560556a
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231656"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introduktion till Stream Analytics-fönsterfunktioner
Tid för direktuppspelning scenarier är utföra åtgärder på data i den temporala windows ett vanligt mönster. Stream Analytics har inbyggt stöd för fönsterfunktioner, så att utvecklare kan skapa komplexa strömbearbetning jobb med minimal ansträngning.

Det finns fyra typer av temporala windows att välja mellan: [**Rullande**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **hoppar**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [ **glidande**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), och [ **Session**  ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) windows.  Du använder fönstret funktionerna i den [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) satsen på frågesyntax i Stream Analytics-jobb.

Alla de [fönsterhantering](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) operations utdataresultat på den **slutet** i fönstret. Utdata från fönstret blir enskild händelse baserat på mängdfunktionen används. Utdata-händelsen kommer att ha tidsstämpeln för slutet av fönstret och alla fönsterfunktioner definieras med en fast längd. 

![Koncept för Stream Analytics-fönstret-funktioner](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Rullande fönster
Rullande fönster funktioner används för att dela en dataström i distinkta tid segment och utför en funktion mot dem, som i exemplet nedan. Viktiga skillnaderna i ett rullande fönster är att de upprepar, inte överlappar varandra och en händelse inte kan hör till mer än en utlösare för rullande fönster.

![Stream Analytics rullande fönster](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hoppande fönster
Hoppande fönster functions hopp framåt genom en fast period. Det kan vara enkelt att se dem som rullande fönster som kan överlappa, så händelser kan tillhöra mer än en resultatmängd i Hopping-fönstret. Om du vill göra ett Hopping fönster ange samma som ett rullande fönster hoppstorlek ska vara detsamma som att tidsperioden. 

![Hoppande fönster för Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Hoppande fönster
Glidande fönsterfunktioner, till skillnad från rullande eller hoppar windows kan producera utdata **endast** när en händelse inträffar. Alla fönster har minst en händelse och fönstret flyttar kontinuerligt framåt en € (epsilon). Händelser kan höra till flera Hoppande fönster som hoppar windows.

![Hoppande fönster för Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Sessionsfönstret
Sessionen fönsterfunktioner gruppera händelser som kommer vid samma tid filtrerar ut tidsperioder där det finns inga data. Den har tre huvudsakliga parametrar: timeout, Max varaktighet och partitionsnyckel (valfritt).

![Sessionsfönstret för Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

En sessionsfönstret börjar när den första händelsen inträffar. Om en annan händelse inträffar inom den angivna tidsgränsen från den senaste insamlade händelsen utökar fönstret för att inkludera den nya händelsen. Annars om inga händelser inträffar inom tidsgränsen kan stängs sedan fönstret när tidsgränsen.

Om händelser hålla inträffar inom den angivna tidsgränsen kan förlängts sessionsfönstret tills maximal varaktighet har uppnåtts. Maximal varaktighet kontrollerar intervall är inställda på att ha samma storlek som den angivna maximala längden. Exempelvis om maximala längd är 10, kontrollerar om fönstret överskrider maximal varaktighet ska utföras vid t = 0, 10, 20, 30, osv.

När en partitionsnyckel anges händelserna grupperas tillsammans med nyckeln och sessionsfönstret tillämpas på varje grupp oberoende av varandra. Den här partitionering är användbart för fall där du behöver annan session windows för olika användare eller enheter.


## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

