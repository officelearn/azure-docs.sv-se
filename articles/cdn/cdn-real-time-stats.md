---
title: Realtid statistik i Azure CDN | Microsoft Docs
description: "Realtid statistik ger data i realtid om Azure CDN prestanda när leverera innehåll till dina klienter."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Realtid statistik i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Det här dokumentet förklarar realtid statistik i Microsoft Azure CDN.  Den här funktionen ger realtidsdata, till exempel bandbredd, cache status och samtidiga anslutningar till CDN-profilen när leverera innehåll till dina klienter. Detta gör att kontinuerlig övervakning av hälsotillståndet för din tjänst när som helst, inklusive go live-händelser.

Följande diagram är tillgängliga:

* [Bandbredd](#bandwidth)
* [Statuskoder](#status-codes)
* [Cache-status](#cache-statuses)
* [Anslutningar](#connections)

## <a name="accessing-real-time-stats"></a>Åtkomst till realtid statistik
1. I den [Azure Portal](https://portal.azure.com), bläddra till CDN-profilen.
   
    ![CDN-profilbladet](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
3. Hovra över den **Analytics** och klicka sedan hovra över den **realtid Stats** utfällbar.  Klicka på **HTTP stort objekt**.
   
    ![CDN-hanteringsportalen](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Realtid stats diagram visas.

Varje diagram visar realtid statistik för det valda tidsintervallet startar när sidan läses in.  Diagrammen uppdateras automatiskt varje några sekunder.  Den **uppdatera diagrammet** knappen, om den finns tar bort diagrammet efter vilken visas endast valda data.

## <a name="bandwidth"></a>Bandbredd
![Bandbredd-diagram](./media/cdn-real-time-stats/cdn-bandwidth.png)

Den **bandbredd** diagram visar mängden bandbredd som används för den aktuella plattformen över det valda tidsintervallet. Skuggade delen av diagrammet anger bandbreddsanvändning. Den exakta mängden bandbredd som används visas direkt under linjediagrammet.

## <a name="status-codes"></a>Statuskoder
![Diagram för status-kod](./media/cdn-real-time-stats/cdn-status-codes.png)

Den **statuskoder** diagram visar hur ofta vissa HTTP-svarskoder sker via det valda tidsintervallet.

> [!TIP]
> En beskrivning av varje alternativ för HTTP-status koden finns [Azure CDN HTTP-statuskoder](https://msdn.microsoft.com/library/mt759238.aspx).
> 
> 

En lista över HTTP-statuskoder visas direkt ovanför diagrammet. Den här listan visar varje statuskod som kan ingå i linjediagrammet och det aktuella antalet förekomster per sekund för denna statuskod. Som standard visas en rad för var och en av dessa statuskoder i diagrammet. Du kan dock välja att endast övervaka statuskoder som har särskild betydelse för din CDN-konfiguration. Om du vill göra detta, markera önskad statuskoder och avmarkera alla andra alternativ och klicka på **uppdatera diagrammet**. 

Du kan dölja loggdata för en viss statuskod tillfälligt.  Klicka på statuskod som du vill dölja förklaringen direkt under diagrammet. Statuskoden ska vara dolda direkt från diagrammet. Om du klickar på den statuskoden igen kommer alternativet för att visas igen.

## <a name="cache-statuses"></a>Cache-status
![Diagram för cache-status](./media/cdn-real-time-stats/cdn-cache-status.png)

Den **Cache statusar** diagram visar hur ofta vissa typer av cache-status sker via det valda tidsintervallet. 

> [!TIP]
> En beskrivning av varje cache status kod alternativ finns [Azure CDN Cache-statuskoder](https://msdn.microsoft.com/library/mt759237.aspx).
> 
> 

En lista över cache statuskoder visas direkt ovanför diagrammet. Den här listan visar varje statuskod som kan ingå i linjediagrammet och det aktuella antalet förekomster per sekund för denna statuskod. Som standard visas en rad för var och en av dessa statuskoder i diagrammet. Du kan dock välja att endast övervaka statuskoder som har särskild betydelse för din CDN-konfiguration. Om du vill göra detta, markera önskad statuskoder och avmarkera alla andra alternativ och klicka på **uppdatera diagrammet**. 

Du kan dölja loggdata för en viss statuskod tillfälligt.  Klicka på statuskod som du vill dölja förklaringen direkt under diagrammet. Statuskoden ska vara dolda direkt från diagrammet. Om du klickar på den statuskoden igen kommer alternativet för att visas igen.

## <a name="connections"></a>Anslutningar
![Anslutningar diagram](./media/cdn-real-time-stats/cdn-connections.png)

Det här diagrammet visar hur många anslutningar har upprättats till edge-servrar. Varje begäran för en tillgång som passerar genom våra CDN resultat i en anslutning.

## <a name="next-steps"></a>Nästa steg
* Håll dig informerad med [aviseringar i realtid i Azure CDN](cdn-real-time-alerts.md)
* Gräva djupare med [avancerade http-rapporter](cdn-advanced-http-reports.md)
* Analysera [användningsmönster](cdn-analyze-usage-patterns.md)

