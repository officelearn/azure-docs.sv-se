---
title: Webb analys
description: Den här artikeln innehåller anvisningar om hur du kan lära dig och använda Web Analytics för att utveckla din verksamhet bäst.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416289"
---
<a name="web-analytics"></a>Webb analys
=============

Den här artikeln innehåller anvisningar om hur du kan lära dig och använda Web Analytics för att utveckla din verksamhet bäst. Den här fliken insikter är för närvarande tillgänglig för alla AppSource-erbjudanden.

Nu när du har skapat och publicerat ditt erbjudande är nästa del av resan att spåra och mäta dess\' framgång. Med **Web Analytics**har vi lagt till möjligheten att se exakt hur väl var och en av dina erbjudanden på Marketplace. Starta resan genom att gå till sidan insikter till vänster i Cloud Partner Portal för att se fliken ny analys.

![Sidan webb analys](./media/si-getting-started/WebAnalytics1.png)

Du ser en omfattande instrument panel för ditt utgivar-ID som har skapats med Microsoft Power BI och gör att du kan se var och\' en av dina erbjudanden, som uppdateras dagligen.

<a name="microsoft-campaigns"></a>**Microsoft-kampanjer**
-----------------------

Vi har aktiverat möjligheten att använda **Microsoft-kampanjer** på Cloud Partner Portal för att utöka dina erbjudanden och spåra tillväxten för dina erbjudanden. Kampanjer är en funktion som stöds nyligen för Marketplace som gör att du kan spåra de olika kanaler som skickar kunder till din apps-information.

### <a name="how-to-make-a-campaign"></a>**Så här gör du en kampanj**

Det enklaste sättet att beskriva kampanjer är att du lägger till ett anpassat ord/term till din URL som finns på din app-informations sida på Marketplace. Google, Bing, LinkedIn och många andra webbplatser uppmanar dig att bygga en annons, länka från sin webbplats till önskad plats.

I allmänhet är dessa ansträngningar att hjälpa till att driva nya kunder i din produkt och det är viktigt att mäta framgången för hur var och en av dina kanaler fungerar. Det är här som kampanjer kommer i.

Det finns två sätt att skapa en egen kampanj.

1. Lägg till URL: en **mktcmpid** som beskriver vad kampanjen är och vilken sida/händelse dessa kunder kommer från.

Du kan till exempel använda:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Avancerat): Använd något av våra generiska ID: n som stöds i URL: en. Vi vill kunna ta emot ytterligare ref-taggar som du behöver använda, så vi har stöd för konventionen för att automatiskt identifiera dessa ytterligare Taggar:
    
    1. **UTM\_-kampanj**
    2. **UTM\_-källa**
    3. **Ref**
    4. **src**

Du kan till exempel använda:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Du kan välja att kombinera flera av dessa kampanj-ID: n för att ytterligare identifiera flera källor som driver trafik för kampanjen, till exempel var kunden kom från (e-post, blogg, sociala medie källa osv.).

Ett exempel:

1. Referent-nyhets brev:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. LinkedIn referent:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Se till att kampanjer passerar alla dina sidor**

Det kan finnas ett scenario där dina kampanjer har en mellanliggande sida som du kör trafik till och sedan fortsätter att skicka kunderna till Marketplace. Det är viktigt att passera dina första kampanj-ID: n till den sista URL: en som du skickar till Marketplace.

Här är ett exempel:

1. Marknadsförings personal köper annonser från Google för att driva trafik till\'företagets landnings ```https://contoso.com```sida. Den här landnings sidan \"har ett försök\" med min produkt länk ```https://appsource.com```som går till.
2. En användare klickar på annonsen och hamnar på företagets\'landnings sida.
    1.  Hänvisnings-URL = google.com
    2.  Landnings sidans URL =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. Användaren klickar på \"länken prova min produkt\" och hamnar i AppSource.
    1. Hänvisnings-URL =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. Landnings sidans URL (**Se till att den\_här URL:\_en har UTM-kampanj och UTM-källa tillagd till denna URL**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Så här utvärderar du en kampanjs framgång
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Sid besök per kampanj**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Detta är en uppdelning av de dagliga sid besöken för kampanjen de kom från.

### <a name="conversion-rate-by-campaign"></a>**Konverterings takt per kampanj**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

På samma sätt som vi visar konverterings takten för hela erbjudandet kan du se hur dina olika kampanjer fungerar i det här diagrammet. Det här diagrammet ska hjälpa dig att identifiera var dina högre konverterings takts kampanjer kommer från.

### <a name="distribution-by-campaign"></a>**Distribution efter kampanj**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

På liknande sätt som vi tittar på domänernas domäner kan du med det här diagrammet se distributionen av dina data per kampanj som användarna kommer till Marketplace under. \_Nocampaign innebär att kunden inte har något kampanj-ID i URL: en när de navigerade till Marketplace.

<a name="next-steps"></a>**Nästa steg**
--------------

Nu när du har möjlighet att spåra dina erbjudanden, vill vi gärna att du ska kunna skapa dina egna kampanjer.

Om du har frågor/funktions begär Anden kan du dela dem via feedback, som finns i det övre högra hörnet.

![Feedback i Cloud Partner Portal](./media/si-getting-started/WebAnalytics5.png)
