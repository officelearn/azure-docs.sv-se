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
ms.openlocfilehash: 2021b5b7602605a7c264e9cd575399077691da34
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Kontrollen Azure Content Delivery Network cachelagring av frågesträngar med frågesträngar - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium från Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN), kan du styra hur cachelagras filer för en webb-begäran som innehåller en frågesträng. Frågesträngen är den del av den begäran som inträffar efter ett frågetecken (?) i en webbegäran med en frågesträng. En frågesträng kan innehålla en eller flera nyckel / värde-par, där namnet och dess värde är åtskilda av ett likhetstecken (=). Varje nyckel / värde-par avgränsas med ett et-tecken (&). Till exempel `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, roll deras inbördes ordning ingen. 

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
 

