---
title: Kontrollera Azure CDN-cachelagring med frågesträngar – premiumnivå
description: Cachelagring av Azure CDN-frågesträng styr hur filer cachelagras när en webbbegäran innehåller en frågesträng. I den här artikeln beskrivs cachelagring av frågesträngar i Azure CDN Premium från Verizon-produkten.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 365c52840d281c0f48d17aacc358e4cce513e3b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083093"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Kontrollera Azure CDN-cachelagring med frågesträngar – premiumnivå
> [!div class="op_single_selector"]
> * [Standard-nivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med CDN (Azure Content Delivery Network) kan du styra hur filer cachelagras för en webbbegäran som innehåller en frågesträng. I en webbbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckelvärdespar där fältnamnet och dess värde avgränsas med ett likhetstecken (=). Varje nyckelvärdespar avgränsas med ett et-tecken (&). Http:\//www.contoso.com/content.mov?field1=value1&fält2=value2. Om det finns mer än ett nyckelvärdespar i en frågesträng för en begäran spelar deras ordning ingen roll. 

> [!IMPORTANT]
> Standard- och premium-CDN-produkterna har samma frågesträngcachningsfunktioner, men användargränssnittet är annorlunda. I den här artikeln beskrivs gränssnittet för **Azure CDN Premium från Verizon**. För frågesträngcachening med Azure CDN-standardprodukter finns i [Kontrollera Azure CDN-cachelagring med frågesträngar - standardnivå](cdn-query-string.md).
>


Det finns tre frågestränglägen:

- **standardcache:** Standardläge. I det här läget skickar POP-noden (POINT-of-presence) frågesträngarna från beställaren till ursprungsservern på den första begäran och cachelagrar tillgången. Alla efterföljande begäranden för tillgången som visas från POP-servern ignorerar frågesträngarna tills den cachelagrade tillgången upphör att gälla.

    >[!IMPORTANT] 
    > Om tokenauktorisering är aktiverat för en sökväg för det här kontot är standardcacheläget det enda läge som kan användas. 

- **no-cache**: I det här läget cachelagras inte begäranden med frågesträngar vid CDN POP-noden. POP-noden hämtar tillgången direkt från ursprungsservern och skickar den till beställaren med varje begäran.

- **unique-cache**: I det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik tillgång med sin egen cache. Svaret från ursprungsservern för en begäran om example.ashx?q=test1 cachelagras till exempel vid POP-noden och returneras för efterföljande cacheminnen med samma frågesträng. En begäran om example.ashx?q=test2 cachelagras som en separat tillgång med sin egen time-to-live-inställning.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ändras med varje begäran, till exempel ett sessions-ID eller ett användarnamn, eftersom det resulterar i ett lågt cache-träffförhållande.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ändra inställningar för cachelagring av frågesträngar för premium-CDN-profiler
1. Öppna en CDN-profil och klicka sedan på **Hantera**.
   
    ![Knappen Hantera CDN-profil](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över fliken **HTTP Stor** och hovra sedan över den utfällbara menyn **Cacheinställningar.** Klicka på **Frågesträngcache.**
   
    Alternativ för cachelagring av frågesträngar visas.
   
    ![Cachelagringsalternativ för CDN-frågesträng](./media/cdn-query-string-premium/cdn-query-string.png)
3. Välj ett frågesträngläge och klicka sedan på **Uppdatera**.

> [!IMPORTANT]
> Eftersom det tar tid för registreringen att spridas via CDN kanske ändringar av cachestränginställningar inte är omedelbart synliga. Förökningen slutförs vanligtvis på 10 minuter.
 

