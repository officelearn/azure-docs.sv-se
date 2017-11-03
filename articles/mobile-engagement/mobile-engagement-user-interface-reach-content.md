---
title: "Användargränssnittet för Azure Mobile Engagement - Reach-innehåll"
description: "Lär dig att hantera unika innehållet i de olika typerna av kampanjer för push-meddelanden i Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3741a43b74af5846e95e42d8a7b533621e780f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Så här hanterar du unikt innehållet i de olika typerna av kampanjer för push-meddelande
Du kan använda avsnittet innehåll för en ny kampanj räckvidden för att ändra innehållet i dina meddelanden, avsökningar, Data-push både och paneler (endast Windows Phone). Push-kampanjer innehåll inställningen är specifik för typ av kampanj. 

### <a name="content-types"></a>Typer av innehåll:
* Meddelanden
* Omröstningar
* Data push-meddelanden
* Paneler (endast Windows Phone)

## <a name="content-of-announcements"></a>Innehållet i meddelanden
 ![Reach-Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Välj typ av ditt meddelande:
* Endast meddelande: det är ett enkelt standard meddelande. Vilket innebär att om en användare klickar på den, ingen extra vy visas, men endast den åtgärd som är kopplade till den sker.
* SMS-meddelande: det är ett meddelande som snabbt tillkallar användaren måste ha en titt på textvyn.
* Web meddelande: det är ett meddelande som snabbt tillkallar användaren att titta på en webbvy.

### <a name="see-also"></a>Se även
* [Nå - hur Tos - meddelanden][Link 3] 

### <a name="about-web-view-announcements"></a>Om Webbvyaviseringar:
Förekomster av mönstret ”{deviceid}” i HTML-kod eller JavaScript-kod som du anger här ersätts automatiskt av ID: t för den enhet som visar ett meddelande. Detta är ett enkelt sätt att hämta Azure Mobile Engagement enhetsidentifierare i en extern webbtjänst som ditt BackOffice är värd.
Om du vill skapa en webbvy i helskärm (utan de standardknappar för Åtgärd och Avsluta som vi tillhandahåller) kan du använda följande funktioner från din webbvyaviserings JavaScript-kod: 

* genomför Meddelandeåtgärden: ReachContent.actionContent()
* Avsluta från meddelandet: ReachContent.exitContent()

### <a name="choose-your-action"></a>Välj åtgärden:
### <a name="about-action-urls"></a>Om åtgärden webbadresser:
Varje URL som kan tolkas av en målenhets operativsystem kan användas som en åtgärds-URL.
Varje dedikerad URL som ditt program eventuellt stöder (t.ex. för att få användarna att går direkt till en viss skärm) kan också användas som en åtgärds-URL.
Varje förekomst av {deviceid}-mönstret ersätts automatiskt av ID: t för den enhet som genomför åtgärden. Detta kan användas för att lätt kunna hämta Azure Mobile Engagement enhetsidentifierare via en extern webbtjänst som ditt BackOffice är värd.

* **Android och iOS åtgärder**
  * Öppna en webbsida
  * http://\[web site domäner\] 
  * Exempel: http://www.azure.com
  * Skicka ett e-postmeddelande
  * mailto:\[e-post-mottagaren\]? ämne =\[ämne\]& body =\[meddelande\] 
  * Example:mailto:foo@example.com? ämne = helg % 20from % 20Azure % 20Mobile % 20Engagement! & body = bra % 20stuff!
  * Skicka ett SMS
  * SMS:\[-telefonnummer\] 
  * Exempel: sms:2125551212
  * Ring upp ett telefonnummer
  * Tel:\[-telefonnummer\] 
  * Exempel: tel:2125551212
* **Android endast åtgärder**
  * Hämta ett program på Play Store
  * market://details?ID=\[app-paket\] 
  * Exempel: market://details?id=com.microsoft.office.word
  * Starta en geolokaliserad sökning
  * GEO:0 0? q =\[sökfråga\] 
  * Exempel: geo:0, 0? q = starbucks paris
* **endast åtgärder för iOS**
  * Hämta ett program på App Store
  * http://iTunes.Apple.com/ [Land] /app/ [programnamn] /id [app-id]? huvudmålservern = 8 
  * Exempel: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Windows-åtgärder
  * Öppna en webbsida
  * http://\[web site domäner\] 
  * Exempel: http://www.azure.com
  * Skicka ett e-postmeddelande
  * mailto:\[e-post-mottagaren\]? ämne =\[ämne\]& body =\[meddelande\] 
  * Example:mailto:foo@example.com? ämne = helg % 20from % 20Azure % 20Mobile % 20Engagement! & body = bra % 20stuff!
  * Skicka ett SMS (Skype Store App krävs)
  * SMS:\[-telefonnummer\] 
  * Exempel: sms:2125551212
  * Ring upp ett telefonnummer (Skype Store App krävs)
  * Tel:\[-telefonnummer\] 
  * Exempel: tel:2125551212
  * Hämta ett program på Play Store
  * MS-windows-store: PDP? PFN =\[app paket-ID\] 
  * Exempel: ms-windows-store: PDP? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Starta en bingkartssökning
  * bingmaps:? q =\[sökfråga\] 
  * Exempel: bingmaps:? q = starbucks paris
  * Använd ett anpassat schema
  * \[anpassat schema\]://\[anpassade schemat parametrar\] 
  * Exempel: myCustomProtocol://myCustomParams
  * Använd paketdata (Store App för tillägg läsa krävs)
  * \[mappen\]\[data\].\[ tillägg\] 
  * Example:myfolderdata.txt

### <a name="build-a-tracking-url"></a>Skapa en spårning-URL:
* Se avsnittet ”inställningar” i den <UI Documentation> för instruktioner om hur du skapar en spårning URL som gör att användarna kan ladda ned en av dina andra program.

### <a name="define-the-texts-of-your-announcement"></a>Definiera texterna för ditt meddelande
Fyll i rubrik, innehåll och knappen texterna för ditt meddelande. Du kan rikta en målgrupp framtida kampanjens på reach-feedback på hur användare svarat på kampanjen. Målgrupper kan baseras på feedback på om den här kampanjen har precis pushas, besvarade, hanterats eller stängts.

### <a name="see-also"></a>Se även
* [UI-dokumentationen – Reach - nytt Push kriterium][Link 28]

## <a name="content-of-polls"></a>Innehållet i avsökningar
![Reach-Content2][31] 

Fyll i rubrik, beskrivning och knappen texterna för ditt meddelande. Lägg sedan till frågor och alternativ för svar på dina frågor.
Du kan rikta en målgrupp framtida kampanjens på reach-feedback på hur användare svarat på kampanjen. Målgrupper kan baseras på om den här kampanjen har precis pushas, besvarade, hanterats eller stängts. Målgrupper kan även baseras på avsökning besvara feedback, där fråga och svar val användas som kriterier.

### <a name="see-also"></a>Se även
* [UI-dokumentationen – Reach - nytt Push kriterium][Link 28]

## <a name="content-of-data-pushes"></a>Innehållet i Data push-meddelanden
![Reach-Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Välj typ av data:
* Text
* Binära data
* Base64-data

### <a name="define-the-content-of-your-data"></a>Definiera dina datas innehåll
* Om du valde för att skicka textdata, kopiera och klistra in texten i rutan ”innehåll”.
* Om du har valt för att skicka eller binary base64-data kan du använda knappen ”ladda upp filen” för att överföra din fil.
* Du kan rikta en målgrupp framtida kampanjens på reach-feedback på hur användare svarat på kampanjen. Målgrupper kan baseras på om den här kampanjen har precis pushas, besvarade, hanterats eller stängts.

### <a name="see-also"></a>Se även
* [UI-dokumentationen – Reach - nytt Push kriterium][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Innehållet i paneler (endast Windows Phone)
![Reach-Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definiera din panels innehåll
Nyttolasten i panelen är text som ska visas i panelen för din app på Windows Phone-enheter.
En sida vid sida-push är Microsoft Push Notification Service (MPNS) version av en intern push för Windows Phone. Panelen push-typen är den enda push-typ som inte har ett svar och så målgruppen för framtida kampanjer inte kan byggas på resultatet av en panel push-kampanj. 

### <a name="see-also"></a>Se även
* [API-dokumentationen - Reach-API - interna Push][Link 4]

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

