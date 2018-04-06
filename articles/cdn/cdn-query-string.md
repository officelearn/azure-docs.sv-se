---
title: Kontrollera Azure CDN cachelagring av frågesträngar med frågesträngar - standardnivån | Microsoft Docs
description: Azure CDN cachelagring av frågesträng styr hur filer cachelagras när en webbegäran innehåller en frågesträng. Den här artikeln beskriver cachelagring i Azure CDN standard produkter.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: ed6f0b2c021fc4b31b85986c07df0502dba826f2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Kontrollen Azure CDN cachelagring av frågesträngar med frågesträngar - standardnivån
> [!div class="op_single_selector"]
> * [Standardnivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med frågan cachelagring av frågesträngar, styr Azure Content Delivery Network (CDN) hur filer cachelagras för en webb-begäran som innehåller en frågesträng. Frågesträngen är den del av den begäran som inträffar efter ett frågetecken (?) i en webbegäran med en frågesträng. En frågesträng kan innehålla en eller flera nyckel / värde-par, där namnet och dess värde är åtskilda av ett likhetstecken (=). Varje nyckel / värde-par avgränsas med ett et-tecken (&). Till exempel http:\//www.contoso.com/content.mov?field1=value1 & fält2 = värde2. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, roll deras inbördes ordning ingen. 

> [!NOTE]
> Azure CDN standard och premium-produkter ger samma frågesträngen cachelagring funktioner, men användargränssnittet är olika.  Den här artikeln beskriver gränssnittet för **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**. För frågan sträng cachelagring med **Azure CDN Premium från Verizon**, se [kontroll Azure CDN cachelagring av frågesträngar med frågesträngar - premiumnivån](cdn-query-string-premium.md).
>

Tre frågan sträng lägen är tillgängliga:

- **Ignorera frågesträngar**: standardläget. I det här läget noden CDN punkt av förekomst (POP) överför frågesträngar från begäranden till den ursprungliga servern den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från POP Ignorera frågesträngar tills cachelagrade tillgången upphör att gälla.

- **Kringgå cachelagring för frågesträngar**: I det här läget begäranden med frågesträngar inte cachelagras på noden CDN POP. Noden POP hämtar tillgången direkt från den ursprungliga servern och skickar den till begäranden med varje begäran.

- **Cachelagra varje unik URL**: I det här läget varje förfrågan med en unik URL, inklusive frågesträngen behandlas som en unik tillgång med sin egen cache. Svaret från den ursprungliga servern för en begäran om example.ashx?q=test1 är cachelagras på noden POP och returneras för efterföljande med samma frågesträngen. En begäran om example.ashx?q=test2 cachelagras som en separat tillgång med sin egen time to live-inställningen.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ändrar med alla begäranden, till exempel ett sessions-ID eller ett användarnamn, eftersom det kommer att resultera i förhållandet låg cache-träff.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändra inställningar för standard CDN-profiler för cachelagring av frågesträng
1. Öppna en CDN-profil, och välj sedan CDN-slutpunkt som du vill hantera.
   
   ![CDN-profil-slutpunkter](./media/cdn-query-string/cdn-endpoints.png)
   
2. I det vänstra fönstret under inställningar klickar du på **cachelagring regler**.
   
    ![Knappen CDN cachelagring regler](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. I den **cachelagring av frågesträngar** listan, Välj ett sträng-frågeläge och klicka sedan på **spara**.
   
   ![CDN-frågesträng cachelagringsalternativ](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Eftersom det tar tid för registreringen ska spridas via CDN kanske cache sträng ändringarna inte visas omedelbart: 
> - För **Azure CDN Standard från Akamai** profiler, spridningen vanligtvis slutförs inom en minut. 
> - För **Azure CDN Standard från Verizon** profiler, spridningen vanligtvis har slutförts inom 90 minuter.
>


