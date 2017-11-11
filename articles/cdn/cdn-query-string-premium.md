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
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: ba9c28f0e6df25b101b45edf836d0b95056cbc6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Kontrollen Azure Content Delivery Network cachelagring av frågesträngar med frågesträngar - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium från Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN), kan du styra hur cachelagras filer för en webb-begäran som innehåller en frågesträng. I en webbegäran med en frågesträng frågesträngen är den del av den begäran som inträffar efter den `?` tecken. En frågesträng kan innehålla en eller flera parametrar som avgränsas med en `&` tecken. Till exempel `http://www.domain.com/content.mov?data1=true&data2=false`. Om det finns fler än en frågesträngparametern i en begäran, spelar ordningen på parametrarna ingen roll. 

> [!IMPORTANT]
> CDN-produkter standard och premium ger samma frågesträngen cachelagring funktioner, men användargränssnittet är olika.  Den här artikeln beskriver gränssnittet för **Azure CDN Premium från Verizon**. För frågan sträng cachelagring med **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**, se [styr cachelagring beteendet för CDN-begäranden med frågesträngar](cdn-query-string.md).
>

Tre frågan sträng lägen är tillgängliga:

- **standard-cache**: standardläget. I det här läget kantnod CDN överför frågesträngar från begäranden till ursprunget den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från kantnoden Ignorera frågesträngar tills cachelagrade tillgången upphör att gälla.
- **no-cache**: I det här läget begäranden med frågesträngar inte cachelagras på kantnod CDN. Kantnoden hämtar tillgången direkt från ursprunget och skickar den till begäranden med varje begäran.
- **Unik cache**: I det här läget varje förfrågan med en unik URL, inklusive frågesträngen behandlas som en unik tillgång med sin egen cache. Till exempel svaret från ursprung för en begäran om `example.ashx?q=test1` är cachelagras på kantnoden och returneras för efterföljande med samma frågesträngen. En begäran om `example.ashx?q=test2` cachelagras som en separat tillgång med sin egen time to live-inställningen.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ändra inställningar för premium CDN profiler för cachelagring av frågesträng
1. Öppna en CDN-profil och klicka sedan på **hantera**.
   
    ![CDN-profilen hantera knappen](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **HTTP stora** och klicka sedan hovra över den **cacheinställningarna** utfälld meny. Klicka på **cachelagring av frågesträng**.
   
    Frågesträng cachelagringsalternativ visas.
   
    ![CDN-frågesträng cachelagringsalternativ](./media/cdn-query-string-premium/cdn-query-string.png)
3. Välj ett sträng-frågeläge och klicka sedan på **uppdatering**.

> [!IMPORTANT]
> Eftersom det tar tid för registreringen ska spridas via CDN kanske cache sträng ändringarna inte visas omedelbart. För **Azure CDN Premium från Verizon** profiler, spridningen vanligtvis har slutförts inom 90 minuter, men i vissa fall kan ta längre tid.
 

