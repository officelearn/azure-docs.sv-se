---
title: Användargränssnittet för Azure Mobile Engagement - Reach
description: Lär dig att nå ut till användare av ditt program med push-meddelanden med Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Hur du nå ut till användare av ditt program med push-meddelanden
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Den här artikeln beskriver den **NÅ** för den **Mobile Engagement** portal. Du använder den **Mobile Engagement** portalen för att övervaka och hantera dina mobila appar. Observera att börja använda portalen måste du först skapa en **Azure Mobile Engagement** konto. Mer information finns i [skapa ett Azure Mobile Engagement-konto](mobile-engagement-create.md).

Avsnittet Reach i Användargränssnittet är Push-kampanj hanteringsverktyg som du kan skapa/redigera/aktivera/Slutför/Övervakare och få statistik på Push notification-kampanjer och funktioner som kan även nås via API: et nå (och vissa delar av låga Push-API) . Kom ihåg att om du använder API: erna eller Användargränssnittet, behöver du integrera räckvidd i programmet för varje plattform med SDK innan du kan använda och Azure Mobile Engagement nå kampanjer.

> [!NOTE]
> Många avsnitt i den **Mobile Engagement** portal Användargränssnittet innehåller den **Visa hjälp** knappen. Tryck på knappen för att få mer information om ett avsnitt.
> 
> 

## <a name="four-types-of-push-notifications"></a>Fyra typer av Push-meddelanden
1. Meddelanden – kan du skicka reklam meddelanden till användare som omdirigerar dem till en annan plats i din app och skicka dem till en webbsida eller store utanför din app. 
2. Omröstningar - kan du samla in information från slutanvändarna genom att ställa frågor till dem.
3. Data-push - kan du skicka en binär fil eller base64-datafil. Informationen i en data-push skickas till appen och ändra din aktuella användarupplevelsen i din app. Programmet måste kunna bearbeta data i en data-push.

## <a name="campaign-details"></a>Information om kampanjer
Du kan redigera klona, ta bort eller aktivera kampanjer som inte har aktiverats ännu hovrar över deras namn eller kan du öppna dem. Du kan också klona kampanjer som redan har aktiverats av användaren håller muspekaren över deras namn eller kan du öppna dem. Du kan inte ändra en kampanj när den väl har aktiverats.

![Reach1][18]

## <a name="reach-feedback"></a>Nå Feedback
Klicka på **statistik** visas detaljerad information om en Reach-kampanj. Den **enkel** vyn innehåller en bild i form av ett stapeldiagram i kolumnen om vad som hänt efter en kampanj har aktiverats. Den **Avancerat** vyn innehåller mer detaljerad information om push-kampanj. Dessa uppgifter är inte tillgängligt om du skickar ett testkampanj d.v.s. en push skickas till en testenhet. Här visas hur du ska tolka dessa uppgifter:

1. **Pushas** – detta anger antalet meddelanden som har pushats till enheter. Det här antalet beror på den målgrupp som du angav när du skapar push-kampanj. Om du inte anger någon målgrupp, sedan skickas den här push till registrerade enheter. Precis som alla push-tjänster vi inte push-meddelanden direkt till enheter men i stället push-installera dem respektive plattforms specifika Push Notification Services (PNS - WNS-APN/GCM) så att de kan skicka meddelanden till enheterna. 
2. **Leverera** – detta anger antalet meddelanden som har levererats av pns-systemet till enheten och bekräftas som tagits emot av Mobile Engagement SDK. 
   
   *Orsaker till levererade antalet är mindre än antalet intryckt:*
   
   1. Om användaren har avinstalleras appen från enheten och pns-systemet inte känner till den när vi skickar push-meddelandet till pns-systemet bort meddelandet.
   2. Om enheten har appen men själva enheterna var offline under långa tidsperioder, misslyckas pns-systemet att leverera meddelandet till enheten. 
   3. Om meddelandet levereras till enheten men Mobile Engagement SDK i appen inte kan identifiera innehållet i meddelandet, släpper meddelandet. Detta kan inträffa om anpassning av meddelanden i appen genererar ett undantag som vi fånga i SDK och släpp meddelandet. Detta kan även uppstå om appen på enheten använder en version av Mobile Engagement SDK som inte kan förstå den nya versionen av push-meddelande som skickas från plattformen men detta är endast när appen har uppgraderats när meddelandet skickades från t han service plattform. Den **Avancerat** fliken talar om hur många meddelanden har tagits bort. 
   4. På iOS-enheter meddelanden ibland inte levereras om antingen enheten är för låg batterinivå eller om appen förbrukar för mycket energi vid bearbetning av fjärr-meddelanden. Detta är en begränsning av iOS-enheter.   
