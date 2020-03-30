---
title: Kontrollera Azure CDN-cachelagringsbeteende med frågesträngar – standardnivå
description: Cachelagring av Azure CDN-frågesträng styr hur filer cachelagras när en webbbegäran innehåller en frågesträng. I den här artikeln beskrivs cachelagring av frågesträngar i Azure CDN-standardprodukter.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 6471241527dd9b594eaaca20ebc75cacb27f8f72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083035"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Kontrollera Azure CDN-cachelagringsbeteende med frågesträngar – standardnivå
> [!div class="op_single_selector"]
> * [Standard-nivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med CDN (Azure Content Delivery Network) kan du styra hur filer cachelagras för en webbbegäran som innehåller en frågesträng. I en webbbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckelvärdespar där fältnamnet och dess värde avgränsas med ett likhetstecken (=). Varje nyckelvärdespar avgränsas med ett et-tecken (&). Http:\//www.contoso.com/content.mov?field1=value1&fält2=value2. Om det finns mer än ett nyckelvärdespar i en frågesträng för en begäran spelar deras ordning ingen roll. 

> [!IMPORTANT]
> Azure CDN-standard- och premiumprodukter ger samma frågesträngcacheningsfunktion, men användargränssnittet är annorlunda. I den här artikeln beskrivs gränssnittet för **Azure CDN Standard från Microsoft**, Azure **CDN Standard från Akamai** och **Azure CDN Standard från Verizon**. För frågesträngcachening med **Azure CDN Premium från Verizon**finns i Kontrollera Azure [CDN-cachelagring med frågesträngar - premiumnivå](cdn-query-string-premium.md).

Det finns tre frågestränglägen:

- **Ignorera frågesträngar**: Standardläge. I det här läget skickar POP-noden (POINT-of-presence) frågesträngarna från beställaren till ursprungsservern på den första begäran och cachelagrar tillgången. Alla efterföljande begäranden för tillgången som visas från POP ignorerar frågesträngarna tills den cachelagrade tillgången upphör att gälla.

- **Kringgå cachelagring för frågesträngar**: I det här läget cachelagras inte begäranden med frågesträngar vid CDN POP-noden. POP-noden hämtar tillgången direkt från ursprungsservern och skickar den till beställaren med varje begäran.

- **Cache varje unik URL:** I det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik tillgång med sin egen cache. Svaret från ursprungsservern för en begäran om example.ashx?q=test1 cachelagras till exempel vid POP-noden och returneras för efterföljande cacheminnen med samma frågesträng. En begäran om example.ashx?q=test2 cachelagras som en separat tillgång med sin egen time-to-live-inställning.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ändras med varje begäran, till exempel ett sessions-ID eller ett användarnamn, eftersom det resulterar i ett lågt cache-träffförhållande.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändra inställningar för cachelagring av frågesträngar för vanliga CDN-profiler
1. Öppna en CDN-profil och välj sedan den CDN-slutpunkt som du vill hantera.
   
   ![Slutpunkter för CDN-profil](./media/cdn-query-string/cdn-endpoints.png)
   
2. Klicka på **Cachelagringsregler**i den vänstra rutan under Inställningar .
   
    ![Knappen CDN-cachelagringsregler](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Markera ett frågesträngläge i listan **Frågesträngcachelagring** och klicka sedan på **Spara**.
   
   ![Cachelagringsalternativ för CDN-frågesträng](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Eftersom det tar tid för registreringen att spridas via Azure CDN kanske ändringar av cachestränginställningar inte är omedelbart synliga:
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 



