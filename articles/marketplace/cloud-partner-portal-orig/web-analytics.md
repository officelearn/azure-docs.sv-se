---
title: Webbanalys
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 4b75b2cbd629204e7edbf1196eec9b03cb7a6736
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943162"
---
<a name="web-analytics"></a>Webbanalys
=============

Den här artikeln innehåller med instruktioner om hur du lär dig och använder Webbanalyser till bästa verksamheten växer. Den här fliken för insikter är för närvarande tillgänglig för alla AppSource-erbjudanden.

Nu när du har skapat och publicerat ditt erbjudande, nästa del av din resa är att spåra och mäter dess\' lyckades. Med **Webbanalyser**, har vi lagt till möjligheten att se exakt hur väl gör var och en av dina erbjudanden på marketplace. Starta din resa genom gå du till sidan insikter på vänster sida av Cloud Partner Portal för att se den nya Analytics-fliken.

![WebAnalytics sida](./media/si-getting-started/WebAnalytics1.png)

Du ser en omfattande instrumentpanel för Publisher-ID som har skapats med Microsoft Power BI och gör det möjligt att se var och en av dina erbjudanden\' data som uppdateras dagligen.

<a name="microsoft-campaigns"></a>**Microsoft Campaigns**
-----------------------

För att utöka dina erbjudanden och spåra tillväxten av dina erbjudanden, har vi aktiverat möjligheten att använda **Microsoft kampanjer** på partnerportalen i molnet. Kampanjer är en funktion som nyligen stöds för marketplace som gör det möjligt för dig att spåra olika kanaler som skickar kunder till din appinformationssida.

### <a name="how-to-make-a-campaign"></a>**Hur du gör en kampanj**

Det enklaste sättet att beskriva kampanjer är att du lägger till en anpassad word/term till din URL som hamnar på sidan med information om appen i marketplace. Google, Bing, LinkedIn och många andra webbplatser uppmanar dig att skapa en annons, länka från sin plats till din önskade plats.

I allmänhet dessa åtgärder är att nya kunder för enheten i din produkt och det är viktigt att mäta framgången för hur var och en av dina kanaler fungerar. Det är där kampanjer kommer in.

Det finns två sätt att skapa din egen kampanj.

1. Lägga till i din URL Frågeparametern **mktcmpid** som beskriver vad kampanjen är och vilka händelsen/sida dessa kunder kommer från.

Du kan till exempel använda: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Avancerat): Använd någon av våra stöds, allmän kampanj ID i URL: en. Vi vill vara minnesalternativ med ytterligare ref-taggar som du vill använda, så vi har stöd för partitioner för att automatiskt identifiera dessa ytterligare taggar:
    
    1. **utm\_campaign**
    2. **UTM\_källa**
    3. **ref**
    4. **src**

Du kan till exempel använda: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Du kan välja att ha en kombination av flera av dessa kampanj-ID: N att ytterligare identifiera flera källor som driver trafik för kampanjen, till exempel där kunden kommer ifrån (e-post, blogg, sociala mediekällor, osv.).

Exempel:

1. Nyhetsbrev referent:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn referent:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Säkerställa kampanjer passera alla sidor**

Det kan finnas ett scenario där dina kampanjer har en mellanliggande sida som du som styr trafiken till som sedan fortsätter att skicka kunderna till marketplace. Det är viktigt att gå igenom din första kampanj-ID: N till den slutliga URL: en som du skickar till marketplace.

Här är ett exempel:

1. Marknadsföring medarbetare köper annonser från Google drive-trafik till företaget\'s landningssida <https://contoso.com>. Den här landningssida har en \"försök min produkt\" länkar som leder till <https://appsource.com>.
2. En användare klickar på ad och landar på företaget\'s landningssida.
    1.  URL: en för hänvisning = google.com
    2.  Webbadress för informationssida = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. Användaren klickar på den \"försök min produkt\" länka och hamnar i AppSource.
    1. URL: en för hänvisning =  <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Webbadress för informationssida (**se till att den här URL: en har utm\_kampanj- och utm\_källor du lägger till denna URL**) = [ https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername? **utm\_kampanj = MyCampaignAdName & utm\_källa = MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Så här utvärderar framgången för en kampanj
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Sidan Visits efter kampanj**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Detta är uppdelning av var och en av dina dagliga besök efter kampanj som de kom från.

### <a name="conversion-rate-by-campaign"></a>**Konverteringsgrad efter kampanj**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Ett liknande sätt hur vi visar konverteringstakt för erbjudandet hela i det här diagrammet som du ser fördelningen av hur kampanjerna olika gör. Det här diagrammet ska hjälpa dig att identifiera var kampanjerna hastighet högre konvertering kommer från.

### <a name="distribution-by-campaign"></a>**Distribution efter kampanj**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Precis som för hur vi tittar på domäner för dina kunder finns det här diagrammet kan du se fördelningen av dina data per kampanj som användarna kommer att marketplace under. \_NoCampaign innebär att kunden inte hade en kampanj-ID i URL: en när de har navigerat till marketplace.

<a name="next-steps"></a>**Nästa steg**
--------------

Nu när du har möjlighet att spåra dina erbjudanden lyckades, vill vi uppmuntrar dig att skapa dina egna kampanjer.

Om du har frågor/funktionsförfrågningar kan du dela dem via Feedback, finns i det övre högra hörnet.

![Feedback i partnerportalen i molnet](./media/si-getting-started/WebAnalytics5.png)