3. **Visas** – detta anger antalet meddelanden som har upp till app-användare på enheten i form av en systemavisering push/out-för-app i meddelandecentret eller ett meddelande i appen i mobilappen.  Den **Avancerat** fliken talar om hur många har systemmeddelanden och hur många har meddelanden i appen. 
   
   *Orsaker till visas antalet är mindre än levererade antal (väntar på att visas)*
   
   1. Om meddelandekampanj hade ett slutdatum på det är det möjligt att meddelandet levererades men vid tiden kommer att öppna och visa det app-användaren kan det har gått så att det har aldrig visas.   
   2. Om meddelandet är ett meddelande i appen och sedan meddelandet visas bara när app-användare öppnar appen. I fall där appen användaren inte har öppnat appen rapporterar SDK att meddelandet kunde levereras men har ännu inte visas förrän den har öppnats. 
   3. Om meddelandet är ett meddelande i appen och konfigurerats för att visas på en specifik aktivitet/skärm och sedan också meddelandet kommer att rapporteras som levererats men ännu inte levereras till öppnar användaren appen på en viss skärm. 
4. **Användarinteraktioner** – detta anger antalet meddelanden som appen användaren har har åtgärdat med och meddelanden som har hanterats eller stängts tas med. 
   
   * *App-användaren kan åtgärda ett meddelande i något av följande sätt:*
     
     1. Om meddelandet är ett system/out-för-app-meddelande eller en app meddelande meddelande endast sedan app användaren klickar på meddelandet.
     2. Om meddelandet är ett meddelande i appen med en text eller Webbvy eller avsökningar sedan app användaren klickar på knappen åtgärd i meddelandet.
     3. Om meddelandet är ett meddelande i appen med en webbvy app användaren klickar på en URL i webbvyn [Android endast]
   * *App-användaren kan avsluta ett meddelande i något av följande sätt:*
     
     1. Klicka på knappen Stäng på meddelandet direkt. 
     2. Svepa in eller ta bort meddelandet. 
     3. Meddelanden i appen med text/webbinnehåll och avsökningar visas vanligtvis till app-användare i två steg. Ett meddelande visas först och när de klickar på den de se efterföljande text/web/avsökning innehållet. App-användaren kan avsluta ett meddelande i någon av de här stegen och informationen i Avancerat läge samlar in det här. 
5. **Åtgärdade** – detta anger antalet meddelanden som uttryckligen åtgärdade av app-användare. Detta är det mest intressanta antalet detta anger hur många användare som appen har intresse av du push-installeras i meddelandet för meddelandet. 

> [!NOTE]
> På iOS & Windows-plattformar, om användaren har appen öppna och kampanjen var kampanj ”när som helst” och det är möjligt att båda utanför appen och meddelanden i appen visas på samma gång. Detta kan orsaka en högre än levererat visas antalet. Om användaren interagerar eller åtgärder meddelandet och även antalet användare interaktioner/Actioned kan vara större än levererade. 
> 
> 

![Reach2][19]

## <a name="see-also"></a>Se också
* [Begrepp][Link 6]
* [Felsöka Guide Service][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

