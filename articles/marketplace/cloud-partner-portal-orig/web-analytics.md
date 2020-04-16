---
title: Webbanalys
description: Den här artikeln innehåller instruktioner om hur du lär dig och använder Web Analytics för att på bästa sätt utveckla ditt företag.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416289"
---
<a name="web-analytics"></a>Webbanalys
=============

Den här artikeln innehåller instruktioner om hur du lär dig och använder Web Analytics för att på bästa sätt utveckla ditt företag. För närvarande är den här fliken Insikter tillgänglig för alla AppSource-erbjudanden.

Nu när du har byggt och publicerat ditt erbjudande, nästa del\' av din resa är att spåra och mäta dess framgång. Med **Web Analytics**har vi lagt till möjligheten att se exakt hur bra vart och ett av dina erbjudanden går på marknaden. Om du vill starta din resa navigerar du till sidan Insikter till vänster i Cloud Partner Portal för att se den nya fliken Analytics.

![Sidan WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Du kommer att se en omfattande instrumentpanel för ditt Publisher-ID som har byggts med Microsoft Power BI och gör att du kan se var och en av dina erbjudandedata,\' som uppdateras dagligen.

<a name="microsoft-campaigns"></a>**Microsoft-kampanjer**
-----------------------

För att få dina erbjudanden att växa och spåra tillväxten av dina erbjudanden har vi gjort det möjligt att använda **Microsoft-kampanjer** på Cloud Partner Portal. Kampanjer är en nyligen stödd funktion för marknadsplatsen som gör att du kan spåra de olika kanaler som skickar kunder till sidan med appinformation.

### <a name="how-to-make-a-campaign"></a>**Hur man gör en kampanj**

Det enklaste sättet att beskriva kampanjer är att du lägger till ett anpassat ord/en anpassad term i webbadressen som hamnar på sidan med appinformation på marknadsplatsen. Google, Bing, LinkedIn och många andra webbplatser uppmuntrar dig att bygga en annons, länk från deras webbplats till önskad webbplats.

I allmänhet är dessa ansträngningar för att hjälpa till att driva nya kunder till din produkt och det är viktigt att mäta framgången för hur var och en av dina kanaler gör. Det är här kampanjer kommer in.

Det finns två sätt att skapa din egen kampanj.

1. Lägg till frågeparametern **mktcmpid** i webbadressen som beskriver vad kampanjen är och vilken sida/händelse dessa kunder kommer ifrån.

Du kan till exempel använda:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Avancerat): Använd ett av våra generiska kampanj-ID:er som stöds i webbadressen. Vi vill vara tillmötesgående med ytterligare ref-taggar som du behöver använda, så vi stöder konventionen för att automatiskt känna igen dessa ytterligare taggar:
    
    1. **utm\_kampanj**
    2. **utm\_källa**
    3. **Referens**
    4. **src**

Du kan till exempel använda:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Du kan välja att ha en kombination av flera av dessa kampanj-ID för att ytterligare identifiera flera källor som driver trafik för kampanjen, till exempel var kunden kom ifrån (e-post, blogg, sociala medier källa, etc.).

Ett exempel:

1. Nyhetsbrev referrer:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. LinkedIn-värvare:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Se till att kampanjer passerar alla dina sidor**

Det kan finnas ett scenario där dina kampanjer har en mellanliggande sida som du kör trafik till som sedan fortsätter att skicka kunderna till marknadsplatsen. Det är viktigt att du skickar igenom dina första kampanj-ID:er till den slutliga webbadressen som du skickar till marknadsplatsen.

Här är ett exempel:

1. Marknadsföring anställd köper annonser från Google för\'att ```https://contoso.com```driva trafik till företagets målsida . Denna målsida \"har ett\" försök min ```https://appsource.com```produkt länk som går till .
2. En användare klickar på annonsen och\'hamnar på företagets målsida.
    1.  Hänvisningsadress = google.com
    2.  URL till målsidan =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. Användaren klickar på \"länken\" Prova min produkt och går in i AppSource.
    1. Url för hänvisning =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. Målsidans URL (**Se till\_att den\_här webbadressen har utm-kampanj och utm-källa som lagts till i den här webbadressen**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Så här utvärderar du framgången för en kampanj
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Sidbesök efter kampanj**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Detta är fördelningen av var och en av dina dagliga sidbesök av kampanjen de kom från.

### <a name="conversion-rate-by-campaign"></a>**Konverteringsfrekvens per kampanj**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

På samma sätt som vi visar konverteringsfrekvensen för hela erbjudandet kan du i det här diagrammet se hur det går för olika kampanjer. Det här diagrammet bör hjälpa dig att identifiera var dina kampanjer med högre konverteringsfrekvens kommer ifrån.

### <a name="distribution-by-campaign"></a>**Distribution efter kampanj**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

I likhet med hur vi ser på dina kunders domäner kan du i det här diagrammet se hur dina data fördelas per kampanj som användarna kommer till marknadsplatsen under. \_NoCampaign innebär att kunden inte hade ett kampanj-ID i webbadressen när de navigerade till marknadsplatsen.

<a name="next-steps"></a>**Nästa steg**
--------------

Nu när du har möjlighet att spåra dina erbjudanden framgång, vill vi uppmuntra dig att skapa dina egna kampanjer.

Om du har frågor/funktionsförfrågningar kan du dela dem via Feedback, som finns i det övre högra hörnet.

![Feedback i Cloud Partner Portal](./media/si-getting-started/WebAnalytics5.png)
