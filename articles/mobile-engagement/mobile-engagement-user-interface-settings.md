---
title: "Användargränssnittet för Azure Mobile Engagement - inställningar"
description: "Lär dig att hantera de globala inställningarna för ditt program med Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: af5c81df2b9f288161b38625d3ac2adde8fb195d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Så här hanterar du de globala inställningarna för programmet
Den **inställningar** menyalternativ som är tillgängliga för ett program varierar beroende på plattform och de behörigheter som du har beviljats för programmet. Inställningarna innefattar följande: information, projekt, Native Push, Push-hastigheten, Tag (appinfo) och kommersiellt tryck. Menyalternativet taggen (appinfo) i avsnittet inställningar kan hanteras av ditt program (med SDK) eller din serverdel (med hjälp av Device API). 

> [!NOTE]
> Många avsnitt i den **Mobile Engagement** portal Användargränssnittet innehåller den **Visa hjälp** knappen. Tryck på knappen för att få mer information om ett avsnitt.
> 
> 

## <a name="details"></a>Information
Kan du ändra namn och beskrivning av programmet, visa ägaren av ditt program och dina rollbehörigheter. 

Konfiguration av kan du visa och ändra veckor starta på dagen och tiden för datakvarhållning i dag.

  ![settings1][46]

## <a name="projects"></a>Projekt
När du vill välja alla projekt som du vill att programmet ska visas i. 

Du kan också söka efter ett projekt och visa namn, beskrivning, ägare och dina rollbehörigheter för alla projekt som ditt program är en del av.

Mer information finns: [UI-dokumentationen – Start][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Intern Push
Kan du registrera ett nytt certifikat eller ta bort och befintliga certifikat för användning med intern push. Intern Push aktiverar Azure Mobile Engagement att skicka till ditt program när som helst, även när den inte körs. 

När att tillhandahålla autentiseringsuppgifter eller certifikat för minst en Native Push-tjänst, kan du välja ”helst” när du skapar Reach-kampanjer och Använd parametern ”meddelaren” i PUSH-API.

### <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
Om du vill aktivera Native Push med Apple Push Notification Service behöver du registrera ditt certifikat. Du måste ange vilken typ av certifikat som utveckling (utveckling) eller produktion (PROD). Du kommer sedan måste överföra ditt certifikat och lösenord.

Mer information finns: [- iOS - SDK-dokumentationen hur du förbereder ditt program för Apple Push-meddelanden][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Windows Push Notification Service (WPNS)
Om du vill aktivera systemspecifik push-avisering med Windows Notification Service måste du tillhandahålla ditt programs autentiseringsuppgifter. Du behöver dina paket säkerhetsidentifierare (SID) och din hemliga nyckel.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging för Android (GCM)
Om du vill aktivera Native Push med GCM måste du följer du anvisningarna från Google. Sedan måste du klistra in en server enkla API-nyckel, konfigurerats utan IP-begränsningar. Kräver integrering med SDK för Android v1.12.0 +.

Mer information finns i: 

* [Android för SDK-dokumentationen hur du integrerar GCM][Link 5]
* [Google GCM utvecklarhandboken](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon Device Messaging för Android (ADM)
Om du vill aktivera Native Push med ADM, måste du ange Amazon <OAuth credentials> som består av en klient-ID och Klienthemlighet (kräver integrering med SDK för Android v2.1.0 +).

Mer information finns i: 

* [Android för SDK-dokumentationen hur du integrerar ADM][Link 5]
* [Amazon Developer ADM-dokumentation](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Push-hastigheten
Visar aktuella push-hastigheten för ditt program och kan du definiera push-hastigheten för ditt program.

  ![settings7][52]

## <a name="tag-app-info"></a>Taggen (appinfo)
![settings11][56]

## <a name="commercial-pressure"></a>Kommersiellt tryck
![settings12][57]

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

