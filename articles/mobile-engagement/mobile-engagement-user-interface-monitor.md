---
title: "Användargränssnittet för Azure Mobile Engagement - Övervakare"
description: "Lär dig hur du övervakar data i realtid om ditt program med Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 5f8a02e35db93585e0fe46d77b3ad18b94c99597
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>Övervaka realtidsdata för appen
Den här artikeln beskriver den **ÖVERVAKAREN** för den **Mobile Engagement** portal. Du använder den **Mobile Engagement** portalen för att övervaka och hantera dina mobila appar. Observera att börja använda portalen måste du först skapa en **Azure Mobile Engagement** konto. 

Avsnittet Övervakare i Användargränssnittet innehåller realtidsanalys information och du kan ställa in varningar när tröskelvärden nås för de flesta av samma information som är tillgänglig tidigare i den [ANALYTICS](mobile-engagement-user-interface-analytics.md) avsnitt i Användargränssnittet. Finns den **ordlista** i avsnittet den [begrepp](http://go.microsoft.com/fwlink/?LinkId=525555) avsnittet definitioner av uttryck och förkortningar i analyser och övervakning (till exempel följande: aktiva användare, nya användare, behålls användare, Session, användaren sökväg Diagram, mappa användare, spårning URL: er, trender, aktivitet, händelse, jobb, fel, Extra information, krascher och App-info).

> [!NOTE]
> Många avsnitt i den **Mobile Engagement** portal Användargränssnittet innehåller den **Visa hjälp** knappen. Tryck på knappen för att få mer information om ett avsnitt.
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>Övervakaren - sessioner, jobb, händelser, fel och krascher
Du kan se hur många användare är för närvarande i sessionen och specifika skärmar eller göra vissa åtgärder. Du kan visa användaraktivitet dividerat med sessioner, jobb, händelser, fel och krascher. Du kan se den aktuella informationen och visa information från den senaste timme, dag eller vecka. Du kan se all information i varje kategori eller sortering av viss Session, jobb, händelse, fel och krascher.  Live övervakning är bra att använda under händelser, t.ex en Push-kampanj för att se om det finns en uptick i praktiken rätt när du skickar Push-meddelande.

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>Felsöka med Monitor - händelser - information
Generera en händelse i ditt program från enheten test och hitta i övervakaren - händelser - information är en av de enklaste sätten att hitta enhets-ID för enheten test och för att bekräfta att Azure Mobile Engagement-integreringen av Analytics, övervakning, och Segment fungerar från ditt program. När du har enheten test enhets-ID, kan du lägga till den din testenheter i ”Mina konto--enheter”. Om du inte kan generera en händelse, se till att Azure Mobile Engagement korrekt är integrerat i din Android/iOS/Web/Windows/Windows Phone-app med SDK.

Mer information finns: [SDK-dokumentationen][Link 5]

![Monitor2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>Felsöka med Monitor kraschar - - information
Du kan granska information om kraschen uppges om din app från övervakaren - krascher - information för att avgöra varför en app kraschar. Du bör också söka efter kända problem med varje version av SDK i viktig information för varje version av SDK för Android/iOS/Web/Windows/Windows Phone.

Mer information finns: [SDK-dokumentation – viktig information][Link 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>Övervakaren - aviseringar
Du kan också ange villkor för aviseringar som skickas automatiskt till dig via e-post eller snabbmeddelande. (XMPP-kompatibla tjänster som Googles GTalk eller Apples iChat stöds.) Aviseringar baseras på en fördefinierad identifiering tröskel större än (>) eller mindre än (<) ett visst antal sessioner, jobb, händelser, fel eller krascher per sekund, minut eller timme. Aviseringar kan övervaka alla aktiviteter av en viss typ, eller bara övervaka en specifik aktivitet jobbet, händelse eller fel. 

Du kan även ange en minsta Identifieringstakt, vilket är den minsta mängden minuter som separeras två meddelanden för samma avisering att säkerställa att när aviseringen utlöses får du ett aldrig fler än 1 meddelande per intervall som anges.

![Monitor4][17]

## <a name="see-also"></a>Se även
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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
