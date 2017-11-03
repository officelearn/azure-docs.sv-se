---
title: "Användargränssnittet för Azure Mobile Engagement - Reach så"
description: "Användargränssnitt, översikt för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 33a0a9d0c399cb7f0a791c4c16dde2e2d62364ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Hur du kommer igång med och hantera push-meddelanden nå ut till användarna
När SDK är helt integrerat i din app, du kan börja använda den Reach-avsnittet i Användargränssnittet för Push-meddelanden för användarna i din app.  

## <a name="do-your-first-push-notification-campaign"></a>Gör din första Push-Meddelandekampanj
* Bekräfta att räckhåll är integrerat i din app med SDK. 
* Välj ditt program

![First1][1]

* Gå till avsnittet ”Reach” och klicka på ”nya meddelandet”

![First2][2]

* Skapa en ny kampanj och ge den namnet
  
![First3][3]

* Välj hur meddelandet ska skickas, som i appen bara

![First4][4]

* Skapa meddelande som du vill skicka

![First5][5]

* Du kan skriva en titel på meddelandet (valfritt).
* Skriva innehållet för push-meddelandet.
* Du kan ladda upp en bild. Tänk på att storleken på filen inte får överstiga 32 768 byte.
* Du har också möjlighet att välja fler alternativ, men för fokus för den här självstudiekursen kommer vi att se som senare.
* Välj innehållstypen som meddelande endast

![First6][6]

* Skapa din push-kampanj och kommer att visas i listan kampanj.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testa din kampanj med Push-meddelanden
![test1][8]

* Registrera din enhet.
* Klicka på kryssrutan på den enhet som du vill skicka.
* Klicka på knappen ”Test” för att skicka push-meddelandet till enheten.

![Test2][9]

* Aktivera kampanjen

![test3][10]

* Nu när du har skapat din kampanj måste du aktivera den för meddelandet ska skickas till användarna.

## <a name="send-personalized-pushes"></a>Skicka push-meddelanden anpassade
* Det här exemplet skapar en distribution där en anpassad rabatt-kod har angetts i push-meddelande.

![Personalize1][11]

Anpassning fungerar genom att ersätta en markör av från en app-info tagg så har du se till att användaren har rätt app-info definieras först. I det här exemplet har utvalda användare en app info-tagg med namnet rebate_code som definierats.
Som du ser ovan innehåller push notification innehållet markör ${rebate_code} som visar att det är som ska ersättas med det faktiska innehållet i app-info-taggen.

> [!WARNING]
> Om taggen app-info inte har definierats för användaren, får inte push-meddelandet.

* Resultat

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Du kan anpassa texten som ditt meddelande
![Personalize3][13]

* Rubrik på meddelandet, inklusive
* Och innehållet i meddelandet.
* Välj typ av meddelande (textvy eller webbvy)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Brödtexten i ett meddelande kan också anpassas med:
* Åtgärds-URL bör du vill anpassa landningssida
* Titeln,
* Innehållet i meddelandet.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Skilja mellan din Push Notification (i eller utanför appen)
* Välj typ av meddelande du push, Välj ditt program, finns i avsnittet ”nå”, väljer eller skapar en push-kampanj och gå till avsnittet ”meddelanden”.
* Klicka på ”leveransläget” som du vill.
* Klicka på kryssrutan ”begränsa aktiviteter” om du vill att aviseringen uppstår på specifika aktiviteter (skärmar).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Leveransläget ”endast utanför appen
![Differentiate2][16]

”Endast utanför appen” leveransläge ger push-meddelanden när programmet avslutas. Detta är standard push-meddelande.
När du väljer ”endast utanför appen” måste du har redan angett certifikat från den plattform som programmet skapar på (APN eller GCM).

