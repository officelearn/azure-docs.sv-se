---
title: Azure Mobile Engagement användargränssnittet - Start
description: Lär dig att hantera dina befintliga program och projekt med hjälp av Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c0379e51a30927af88367fc687d8f8ce66eb827f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>Så här hanterar du ditt befintliga program och projekt
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Den här artikeln beskriver den **Start** sida av den **Mobile Engagement** portal. Du använder den **Mobile Engagement** portalen för att övervaka och hantera dina mobila appar. Observera att börja använda portalen måste du först skapa en **Azure Mobile Engagement** konto. 

Gå till startsidan, klicka på **hem** på upp till vänster på sidan. Den innehåller en lista över alla program som ingår i den valda samlingen. På den här sidan se du bara en snabb överblick över dina program.

Sidan innehåller även alla projekt som kan innehålla alla program som finns i ditt konto. Observera att alla kan komma åt sidan av Gränssnittet genom att skapa ett konto, men du behöver ge behörighet till andra användare för att de ska ha åtkomst till dina anpassade program i **Mina projekt**.

Du kan också visa adressutrymmet för de valda programmen. Eller välj att visa adressutrymmet för valda program i ett projekt.

![Home1][0]

## <a name="my-applications"></a>Mina program
Snabb överblick över dina program kan du välja vilka program som du vill öppna om du vill visa detaljerad menyfliksområdet. Du kan klicka på namnet på ditt program för att återgå till den senast besökta menyfliksområdet platsen i ditt program, eller klicka på ikonen växeln gå direkt till sidan ”Inställningar” i ditt program. Du kan söka, filtrera eller sortera informationen som visas i program-tabeller. Du kan också dra och släpp kolumnrubrikerna Om du vill ändra ordningen.

Översikt över dina program innehåller bland annat:

* **Ny användare trend**: utvecklingen av nya användare under de senaste två veckorna.
* **Aktiva användare**: antal aktiva användare under de senaste 30 dagarna.
* **Aktiva användare trend**: utvecklingen av aktiva användare under de senaste två veckorna.
* **Sessioner**: en session är en användning av programmet som utförs av en användare, från tidpunkten då användaren startar med hjälp av det, tills upphör.
* **Sessionen trender**: utvecklingen av sessioner under de senaste två veckorna.

När du klickar på ett program kan börja du övervaka och hantera dina appar via Användargränssnittet. Exempel:    

* [Övervaka realtidsdata för appen](mobile-engagement-user-interface-monitor.md)
* [Analysera historiska data för appen](mobile-engagement-user-interface-analytics.md)
* [Skapa och hantera användarsegment för att identifiera användningsmönster](mobile-engagement-user-interface-segments.md)
* [Nå ut till appanvändarna med push-meddelanden](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>Mina projekt
Du kan använda projekt för att gruppera dina program och ge behörighet till andra användare att komma åt dina program. Du kan ge behörighet till andra användare genom att ange e-postadress. Den **nytt projekt** knappen om du vill skapa ett nytt projekt genom att bara ange ”namn” och ”beskrivning” det nya projektet. När projektet har skapats kan klicka du på projektnamnet att redigera namn och beskrivning av produkten och välj de program som du vill visa i det här projektet.

![Home6][60]

Roller är:

* **Visningsprogrammet**: A Viewer är en användare kan bara visa program som hör till ett projekt. Ett visningsprogram kan komma åt analytics och övervaka data och titta på Reach resultat. Ett visningsprogram kan inte ändra någon information eller hantera program eller användare. Ett visningsprogram kan inte skapa eller ändra Reach kampanjstatus.
* **Utvecklare**: A utvecklare är en användare som kan göra allt ett visningsprogram kan göra, samt hantera program. En utvecklare kan aktivera och inaktivera program, ändra programinformationen (till exempel och signaturmappar) och skapa Reach-kampanjer. En utvecklare kan inte hantera användare.
* **Administratören**: en administratör är en användare som kan göra allt en utvecklare kan göra, samt hantera användare. En administratör kan erbjuda användare att ansluta till ett projekt kan ändra användarroller och ändra projektets information. Behörighet på användarnivå kan också anges i ”inställningar”.

Klicka på ett projekt för att visa alla program som ingår i det här projektet. Följande bild visar adressutrymmet för de valda programmen.

![Home2][3]

## <a name="see-also"></a>Se också
* [Begrepp][Link 6]
* [Felsöka Guide Service][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
