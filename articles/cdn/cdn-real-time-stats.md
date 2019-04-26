---
title: Realtidsstatistik i Azure CDN | Microsoft Docs
description: Statistik i realtid ger data om prestanda för Azure CDN i realtid när du levererar innehåll till dina klienter.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: eb20630533735fb46ea7743be75448329281938a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334627"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Realtidsstatistik i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Det här dokumentet beskriver realtidsstatistik i Microsoft Azure CDN.  Den här funktionen tillhandahåller realtidsdata, t.ex bandbredd, cachelagringsstatus och samtidiga anslutningar till din CDN-profil när du levererar innehåll till dina klienter. På så sätt kan du kontinuerlig övervakning av hälsotillståndet för din tjänst när som helst, inklusive go live-händelser.

I följande diagram är tillgängliga:

* [Bandwidth](#bandwidth)
* [Statuskoder](#status-codes)
* [Status för cachelagring](#cache-statuses)
* [Anslutningar](#connections)

## <a name="accessing-real-time-stats"></a>Åtkomst till realtidsstatistik
1. I den [Azure-portalen](https://portal.azure.com), bläddra till din CDN-profil.
   
    ![CDN-profilbladet](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
3. Hovra över den **Analytics** och sedan hovra över den **statistik i realtid** utfällbar meny.  Klicka på **LOB-HTTP-**.
   
    ![CDN-hanteringsportalen](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Realtidsstatistik diagram visas.

Var och en av diagrammen visar statistik i realtid för det valda tidsintervall som startar när sidan läses in.  Diagrammen uppdateras automatiskt några sekunders mellanrum.  Den **uppdatera Graph** knapp, om sådan finns, rensar i diagrammet, därefter visas endast valda data.

## <a name="bandwidth"></a>Bandbredd
![Bandbredd graph](./media/cdn-real-time-stats/cdn-bandwidth.png)

Den **bandbredd** diagrammet visar mängden bandbredd som används för den aktuella plattformen via det valda tidsintervallet. Den skuggade delen av diagrammet anger bandbreddsanvändning. Den exakta mängden bandbredd som används för tillfället visas direkt under linjediagrammet.

## <a name="status-codes"></a>Statuskoder
![Kod statusdiagram](./media/cdn-real-time-stats/cdn-status-codes.png)

Den **statuskoder** graph anger hur ofta vissa HTTP-svarskoder sker via det valda tidsintervallet.

> [!TIP]
> En beskrivning av varje alternativ för HTTP-status code finns i [Azure CDN HTTP-statuskoder](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

En lista över HTTP-statuskoder visas direkt ovanför diagrammet. Den här listan anger varje statuskod som kan tas med i linjediagrammet och det aktuella antalet förekomster per sekund för den statuskoden. Som standard visas en rad för var och en av dessa statuskoder i diagrammet. Du kan dock välja att endast övervaka statuskoder som har särskild betydelse för din CDN-konfiguration. Om du vill göra detta måste markera de önskade statuskoder och avmarkera alla andra alternativ och klicka på **uppdatera Graph**. 

Du kan dölja loggade data för en viss statuskod tillfälligt.  Klicka på den statuskod som du vill dölja i förklaringen nedan i diagrammet. Statuskoden ska vara dolda direkt från diagrammet. Om du klickar på den statuskoden igen kommer det alternativet som ska visas igen.

## <a name="cache-statuses"></a>Status för cachelagring
![Cachelagra statusar graph](./media/cdn-real-time-stats/cdn-cache-status.png)

Den **Cachelagringsstatus** graph anger hur ofta vissa typer av cachelagring sker via det valda tidsintervallet. 

> [!TIP]
> En beskrivning av varje kod statusalternativet cache finns i [statuskoder för Azure CDN-cacheminnet](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

En lista över cache statuskoder visas direkt ovanför diagrammet. Den här listan anger varje statuskod som kan tas med i linjediagrammet och det aktuella antalet förekomster per sekund för den statuskoden. Som standard visas en rad för var och en av dessa statuskoder i diagrammet. Du kan dock välja att endast övervaka statuskoder som har särskild betydelse för din CDN-konfiguration. Om du vill göra detta måste markera de önskade statuskoder och avmarkera alla andra alternativ och klicka på **uppdatera Graph**. 

Du kan dölja loggade data för en viss statuskod tillfälligt.  Klicka på den statuskod som du vill dölja i förklaringen nedan i diagrammet. Statuskoden ska vara dolda direkt från diagrammet. Om du klickar på den statuskoden igen kommer det alternativet som ska visas igen.

## <a name="connections"></a>Anslutningar
![Anslutningar graph](./media/cdn-real-time-stats/cdn-connections.png)

Det här diagrammet visar hur många anslutningar har upprättats till edgeservrar. Varje begäran för en tillgång som passerar genom våra CDN-resultat i en anslutning.

## <a name="next-steps"></a>Nästa steg
* Håll dig informerad med [aviseringar i realtid i Azure CDN](cdn-real-time-alerts.md)
* Gräv djupare med [avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
* Analysera [användningsmönster](cdn-analyze-usage-patterns.md)

