---
title: "Kontrollera Azure Content Delivery Network cachelagring av frågesträngar med frågesträngar | Microsoft Docs"
description: "Azure CDN cachelagring av frågesträng styr hur filer cachelagras när de innehåller frågesträngar."
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
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 9ffd05a0eb4d976dc40a1c5d45fd22ebf9bd4db1
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Kontrollen Azure Content Delivery Network cachelagring av frågesträngar med frågesträngar
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium från Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN), kan du styra hur cachelagras filer för en webb-begäran som innehåller en frågesträng. Frågesträngen är den del av den begäran som inträffar efter ett frågetecken (?) i en webbegäran med en frågesträng. En frågesträng kan innehålla en eller flera nyckel / värde-par, där namnet och dess värde är åtskilda av ett likhetstecken (=). Varje nyckel / värde-par avgränsas med ett et-tecken (&). Till exempel `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, roll deras inbördes ordning ingen. 

> [!IMPORTANT]
> CDN-produkter standard och premium ger samma frågesträngen cachelagring funktioner, men användargränssnittet är olika.  Den här artikeln beskriver gränssnittet för **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**. För att fråga sträng cachelagring med **Azure CDN Premium från Verizon**, se [styr cachelagring beteendet för CDN-begäranden med frågesträngar - Premium](cdn-query-string-premium.md).

Tre frågan sträng lägen är tillgängliga:

- **Ignorera frågesträngar**: standardläget. I det här läget kantnod CDN överför frågesträngar från begäranden till ursprunget den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från kantnoden Ignorera frågesträngar tills cachelagrade tillgången upphör att gälla.
- **Kringgå cachelagring för frågesträngar**: I det här läget begäranden med frågesträngar inte cachelagras på kantnod CDN. Kantnoden hämtar tillgången direkt från ursprunget och skickar den till begäranden med varje begäran.
- **Cachelagra varje unik URL**: I det här läget varje förfrågan med en unik URL, inklusive frågesträngen behandlas som en unik tillgång med sin egen cache. Till exempel svaret från ursprung för en begäran om `example.ashx?q=test1` är cachelagras på kantnoden och returneras för efterföljande med samma frågesträngen. En begäran om `example.ashx?q=test2` cachelagras som en separat tillgång med sin egen time to live-inställningen.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändra inställningar för standard CDN-profiler för cachelagring av frågesträng
1. Öppna en CDN-profil, och välj sedan CDN-slutpunkt som du vill hantera.
   
   ![CDN-profil-slutpunkter](./media/cdn-query-string/cdn-endpoints.png)
   
2. I det vänstra fönstret under inställningar klickar du på **cachelagring regler**.
   
    ![Knappen CDN cachelagring regler](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. I den **cachelagring av frågesträngar** listan, Välj ett sträng-frågeläge och klicka sedan på **spara**.
   
   ![CDN-frågesträng cachelagringsalternativ](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Eftersom det tar tid för registreringen ska spridas via CDN kanske cache sträng ändringarna inte visas omedelbart. För **Azure CDN från Akamai**-profiler, slutförs spridningen vanligtvis inom en minut. För **Azure CDN från Verizon**-profiler, slutförs spridningen vanligtvis inom 90 minuter, men i vissa fall kan det ta längre tid.


