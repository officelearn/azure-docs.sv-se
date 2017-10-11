---
title: Azure Mobile Engagement - nyckeln funktioner
description: Beskriver de viktigaste funktionerna i Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d98bafb6-4fd0-4cc3-8c2f-962af70c416c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8270a223ca31c623c1b760a64c0e4d64fb85e080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement---key-features"></a>Azure Mobile Engagement - nyckeln funktioner
Den här artikeln ger en översiktlig överblick om viktiga funktioner i Mobile Engagement-plattformen. 

## <a name="general"></a>**Allmänt**
* **Hitta SDK: er för alla större plattformar** SDK: er som är tillgängliga för alla större plattformar - iOS, Android, universell Windows, Windows Phone Silverlight, Kindle, Cordova. 
  Vi ger enkel för att integrera SDK: er och användbara dokumentation för att komma igång på alla plattformar du väljer. 
* **Separat SaaS-portal** ger enkel åtkomst till marknadsföringsgruppen utan att behöva gå igenom Azure-hanteringsportalen. 
* **Tillgängligheten för öppna REST API: er** för att integrera och automatisera IT-CRM/CMS-system med öppen plattform API: er, vi tillhandahåller öppna REST API: er och .NET SDK för att använda dessa API: er som gör att du kan enkelt integreras och automatisera med Mobile Engagement. Se [detta](mobile-engagement-api-authentication.md) mer information. 
* **Power BI-anslutningsprogrammet tillgängliga** du kan också dra ut viktiga analytics diagrammen i Power BI-instrumentpanelen. Se den här [guide](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-mobile/)
* **Försäkran för säkerhet och sekretess** Azure Mobile Engagement som en del av Azure-familjen följer alla standard metodtips runt säkerhet och sekretess för en tjänst i molnet.

## <a name="actionable-analytics"></a>**Tillämplig Analytics**
* **Övervaka data i realtid** kan du spåra realtid analytics med hjälp av vår Monitor-modul som innehåller information som sessioner, händelser, fel och krascher i realtid. Ta en titt på den här [artikel](mobile-engagement-concepts.md) att få en förståelse av de grundläggande begrepp. 
  
    ![][1]
  
    ![][2]        
* **Visa aggregerade data** du också få en bättre överblick över aggregerade analytics data med hjälp av vår Analytics-modulen där du kan enkelt filtrera dina data baserat på din app version och tid punkter.
  
    ![][3]        
* **Få insikter om dina användare och kvarhållning mönster**
  
    ![][4]        
* **Få insikter om dina användare kommer från och hur lång tid kan de utgifter på skärmen**
  
    ![][5]        
  
    ![][6]        
* **Ta reda på vilka skärmar är app användarna besöker och hur kan du optimera användaren sökvägen** detta hjälper dem att identifiera skärmar och funktioner som du vill att de ska.
  
    ![][7]        
  
    ![][8]        
* **Få insikter som är de vanligaste händelser i din app och få en förståelse av dina affärsprocesser baserat på dessa händelser** 
  
    ![][9]    
* **Spåra vanliga fel och krascher och få insikter för utvecklare-teamet**
  
    ![][10]        
  
    ![][11]    
* **Förstå vilka enheter och nätverk app användarna ansluter till din app, för att optimera appen** 
  
    ![][12]    

## <a name="targeted--personalized-push-notifications"></a>**Riktad & anpassade Push-meddelanden**
* **Skapa ett segment som är baserade på någon av insamlade data** du kan använda någon av händelse/Session/aktivitet/jobbet/krascher/fel/taggar data för den här.
  
    ![][13]
  
    ![][14]        
* **Spåra historiken för skapade segment-dag under dagen**
  
    ![][15]    
* **Skicka riktade meddelanden** riktad vanligaste som gamla/nya användare etc. eller till din anpassade skapade segment
  
    ![][16]    
* **Skicka både out av app/system och omfattande HTML-baserad i appen push-meddelanden som passar ditt scenario**
  
    ![][17]    
  
    ![][18]    
* **Målet i appen meddelanden som visas på en viss skärm/aktivitet i appen**
  
    ![][19]    
* **Ange en ”åtgärd” när användaren klickar på ett meddelande** det kan vara så enkelt som att bara öppna en webbsida eller navigerar i appen till en viss skärm genom att klicka på meddelandet. 
  
    ![][20]
* **Skicka lokaliserade meddelanden** så att den passar användarna app på det språk som de vill. 
  
    ![][21]    
* **Ange ett start- och tid för dina kampanjer** 
  
    ![][22]    
* **Enkelt testa dina meddelanden** genom att registrera en testenhet och Skicka testmeddelande till den här enheten.
  
    ![][23]    
* **Enkelt att ange ett meddelande i appen ska visas som en snabb avsökning/undersökning**  
  
    ![][24]
* **Hämta push kampanjstatistik** om dina meddelanden som ger dig en uppfattning om hur lyckade har meddelanden.
  
    ![][25]    
* **Enkelt anpassa och ge dina meddelanden med hjälp av taggar-app-info och emojis tecken** 
  
    ![][26]    
  
    ![][27]    
* **Ange Push-gränser för att förhindra att skicka skräppost användare** du inte vill skicka en mängd push-meddelanden till användare av ditt program och gå som massutskick dem. Detta är där funktionen våra Push-gränser är användbar där du kan konfigurera push-gränser på Granulariteten för ett segment. 
  
    ![][28]            

<!-- Images -->
[1]: ./media/mobile-engagement-key-features/monitor1.png
[2]: ./media/mobile-engagement-key-features/monitor2.png
[3]: ./media/mobile-engagement-key-features/analytics-filter.png
[4]: ./media/mobile-engagement-key-features/retention.png
[5]: ./media/mobile-engagement-key-features/analytics-geomap.png
[6]: ./media/mobile-engagement-key-features/analytics-session-length.png
[7]: ./media/mobile-engagement-key-features/analytics-activities.png
[8]: ./media/mobile-engagement-key-features/analytics-userpath.png
[9]: ./media/mobile-engagement-key-features/analytics-events.png
[10]: ./media/mobile-engagement-key-features/analyics-errors.png
[11]: ./media/mobile-engagement-key-features/analyics-errors-details.png
[12]: ./media/mobile-engagement-key-features/technicals.png
[13]: ./media/mobile-engagement-key-features/segment.png
[14]: ./media/mobile-engagement-key-features/segment-creation.png
[15]: ./media/mobile-engagement-key-features/segment-history.png
[16]: ./media/mobile-engagement-key-features/segment-push.png
[17]: ./media/mobile-engagement-key-features/out-of-app.png
[18]: ./media/mobile-engagement-key-features/in-app-push.png
[19]: ./media/mobile-engagement-key-features/push-in-activity.png
[20]: ./media/mobile-engagement-key-features/push-action.png
[21]: ./media/mobile-engagement-key-features/push-languages.png
[22]: ./media/mobile-engagement-key-features/push-timeframe.png
[23]: ./media/mobile-engagement-key-features/push-test.png
[24]: ./media/mobile-engagement-key-features/push-poll.png
[25]: ./media/mobile-engagement-key-features/push-stats.png
[26]: ./media/mobile-engagement-key-features/push_personalized.png
[27]: ./media/mobile-engagement-key-features/push_emoji.png
[28]: ./media/mobile-engagement-key-features/push_limits.png









