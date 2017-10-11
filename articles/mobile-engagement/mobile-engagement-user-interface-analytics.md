---
title: "Användargränssnittet för Azure Mobile Engagement - Analytics"
description: "Lär dig att analysera historiska data för ditt program med Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ad05676919d6c254d60fd010c3f589f663c4745d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Analysera historiska data om ditt program
Den här artikeln beskriver den **ANALYTICS** för den **Mobile Engagement** portal. Du använder den **Mobile Engagement** portalen för att övervaka och hantera dina mobila appar. Observera att börja använda portalen måste du först skapa en **Azure Mobile Engagement** konto.

Analytics-avsnittet i Användargränssnittet innehåller aktuell information om ditt program som bygger på historiska data som uppdateras var 24: e timme. Informationen visas på olika instrumentpaneler som består av rad/fält/cirkeldiagram, rutnät och kartor. Data kan också hämtas som CSV-filer. De flesta av samma information som finns i realtid i avsnittet övervaka i Användargränssnittet och kan även nås från Analytics-API.

> [!NOTE]
> Många avsnitt i den **Mobile Engagement** portal Användargränssnittet innehåller den **Visa hjälp** knappen. Tryck på knappen för att få mer information om ett avsnitt.

## <a name="standard-and-custom-analytics"></a>Standard- och anpassade Analytics
Azure Mobile Engagement tillhandahåller en uppsättning basic, standard analytiska information om ditt program som kan visas i diagram registreringen så snart du integrera din app med SDK. Azure Mobile Engagement ger också möjlighet att samla in ytterligare anpassade analys information som du vill om slutanvändarnas beteende. Du kan göra detta genom att skapa en taggplan av anpassade ”taggar (app-info)”, som skapas från **inställningar** så att Azure Mobile Engagement kan samla in ytterligare data för dig.

## <a name="analytics"></a>Analys
* Instrumentpanelen: Visar allmän information om nya och består användarna och deras trender.
* Användare: Användare identifieras genom sina enhets-ID: Detta ID är unikt för varje enhet (en ny användare är faktiskt en ny enhet). En användare anses vara ny under ett visst tidsintervall om han eller hon har genomfört sin första session under tidsintervallet. En användare anses som kvarhållen om hon eller han har genomfört minst en session under de senaste sju dagarna. Aktiva användare kan användare som gjort minst en session under en given tidsperiod. Du kan sortera efter, varje månad, varje vecka, varje dag eller varje timme tidsperioder. Alla diagram likna men kan du filtrera efter olika funktioner, till exempel versionen av programmet, och sedan på Sortera efter en viss tidsperiod. Standardinformation som samlats in genom att integrera SDK innehåller följande: aktiva användare, ny användare, sessioner, längden på varje session, teknisk information om land, lokala variabler, plats, språk operatör, enheter, inbyggd programvara, nätverk (Wi-Fi) , versioner av appen och SDK, som används av kunder. Den här informationen kan visas i realtid från avsnittet övervakaren.

> [!NOTE]
> Hur lång tid som baseras på användarnas Enhetsinställningar datum så att en användare vars telefonen har datum som felaktigt kan visas i fel tidsperioden.

