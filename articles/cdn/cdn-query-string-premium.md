---
title: Kontrollera Azure CDN cachelagring av frågesträngar med frågesträngar - premiumnivån | Microsoft Docs
description: Azure CDN cachelagring av frågesträng styr hur filer cachelagras när en webbegäran innehåller en frågesträng. Den här artikeln beskrivs i Azure CDN Premium från produkt-Verizon cachelagring av frågesträng.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: 9d92602ef5071579e0c741dd24a4e3e9f7b2c747
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Kontrollen Azure CDN cachelagring av frågesträngar med frågesträngar - premium-nivån
> [!div class="op_single_selector"]
> * [Standardnivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Översikt
Med frågan cachelagring av frågesträngar, styr Azure Content Delivery Network (CDN) hur filer cachelagras för en webb-begäran som innehåller en frågesträng. Frågesträngen är den del av den begäran som inträffar efter ett frågetecken (?) i en webbegäran med en frågesträng. En frågesträng kan innehålla en eller flera nyckel / värde-par, där namnet och dess värde är åtskilda av ett likhetstecken (=). Varje nyckel / värde-par avgränsas med ett et-tecken (&). Till exempel http:\//www.contoso.com/content.mov?field1=value1 & fält2 = värde2. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, roll deras inbördes ordning ingen. 

> [!NOTE]
> Azure CDN standard och premium-produkter ger samma frågesträngen cachelagring funktioner, men användargränssnittet är olika.  Den här artikeln beskriver gränssnittet för **Azure CDN Premium från Verizon**. För att fråga sträng cachelagring med **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**, se [kontroll Azure CDN cachelagring av frågesträngar med frågesträngar - standardnivån](cdn-query-string.md).
>


Tre frågan sträng lägen är tillgängliga:

- **standard-cache**: standardläget. I det här läget noden CDN punkt av förekomst (POP) överför frågesträngar från begäranden till den ursprungliga servern den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från servern POP Ignorera frågesträngar tills cachelagrade tillgången upphör att gälla.

    >[!IMPORTANT] 
    > Om token tillstånd är aktiverat för valfri sökväg för det här kontot, är standard-cache-läge det enda läge som kan användas. 

- **no-cache**: I det här läget begäranden med frågesträngar inte cachelagras på noden CDN POP. Noden POP hämtar tillgången direkt från den ursprungliga servern och skickar den till begäranden med varje begäran.

- **Unik cache**: I det här läget varje förfrågan med en unik URL, inklusive frågesträngen behandlas som en unik tillgång med sin egen cache. Svaret från den ursprungliga servern för en begäran om example.ashx?q=test1 är cachelagras på noden POP och returneras för efterföljande med samma frågesträngen. En begäran om example.ashx?q=test2 cachelagras som en separat tillgång med sin egen time to live-inställningen.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ändrar med alla begäranden, till exempel ett sessions-ID eller ett användarnamn, eftersom det kommer att resultera i förhållandet låg cache-träff.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ändra inställningar för premium CDN profiler för cachelagring av frågesträng
1. Öppna en CDN-profil och klicka sedan på **hantera**.
   
    ![CDN-profilen hantera knappen](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **HTTP stora** och klicka sedan hovra över den **cacheinställningarna** utfälld meny. Klicka på **cachelagring av frågesträng**.
   
    Frågesträng cachelagringsalternativ visas.
   
    ![CDN-frågesträng cachelagringsalternativ](./media/cdn-query-string-premium/cdn-query-string.png)
3. Välj ett sträng-frågeläge och klicka sedan på **uppdatering**.

> [!IMPORTANT]
> Eftersom det tar tid för registreringen ska spridas via CDN kanske cache sträng ändringarna inte visas omedelbart. För **Azure CDN Premium från Verizon** profiler, spridningen vanligtvis har slutförts inom 90 minuter.
 

