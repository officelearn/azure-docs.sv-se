---
title: Kontrollera Cachelagringsbeteendet med frågesträngar - standard-nivån | Microsoft Docs
description: Azure CDN cachelagring av frågesträngar kontroller hur filerna cachelagras när en webbegäran innehåller en frågesträng. Den här artikeln beskriver cachelagring i Azure CDN standard produkter.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: f0dab3dc81c626e3e7f8c79b4142e5eb4f2a1276
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324782"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Kontrollera Cachelagringsbeteendet med frågesträngar - standard-nivån
> [!div class="op_single_selector"]
> * [Standardnivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN), kan du styra hur filerna cachelagras för en webbegäran som innehåller en frågesträng. I en webb-begäran med en frågesträng är frågesträngen som del av den begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla en eller flera nyckel / värde-par, där fältnamnet och dess värde avgränsas med ett likhetstecken (=). Varje nyckel / värde-par är avgränsade med ett et-tecken (&). Exempel: http:\//www.contoso.com/content.mov?field1=value1 & fält2 = värde2. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, spelar ingen deras inbördes ordning. 

> [!IMPORTANT]
> Azure CDN standard och premium-produkter ger samma cachelagring av frågesträngar funktioner, men användargränssnittet är olika. Den här artikeln beskriver gränssnitt för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**. För cachelagring av frågan sträng med **Azure CDN Premium från Verizon**, se [Kontrollera Cachelagringsbeteendet med frågesträngar - premium-nivån](cdn-query-string-premium.md).

Det finns tre lägen för frågan sträng:

- **Ignorera frågesträngar**: Standardläget. I det här läget noden CDN point of presence (POP) skickar frågesträngarna från begäranden till den ursprungliga servern på den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från POP Ignorera frågesträngarna tills den cachelagra tillgången upphör att gälla.

- **Kringgå cachelagring för frågesträngar**: I det här läget cachelagras begäranden med frågesträngar inte i CDN POP-nod. Noden POP hämtar tillgången direkt från den ursprungliga servern och skickar dem till begäranden med varje begäran.

- **Cachelagra varje unik URL**: I det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen som en unik tillgång med sin egen cache. Till exempel är svaret från den ursprungliga servern för en begäran om example.ashx?q=test1 cachelagras på POP-nod och returneras för efterföljande cacheminnen med samma frågesträngen. En begäran om example.ashx?q=test2 cachelagras som en separat tillgång med sin egen time-to-live-inställningen.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ändrar med alla begäranden, till exempel ett sessions-ID eller ett användarnamn, eftersom det resulterar i ett låga Cacheträff förhållande.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändra cachelagring av inställningar för standard CDN-profiler
1. Öppna en CDN-profil, och välj sedan den CDN-slutpunkt som du vill hantera.
   
   ![CDN-slutpunkter för profilen](./media/cdn-query-string/cdn-endpoints.png)
   
2. I det vänstra fönstret under inställningar klickar du på **Cachelagringsregler**.
   
    ![Knappen CDN-cachelagringsregler](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. I den **cachelagring av frågesträngar** , Välj ett sträng-frågeläge och sedan klicka på **spara**.
   
   ![CDN-frågesträngen som alternativ för cachelagring](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Eftersom det tar tid för registreringen att sprida via Azure CDN kanske cache sträng ändras inte omedelbart synliga:
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 