* Kvarhållning: En användare anses vara kvarhållen under ett visst tidsintervall om han eller hon har genomfört sin första session under tidsintervallet. Du kan ändra de tidsintervaller under vilka kvarhållna användare (och nya användare) ska räknas till timmar, dagar, veckor eller månader. Användaren kvarhållning analytics bygger på kohorter. En kommittén motsvarar uppsättningen av alla nya användare som har identifierats för en viss period (d.v.s. uppsättning användare som utför sin första session under denna tid). Vi använder kohorter 1 dag, 2 dagar, 4 dagar, 7 dagar eller 1 månad. En kommittén får varje 1-dag 2-4 dagar, 7 dagars eller 1-månads, Azure Mobile Engagement beräknar uppsättningen av alla användare som tillhör kommittén och är fortfarande aktiva (d.v.s. uppsättning användare som har genomfört minst en session under perioden). Den här uppsättningen med användare kallas en kommittén version. (Azure Mobile Engagement kan du visa hur många användare fortfarande använder din app, men endast plattform specifika arkivet ser du hur många användare avinstalleras din app – till exempel GooglePlay iTunes Windows Store, etc.).
* Sessioner: En användning av en användare. Sessioner genereras från sekvensen av aktiviteter som utförs av användare (en aktivitet är ofta kopplade till användningen av en skärm för programmet, men detta kan variera beroende på hur SDK: N har integrerats i programmet). En användare kan bara utföra en aktivitet i taget: sessionen startar när användaren startar den första aktiviteten och slutar när användarens sista aktivitet är klar. Om en användare är fler än några få sekunder utan att utföra alla aktiviteter, är hans sekvensen av aktiviteter uppdelad i två olika sessioner.
* Aktiviteter: Namnen på varje skärm i ditt program och hur lång tid användare spendera på varje skärm. Aktiviteter är ett anpassat analytiska alternativ som motsvarar ”app-info”-taggar som du har ställt in för din egen app:
* Sökväg till användare: Visar hur dina användare navigerar genom ditt programs aktiviteter (skärmar). Du kan justera detaljnivån genom att flytta skjutreglaget. Blå noder representerar ditt programs aktiviteter. Deras storlek är proportionell mot tid som användarna ägnat åt den. Vita noder representerar start och stopp för sessioner. Röda noder representerar krascher. Länkar representerar övergångar mellan ditt programs aktiviteter (eller mellan aktiviteter och krascher). Klicka på en nod eller en länk för att visa en knappbeskrivning med mer information om dina data: tid som ägnats åt en viss skärm, antalet övergångar och procentandelen övergångar från källaktiviteten till målaktiviteten. (En---60%---> B innebär att användare på aktivitet A går till aktivitet B 60% av tiden.) Du kan organisera om diagrammet som du vill förtydliga något; dess position sparas varje gång du gör en ändring. Du kan visa eller dölja krasherna som du vill.
* Händelser: Vissa åtgärder som utförs av en användare i programmet. Distribution av händelser visas som antalet händelser per användare per session. En händelse representerar en omedelbar åtgärd, till exempel klickar på en knapp eller mottagning av ett meddelande. (Innebörden av olika händelser beror på hur SDK: N har integrerats i programmet.) En händelse kan inträffa under en session eller ett jobb eller kan vara fristående.
* Jobb: Liknande händelser förutom de fokusera på längden för åtgärden. Jobb kan exempelvis ange du teknisk information om hur lång tid det tar innehåll ska läsa in eller ett anrop till webbtjänsten. Det kan också visa hur lång tid det tar för en användare att fylla i ett formulär, skapa ett konto eller göra ett inköp. Ett jobb representerar en uppgifts varaktighet, till exempel varaktighet för en nedladdnings varaktighet eller tid en banderoll visas på skärmen. (Innebörden av ett jobb beror på hur SDK: N har integrerats i programmet.) Jobb är ofta kopplade till bakgrundsuppgifter som genomförs utanför omfånget för en session (dvs utan att användaren är inblandad).
* Technicals: Teknisk information om enheterna som användare av en app som du kan följa, till exempel språk, operatör, nätverk, enhet, inbyggd programvara, och storleken på användarnas enheter och versionen av appen och SDK-version som används i din app.
* Fel: Information om tekniska fel i programmet som inte orsakar programmet kraschar. Ett fel representerar ett snabbmeddelanden problem, till exempel nätverksfel eller dålig hantering. (Innebörden av olika händelser beror på hur SDK: N har integrerats i programmet.) Ett fel kan inträffa under en session eller ett jobb eller kan vara fristående.
* Krascher: Information om fel som gör programmet kraschar. En krasch är ett oväntat tillstånd där programmet slutar att fungera som förväntat och måste stoppas. En krasch beror oftast på grund av ett fel i programmet.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Åtkomst till kvarhållning-översikt
![Analytics3][12]

Översikt för kvarhållning är uppdelad i mitten i flera kort var och en visar en översikt för en viss period. 2 dagar loggperioden visas i exemplet. Andra kort visar 4 dagar och 7 dagars kvarhållningsperioder.

## <a name="understanding-the-retention-overview-cards"></a>Förstå kvarhållning översikt-kort
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Varje kort består av 3 delar:
1. 1: kommittén och perioden
2. 2-4: kvarhållning för den aktuella perioden
3. 5: miniatyrdiagram historiken

### <a name="here-is-detailed-information-about-each-element"></a>Här är detaljerad information om varje element:
1. Kommittén och period: den här rubriken ger typ av kommittén. Här innebär ”2-dagarsperiod” att vi ska titta på hur användare över 2 dagar användare kommit över en tidsperiod 2 dagar, och om de ansluter i följande block 2 dagar. Exemplet ovan anser aktiviteten användare mellan 21 och 22nd november.
2. Detta ger ett kvarhållning hastigheten med vilken 21 och 22 i November för de användare som inkommer på 19 och 20 i November. Vi hade här 1 aktiva användare mellan 21 och 22nd, över 3 som var nya användare mellan 19 och 20.
3. Denna visual indikator ger samma information som ovan grafiskt. (Tredje cirkelns är från 33% numret.) Färgen som innehåller ytterligare information: grönt anger antalet växer från den föregående beräkningen. Gult innebär stabilt och röda sätt minskar.
4. Detta anger de värden som används för beräkning.
5. Det här är ett miniatyrdiagram av tidigare kvarhållning värden. Det kan du se värdena som tidigare har en omfattande vy av hur det utvecklades.

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