### <a name="see-also"></a>Se även
* [Certifikat för Apple Push Notification Service –](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging-certifikat](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>”i appen bara” leveransläge
![Differentiate3][17]

”I appen bara” leveransläge ger push-meddelanden när programmet körs.
Du behöver inte gå igenom APN och GCM-systemet för det här meddelandet.
Du kan använda i appen leverans system för att nå dina slutanvändare.
Du kan helt anpassa meddelandet och bestämma i vilken aktivitet (skärm) meddelandet ska visas.

### <a name="anytime-delivery-mode"></a>”När som helst” leveransläge
Du kan välja ett ”när som helst” leveransläge, säkerställer du att nå slutanvändaren om programmet körs eller inte.
När du väljer ”när som helst” måste du har redan angett certifikat från den plattform som programmet skapar (APN eller GCM). 

## <a name="schedule-a-push-campaign"></a>Schemalägga en Push-kampanj
### <a name="plan-to-start-a-campaign"></a>Planera att starta en kampanj
![Shedule1][18]

Det är den 21 av mars och du har ett meddelande att göra och hyvlat för den 22 av mars vid midnatt. Du behöver inte vara framför gränssnittet för att göra en push! Du kan planera i förväg exakt minut meddelanden ska skickas.

* Avmarkera ”None” kryssrutan och välj en starttid 
* Välj datumet och tidpunkt som du vill starta push-kampanj.

### <a name="plan-to-end-a-campaign"></a>Planera att avsluta en kampanj
![Shedule2][19]

Du vill kampanjen slutar på det 25 av mars klockan 3:00, men du vet att du inte kan göra det.
Du behöver inte vara framför gränssnittet till push! Du kan planera i förväg exakt minut kampanjen slutar.

* Klicka på ”None” kryssrutan eller välj en sluttid
* Välj datumet och tidpunkt som du vill slutföra push-kampanj.

### <a name="end-a-campaign-manually"></a>Avsluta en kampanj manuellt
![Shedule3][20]

Som standard till ”None”-kryssrutorna är markerade.
Det innebär att kampanjen startar så fort du aktivera den i avsnittet reach och avslutas när du vill sluta på reach-avsnittet.

> [!NOTE]
> Kampanjer som skapats utan ett slutdatum lagra push lokalt på enheten och visar den nästa gång appen öppnas även om kampanjen manuellt avslutas.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Förbättra ett Push-meddelande med en textvy
### <a name="what-is-a-text-view"></a>Vad är en textvy?
![TextView1][21]

En textvy som är ett popup-fönster med textinnehåll. Det här popup-fönstret visas när slutanvändaren har klickat på push-meddelandet.
En textvy kan du visa mer innehåll till slutanvändaren. Det är också möjlighet att lämna ett anrop till åtgärd, till exempel hoppa till en sida för din app, omdirigering till en butik, öppna en webbsida, e-post, startar en geolokaliserade sökning, etc...

### <a name="example-text-view"></a>Exempel: Textvy
* Skapa din Push-meddelandekampanj i avsnittet ”Reach” och namnge din kampanj

![TextView2][22]

* Skriva ett meddelande som ska visas på meddelandet.
* Välj ”text” meddelande innehållstyp

![TextView3][23]

> [!NOTE]
> När du överför en textvy kommer alltid med ett meddelande först. 

* Definiera texten (efter att ha markerat meddelande textinnehåll i underavsnittet visas så att du kan definiera den text som du vill ska visas.)

![TextView4][24]

* Skriva rubrik som visas överst i meddelandet.
* Skriva innehållet i textvyn.
* Skriv det innehåll som visas på åtgärdsknappen (en med åtgärdsknappen kan programmet för att göra något, till exempel öppna en sida i programmet, omdirigering till en App-butik eller alla typer av datakällor som du kan ange).
* Skriv innehållet som ska visas på knappen Avsluta (genom att klicka på knappen Stäng, textvyn försvinner.)
* Skapa din push-meddelandekampanj och den visas på listan kampanj.

![TextView5][25]

* Aktivera push-meddelandekampanj för att skicka textvyn till användarna.

![TextView6][26]

* Resultat

![TextView7][27]

* Användaren får ett meddelande och klicka på det.
* Textvyn visas som ett popup-fönster som tillåter användaren att interagera med den.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Förbättra ett Push-meddelande med en webbvy
### <a name="what-is-a-web-view"></a>Vad är en webbvy?
![WebView1][28]

En webbvy är ett popup-fönster med webbinnehåll. Det här popup-fönstret visas när slutanvändaren har klickat på push-meddelandet.
En webbvy möjliggör mer interaktion med slutanvändaren.
Det är också möjlighet att lämna ett anrop till åtgärd, till exempel omdirigering till App Store, öppna en webbsida, e-post, starta en geolokaliserade sökning, etc...

### <a name="example-web-view"></a>Exempel: Webbvy
* Skapa din Push-kampanj i avsnittet ”Reach” och namnge din kampanj.

![WebView2][29]

* Skriva ett meddelande som ska visas på meddelandet.
* Välj typen av meddelande-innehåll som ”web”

![WebView3][30]

### <a name="about-announcement-types"></a>Om meddelandet typer:
* Endast meddelande: det är ett enkelt standard meddelande. Vilket innebär att om en användare klickar på den, ingen extra vy visas, men endast den åtgärd som är kopplade till den sker.
* SMS-meddelande: det är ett meddelande som snabbt tillkallar användaren måste ha en titt på textvyn.
* Web meddelande: det är ett meddelande som snabbt tillkallar användaren att titta på en webbvy.
  Välj innehållet som ”Web meddelandet”.

> [!NOTE]
> När du överför en webbvy kommer alltid med ett meddelande först.

* Definiera webbinnehållet (efter att ha valt meddelande webbinnehåll underavsnittet visas så att du kan definiera den webbinnehåll du vill ska visas.)

![WebView4][31]

* Skriva rubrik som visas överst i meddelandet (valfritt).
* Skriv in HTML-koden här.
* Klicka på knappen för att växla edition och se hur den ser ut för redigering.
* Skriv det innehåll som visas på åtgärdsknappen (en med åtgärdsknappen kan programmet för att göra något, till exempel öppna en sida i programmet, omdirigering till en butik eller alla typer av datakällor som du kan ange).
* Skriv innehållet som ska visas på knappen Avsluta (genom att klicka på knappen Stäng, webbvyn försvinner).
* Resultat

![WebView5][32]

* Användaren ta emot meddelandet och klicka på den.
* Textvyn visas som ett popup-fönster som tillåter användaren att interagera med den.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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

