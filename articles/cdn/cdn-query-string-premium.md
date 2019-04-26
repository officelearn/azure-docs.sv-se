---
title: Kontrollera Cachelagringsbeteendet med frågesträngar - premium-nivån | Microsoft Docs
description: Azure CDN cachelagring av frågesträngar kontroller hur filerna cachelagras när en webbegäran innehåller en frågesträng. Den här artikeln beskriver cachelagring i Azure CDN Premium från Verizon produkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2f0a361d53489e22ccc8e41406e5b86b423ea2f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324834"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Kontrollera Cachelagringsbeteendet med frågesträngar - premium-nivån
> [!div class="op_single_selector"]
> * [Standardnivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN), kan du styra hur filerna cachelagras för en webbegäran som innehåller en frågesträng. I en webb-begäran med en frågesträng är frågesträngen som del av den begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla en eller flera nyckel / värde-par, där fältnamnet och dess värde avgränsas med ett likhetstecken (=). Varje nyckel / värde-par är avgränsade med ett et-tecken (&). Exempel: http:\//www.contoso.com/content.mov?field1=value1 & fält2 = värde2. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, spelar ingen deras inbördes ordning. 

> [!IMPORTANT]
> CDN-produkter standard och premium ger samma cachelagring av frågesträngar funktioner, men användargränssnittet är olika. Den här artikeln beskriver gränssnitt för **Azure CDN Premium från Verizon**. Frågesträngen cachelagring med Azure CDN standard produkter, finns i [Kontrollera Cachelagringsbeteendet med frågesträngar - standard-nivån](cdn-query-string.md).
>


Det finns tre lägen för frågan sträng:

- **standard-cache**: Standardläget. I det här läget noden CDN point of presence (POP) skickar frågesträngarna från begäranden till den ursprungliga servern på den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från POP-servern Ignorera frågesträngarna tills den cachelagra tillgången upphör att gälla.

    >[!IMPORTANT] 
    > Om token auktorisering har aktiverats för valfri sökväg på det här kontot, är det enda läge som du kan använda standard-cache-läge. 

- **no-cache**: I det här läget cachelagras begäranden med frågesträngar inte i CDN POP-nod. Noden POP hämtar tillgången direkt från den ursprungliga servern och skickar dem till begäranden med varje begäran.

- **unique-cache**: I det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen som en unik tillgång med sin egen cache. Till exempel är svaret från den ursprungliga servern för en begäran om example.ashx?q=test1 cachelagras på POP-nod och returneras för efterföljande cacheminnen med samma frågesträngen. En begäran om example.ashx?q=test2 cachelagras som en separat tillgång med sin egen time-to-live-inställningen.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ändrar med alla begäranden, till exempel ett sessions-ID eller ett användarnamn, eftersom det resulterar i ett låga Cacheträff förhållande.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ändra cachelagring av inställningar för premium CDN-profiler
1. Öppna en CDN-profil och klicka sedan på **hantera**.
   
    ![Knappen för CDN-profil hantera](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **HTTP stora** och sedan hovra över den **cacheinställningarna** utfälld meny. Klicka på **cachelagring av frågesträng**.
   
    Frågesträng cachelagringsalternativ visas.
   
    ![CDN-frågesträngen som alternativ för cachelagring](./media/cdn-query-string-premium/cdn-query-string.png)
3. Välj ett sträng-frågeläge och klicka sedan på **uppdatering**.

> [!IMPORTANT]
> Eftersom det tar tid för registreringen att spridas i CDN kanske cache sträng ändras inte omedelbart synliga. Slutförs spridningen vanligtvis inom 10 minuter.
 

