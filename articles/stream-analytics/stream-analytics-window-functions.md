---
title: Introduktion till Azure Stream Analytics fönsterhantering funktioner
description: Den här artikeln beskriver de tre fönsterhantering funktionerna (rullande hopping, glidande) som används i Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: c6f5dbe49cb60e3c7b2bc6562acf2d7fd79096ec
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Introduktion till Stream Analytics-fönstrets funktioner
I många realtid strömning scenarier, är det nödvändigt att utföra åtgärder endast på de data som finns i den temporala windows. Inbyggt stöd för fönsterhantering funktioner är en nyckelfunktion i Azure Stream Analytics som flyttar nålen på utvecklarproduktivitet i authoring komplexa dataströmmen bearbetar jobb. Stream Analytics gör att utvecklare kan använda [ **rullande**](https://msdn.microsoft.com/library/dn835055.aspx), [ **Hopping** ](https://msdn.microsoft.com/library/dn835041.aspx) och [ **glidande** ](https://msdn.microsoft.com/library/dn835051.aspx) windows utföra temporala åtgärder på strömmande data. Det är värt att nämna som alla [fönstret](https://msdn.microsoft.com/library/dn835019.aspx) operations utdata resultat på den **end** i fönstret. Utdata från fönstret blir enskild händelse baserat på mängdfunktionen används. Händelsen har tidsstämpeln för slutet av fönstret och alla Windows-funktioner har definierats med en fast längd. Slutligen är det viktigt att notera att alla Windows-funktioner kan användas i en [ **GROUP BY** ](https://msdn.microsoft.com/library/dn835023.aspx) satsen.

![Stream Analytics fönstret fungerar begrepp](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Rullande fönster
Rullande fönster funktioner som används för att segmentera en dataström i distinkta tid segment och utföra en funktion mot dem, till exempel i exemplet nedan. Viktiga skillnaderna i en rullande fönster är att de upprepar, inte överlappar och en händelse får inte tillhöra mer än en rullande fönster.

![Stream Analytics-fönstrets funktioner rullande introduktion](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hoppande fönster
Hoppande fönster funktioner hopp framåt genom en bestämd tid. Det kan vara enkelt att se dem som rullande fönster som kan överlappa så händelser kan tillhöra mer än en resultatmängd i Hopping-fönstret. Om du vill ha ett Hopping fönster som en rullande ange fönster en bara hoppstorleken för att vara samma som fönsterstorleken. 

![Stream Analytics fönstret fungerar Hoppande introduktion](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Hoppande fönster
Glidande fönstrets funktioner, till skillnad från rullande eller Hopping windows, skapa ett utgående **endast** när en händelse inträffar. Alla fönster har minst en händelse och fönstret flyttar kontinuerligt fram en € (epsilon). Som Hopping Windows kan händelser tillhöra mer än ett glidande fönster.

![Stream Analytics-fönstrets funktioner glidande introduktion](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Få hjälp med att fönstrets funktioner
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

