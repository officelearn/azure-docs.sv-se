---
title: "Kontrollera Azure CDN cachelagring av frågesträngar med frågesträngar | Microsoft Docs"
description: "Azure CDN cachelagring av frågesträng styr hur filer ska kunna cachelagras när de innehåller frågesträngar."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8d79626fa8516f226a82d3dac693c2033904c91d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings"></a>Kontrollen Azure CDN cachelagring av frågesträngar med frågesträngar
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium från Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Översikt
Cachelagring av frågesträng styr hur filer ska kunna cachelagras när de innehåller frågesträngar.

> [!IMPORTANT]
> Standard- och Premium CDN produkter ger samma frågesträngen cachelagring funktioner, men användargränssnittet skiljer sig åt.  Det här dokumentet beskriver gränssnittet för **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**.  För att fråga sträng cachelagring med **Azure CDN Premium från Verizon**, se [styr cachelagring beteendet för CDN-begäranden med frågesträngar - Premium](cdn-query-string-premium.md).
> 
> 

Det finns tre lägen:

* **Ignorera frågesträngar**: det här är standardläget.  CDN-kantnod skickar frågesträngen från begäranden till ursprunget på den första begäran och cacheposten tillgången.  Alla efterföljande förfrågningar för den tillgången som hämtas från kantnoden ignoreras frågesträngen tills cachelagrade tillgången upphör att gälla.
* **Kringgå cachelagring för URL med frågesträngar**: I det här läget begäranden med frågesträngar inte cachelagras på kantnod CDN.  Kantnoden hämtar tillgången direkt från ursprunget och skickar den till begäranden med varje begäran.
* **Cachelagra varje unik URL**: det här läget behandlar varje begäran med en frågesträng som en unik tillgång med sin egen cache.  Till exempel svaret från ursprung för en begäran om *foo.ashx?q=bar* skulle cachelagras på kantnoden och returneras för efterföljande med samma fråga strängen.  En begäran om *foo.ashx?q=somethingelse* skulle cachelagras som en separat tillgång med sin egen tid till live.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändra inställningar för standard CDN-profiler för cachelagring av frågesträng
1. CDN-profilbladet Klicka CDN-slutpunkt som du vill hantera.
   
    ![CDN-profilen bladet-slutpunkter](./media/cdn-query-string/cdn-endpoints.png)
   
    CDN-slutpunkten blad öppnas.
2. Klicka på den **konfigurera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-query-string/cdn-config-btn.png)
   
    Konfiguration av CDN-blad öppnas.
3. Välj en inställning i den **cachelagring av frågesträngar** listrutan.
   
    ![CDN-frågesträng cachelagringsalternativ](./media/cdn-query-string/cdn-query-string.png)
4. När du har gjort ditt val klickar du på den **spara** knappen.

> [!IMPORTANT]
> Inställningsändringarna kanske inte syns direkt, eftersom det tar tid för registreringen ska spridas via CDN.  För profiler av typen <b>Azure CDN från Akamai</b> slutförs spridningen vanligtvis inom en minut.  För profiler av typen <b>Azure CDN från Verizon</b> slutförs spridningen vanligtvis inom 90 minuter, men i vissa fall kan det ta längre tid.
> 
> 

