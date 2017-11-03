---
title: "Kontrollera Azure CDN cachelagring av frågesträngar med frågesträngar - Premium | Microsoft Docs"
description: "Azure CDN cachelagring av frågesträng styr hur filer ska kunna cachelagras när de innehåller frågesträngar."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>Kontrollen Azure CDN cachelagring av frågesträngar med frågesträngar - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium från Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Översikt
Cachelagring av frågesträng styr hur filer ska kunna cachelagras när de innehåller frågesträngar.

> [!IMPORTANT]
> Standard- och Premium CDN produkter ger samma frågesträngen cachelagring funktioner, men användargränssnittet skiljer sig åt.  Det här dokumentet beskriver gränssnittet för **Azure CDN Premium från Verizon**.  För frågan sträng cachelagring med **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**, se [styr cachelagring beteendet för CDN-begäranden med frågesträngar](cdn-query-string.md).
> 
> 

Det finns tre lägen:

* **standard-cache**: det här är standardläget.  CDN-kantnod skickar frågesträngen från begäranden till ursprunget på den första begäran och cacheposten tillgången.  Alla efterföljande förfrågningar för den tillgången som hämtas från kantnoden ignoreras frågesträngen tills cachelagrade tillgången upphör att gälla.
* **no-cache**: I det här läget begäranden med frågesträngar inte cachelagras på kantnod CDN.  Kantnoden hämtar tillgången direkt från ursprunget och skickar den till begäranden med varje begäran.
* **Unik cache**: det här läget behandlar varje begäran med en frågesträng som en unik tillgång med sin egen cache.  Till exempel svaret från ursprung för en begäran om *foo.ashx?q=bar* skulle cachelagras på kantnoden och returneras för efterföljande med samma fråga strängen.  En begäran om *foo.ashx?q=somethingelse* skulle cachelagras som en separat tillgång med sin egen tid till live.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ändra inställningar för premium CDN profiler för cachelagring av frågesträng
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **HTTP stora** och klicka sedan hovra över den **cacheinställningarna** utfällbar.  Klicka på **cachelagring av frågesträng**.
   
    Frågesträng cachelagringsalternativ visas.
   
    ![CDN-frågesträng cachelagringsalternativ](./media/cdn-query-string-premium/cdn-query-string.png)
3. När du har gjort ditt val klickar du på den **uppdatering** knappen.

> [!IMPORTANT]
> Inställningsändringarna kanske inte syns direkt, eftersom det tar tid för registreringen ska spridas via CDN.  För profiler av typen <b>Azure CDN från Verizon</b> slutförs spridningen vanligtvis inom 90 minuter, men i vissa fall kan det ta längre tid.
> 
> 

