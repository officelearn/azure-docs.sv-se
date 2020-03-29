---
title: Statistik i realtid i Azure CDN | Microsoft-dokument
description: Realtidsstatistik ger realtidsdata om prestanda för Azure CDN när du levererar innehåll till dina klienter.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593495"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistik i realtid i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Det här dokumentet förklarar statistik i realtid i Microsoft Azure CDN.  Den här funktionen tillhandahåller realtidsdata, till exempel bandbredd, cachestatus och samtidiga anslutningar till CDN-profilen när du levererar innehåll till dina klienter. Detta möjliggör kontinuerlig övervakning av hälsan hos din tjänst när som helst, inklusive go-live-evenemang.

Följande diagram är tillgängliga:

* [Bandbredd](#bandwidth)
* [Statuskoder](#status-codes)
* [Cachestatus](#cache-statuses)
* [Anslutningar](#connections)

## <a name="accessing-real-time-stats"></a>Komma åt statistik i realtid
1. I [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil.
   
    ![Cdn-profilblad](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profilblad](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
3. Håll muspekaren över fliken **Analytics** och hovra sedan över **utfällningen av statistik i realtid.**  Klicka på **HTTP Large Object**.
   
    ![CDN-hanteringsportal](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Statistikdiagrammen i realtid visas.

Var och en av diagrammen visar realtidsstatistik för det valda tidsintervallet, med början när sidan läses in.  Diagrammen uppdateras automatiskt med några sekunders mellanrum.  Knappen **Uppdatera diagram,** om sådan finns, rensar diagrammet, varefter den bara visar de markerade data.

## <a name="bandwidth"></a>Bandbredd
![Diagram över bandbredd](./media/cdn-real-time-stats/cdn-bandwidth.png)

I diagrammet **Bandbredd** visas hur mycket bandbredd som används för den aktuella plattformen under det valda tidsintervallet. Den skuggade delen av diagrammet indikerar bandbreddsanvändning. Den exakta mängden bandbredd som för närvarande används visas direkt under linjediagrammet.

## <a name="status-codes"></a>Statuskoder
![Diagram över statuskod](./media/cdn-real-time-stats/cdn-status-codes.png)

Diagrammet **Statuskoder** anger hur ofta vissa HTTP-svarskoder förekommer under det valda tidsintervallet.

> [!TIP]
> En beskrivning av varje HTTP-statuskodsalternativ finns i [Azure CDN HTTP Status Codes](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

En lista med HTTP-statuskoder visas direkt ovanför diagrammet. Den här listan anger varje statuskod som kan inkluderas i linjediagrammet och det aktuella antalet förekomster per sekund för statuskoden. Som standard visas en rad för var och en av dessa statuskoder i diagrammet. Du kan dock välja att bara övervaka de statuskoder som har särskild betydelse för CDN-konfigurationen. Det gör du genom att kontrollera önskade statuskoder och avmarkera alla andra alternativ och sedan klicka på **Uppdatera diagram**. 

Du kan tillfälligt dölja loggade data för en viss statuskod.  Klicka på den statuskod som du vill dölja i förklaringen direkt under diagrammet. Statuskoden döljs omedelbart från diagrammet. Om du klickar på statuskoden igen visas det alternativet igen.

## <a name="cache-statuses"></a>Cachestatus
![Diagram över cachestatus](./media/cdn-real-time-stats/cdn-cache-status.png)

Diagrammet **Cachestatus** anger hur ofta vissa typer av cachestatusar förekommer under det valda tidsintervallet. 

> [!TIP]
> En beskrivning av varje cachestatuskodsalternativ finns i [Azure CDN Cache Status Codes](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

En lista över cachestatuskoder visas direkt ovanför diagrammet. Den här listan anger varje statuskod som kan inkluderas i linjediagrammet och det aktuella antalet förekomster per sekund för statuskoden. Som standard visas en rad för var och en av dessa statuskoder i diagrammet. Du kan dock välja att bara övervaka de statuskoder som har särskild betydelse för CDN-konfigurationen. Det gör du genom att kontrollera önskade statuskoder och avmarkera alla andra alternativ och sedan klicka på **Uppdatera diagram**. 

Du kan tillfälligt dölja loggade data för en viss statuskod.  Klicka på den statuskod som du vill dölja i förklaringen direkt under diagrammet. Statuskoden döljs omedelbart från diagrammet. Om du klickar på statuskoden igen visas det alternativet igen.

## <a name="connections"></a>Anslutningar
![Diagram över anslutningar](./media/cdn-real-time-stats/cdn-connections.png)

Det här diagrammet visar hur många anslutningar som har upprättats till dina kantservrar. Varje begäran om en tillgång som passerar genom vår CDN resulterar i en anslutning.

## <a name="next-steps"></a>Efterföljande moment
* Få meddelanden med [aviseringar i realtid i Azure CDN](cdn-real-time-alerts.md)
* Djupare med [avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
* Analysera [användningsmönster](cdn-analyze-usage-patterns.md)

