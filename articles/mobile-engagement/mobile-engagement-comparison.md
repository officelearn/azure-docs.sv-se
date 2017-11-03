---
title: "Jämföra Azure Mobile Engagement med andra liknande Azure-tjänster"
description: "Jämföra Azure Mobile Engagement med andra liknande Azure-tjänster - HockeyApp, AppInsights, Notification Hubs"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7df2eb9ecebe3313dad9c15171552a084787f6b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Jämföra Azure Mobile Engagement med andra liknande Azure-tjänster
Lista över tjänster som erbjuds av Microsoft Azure expanderande någonsin och ibland kanske du undrar hur är Azure Mobile Engagement skiljer sig den här tjänsten som du läser eller talas om. Den här artikeln försök att rensa förvirringen och dirigerar dig att välja Azure Mobile Engagement när det är mest lämpliga för din användning. 

Azure Mobile Engagement är en tjänst som är avsedda specifikt **för digitala marknadsförare/CMOs** men kan användas av alla **mobilappen ägare eller utgivare** som vill öka användningen, kvarhållning och Monetarisering av sina mobila appar. 

*Det är en programvara som en tjänst (SaaS) användaren plattform som tillhandahåller datadriven statistik för appanvändning, användarsegmentering, och möjliggör kontextmedvetna push-meddelanden och meddelanden i appen.* 

Bryta ned den här definitionen kan har vi följande viktiga egenskaper som visar även dess unika värdeförslag:

1. **Kontextmedvetna push-meddelanden och meddelanden i appen**
   
   Detta är produkten core fokus - utför mål- och anpassade push-meddelanden. Och för att detta ska hända, vi samlar in omfattande beteendeanalys data. 
2. **Datadriven statistik för appanvändning**
   
   Vi ger plattformsoberoende SDK: er för att samla in beteendeanalys om appanvändare. Observera termen beteendeanalys (mot prestanda analytics) eftersom vi fokusera på hur appanvändarna använder appen. Vi samlar in grundläggande analytics prestandadata om fel, krascher etc men som inte core fokus för produkten. 
3. **Användarsegmentering**
   
   När du har samlat in app användarnas beteendeanalys data kan vi du segment målgruppen baserat på olika parametrar och insamlade data så att du kan köra riktade push-kampanjer. 
4. **Programvara som en-tjänst (SaaS):**
   
   Vi har en separat från Azure-hanteringsportalen som är optimerad för att interagera och visa omfattande beteendeanalys om appanvändare och köra push marknadsföringskampanjer portal. Produkten är avsedd för att få med dig i aldrig!   

Här är hur vi Jämför med andra Azure-erbjudanden till synes liknande – Observera att artikeln inte kan göra en funktionsjämförelse men tar ett scenario med flera baserad metod för att definiera vilka produkten fungerar bäst med den här uppsättningen skillnad i manuellt:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) är Microsofts mobila DevOps-lösning. Med den här lösningen kan du distribuera versioner till betatestare, samla in kraschdata och få feedback från användare. Det är integrerat med Visual Studio Team Services aktiverar enkelt skapa distributioner och arbetsuppgiftsintegrering. 
   
   Det här gäller DevOps och analysdata för att samla in prestanda om de mobila apparna. Kan det sluta med att integrera båda HockeyApps och Mobile Engagement i din app och som inte ovanliga eftersom även om det inte finns några överlapp av insamlade data, core produkterna fokuserar på olika och de hjälper med att uppnå olika mål för dig.  
2. [Application Insights](../application-insights/app-insights-overview.md) om din mobila app har en server-side sedan du använder Application Insights för att övervaka web server-side för din app men du bör använda HockeyApp för mobila appar klienten sida. 
3. [Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) är en förenklad tjänst i den mening att behöver du en SDK integrerat i mobilappen och du kan ha fullständig kontroll över din mobila app och kan skicka push-meddelanden med grundläggande funktioner för märkning. Det här är bra för varje app-ägare som behöver mindre om mål och mer skicka/kommunicera information direkt till appanvändarna (sändning till en stor del). 
   
   Observera i fokus skickas blixtsnabb snabb meddelanden med grundläggande segmentering kapacitet. 

Låt oss ta vissa scenarier:

1. Tim är en del av digitala marknadsföringsgruppen i Adventure Works-butiken som publicerar mobilappar för användare. Tims målet är att säkerställa att de användare som hämtar sina mobila appar fortsätter att använda den och ofta. Detta inte bara ökar ett varumärke bifoga med användarna, men också ökar Monetarisering när app-användare gör inköp med hjälp av mobilappen. För den här Tim kommer att behöva skicka riktade meddelanden till användare app, som de vara användbart att uppmana dem att öppna appen och återgå till det ofta. Detta är Tim hittar Mobile Engagement användbart. 
2. Joann är en del av Utvecklingsteamet mobila appar på Adventure Works och är ute efter en produkt att logga information om kraschar, undantag, och hämta prestanda telemetri från en app. Detta är Joann hittar HockeyApp användbart. Joann kan integrera både HockeyApp för sitt developer fokuserar scenarier och Azure Mobile Engagement för Tim i samma app att hämta bäst av båda. 
3. Robin är en del av Utvecklingsteamet mobila appar på Nyheter i Contoso-nätverket och alla hon vill är att skicka ut förstöra nyhetsaviseringar till alla användare utan mycket segmentering så snart nyheter avisering utlöses. Detta är Robin hittar Meddelandehubbar användbart. 
   I det här scenariot är det möjligt men då den mobila appen utvecklas, det är ett krav att göra mycket bättre segmentering och få information om appen användarens beteende. För tillfället måste Robin utvärdera Azure Mobile Engagement. 

Om du vill Sammanfattningsvis, syftet med Mobile Engagement är inte bara för att samla in analytics inte - ”ännu en Analytics produkt från Microsoft”. Det handlar om att skicka riktade push-meddelanden och för den här riktad vi samlar in beteendeanalys data men förblir det i fokus skicka push-meddelanden som gör bäst till app-användare så att det inte kommer som skräppost. 

Mer information – ta en titt på den här [kort video](mobile-engagement-overview.md) om Mobile Engagement i en nutshell. 

