---
title: "Analysera navigering användarmönster med användaren flödar i Azure Application Insights | Microsoft docs"
description: "Analysera hur användarna navigera mellan sidor och funktioner i ditt webbprogram."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/02/2017
ms.author: cfreeman
ms.openlocfilehash: d17ed3dff08f00a1d6a2108608e42b29f95fbd84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analysera navigering användarmönster med användaren flödar i Application Insights

![Application Insights användaren flödar verktyget](./media/app-insights-usage-flows/flows.png)

Verktyget användaren flödar visualizes hur användarna navigera mellan sidor och funktioner på din plats. Det är bra för att besvara frågor som:
* Hur användarna navigera bort från en sida på webbplatsen?
* Vad användare klicka på en sida på webbplatsen?
* Var finns de platser som användare omsättningsuppdateringar mest från platsen?
* Finns det platser där användare Upprepa samma åtgärd flera gånger?

Verktyget flödar användaren startar från en inledande sidan vyn eller den händelse som du anger. Få den här vyn sida eller anpassade händelsen visar användaren flödar sidvisningar och anpassade händelser som användare skickas omedelbart efteråt under en session två steg efteråt, och så vidare. Rader med olika tjocklek visar hur många gånger varje sökväg har följt av användare. Särskilda ”sessionen avslutats” noderna visar hur många användare skickas ingen sidvisningar eller anpassade händelser efter den föregående noden syntaxmarkering där användare förmodligen kvar din plats.



> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser du använder verktyget trafikflöden för användaren. [Lär dig att konfigurera din app att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md).
> 
> 

## <a name="start-by-choosing-an-initial-page-view-or-custom-event"></a>Starta genom att välja en inledande sidan vy eller anpassade händelsen

![Välj en inledande händelse för användaren flödar](./media/app-insights-usage-flows/flows-initial-event.png)

Kom igång genom att besvara frågor med verktyget användaren flödar väljer du ett första sidan vyn eller anpassade händelsen ska fungera som startpunkt för visualiseringen:
1. Klicka på länken i den ”vad användarna gör efter...”? title eller klicka på knappen Redigera. 
2. Välj en vyn sida eller anpassade händelsen i listrutan ”första händelsen”.
3. Klicka på ”Skapa diagram”.

Kolumnen ”steg 1” för visualiseringen visar vad användare har oftast bara efter händelsen inledande sorterade upp och ned från mest till minst-frekventa. ”Steg 2” och efterföljande kolumner visar vad användarna har därefter skapa en bild av alla sätt användare har navigerat till din plats.

Som standard exempel verktyget användaren flödar slumpmässigt endast under de senaste 24 timmarna sidvisningar och anpassade händelsen från webbplatsen. Du kan öka tidsintervallet och ändra balans mellan prestanda och noggrannhet för slumpmässiga prov i Redigera-menyn.

Klicka på ”X” på noder som du vill dölja om några av de sidvisningar och anpassade händelser är inte relevant för dig. När du har valt de noder som du vill dölja, klicka på ”Skapa diagram” nedan visualiseringen. Klicka på knappen Redigera om du vill se alla noder som du har dolt och titta på avsnittet ”exkluderade händelser”.

Om sidvisningar eller anpassade händelser saknar som du förväntar dig att se på visualiseringen:
* Sök i avsnittet ”exkluderade händelser” i Redigera-menyn.
* Använd ”detaljnivån”-kontrollen i Redigera-menyn för att inkludera mer sällan händelser i visualiseringen.
* Om vyn sida eller anpassade händelsen som du förväntar dig skickas sällan av användare, försök att öka tidsintervall för visualisering i Redigera-menyn.
* Kontrollera att sidan Visa eller anpassade händelsen som du förväntar dig att anges som samlas in av Application Insights SDK i källkoden för din webbplats. [Läs mer om att samla in anpassade händelser.](app-insights-api-custom-events-metrics.md)

Om du vill se fler steg i visualiseringen använda skjutreglaget ”antal steg” i Redigera-menyn.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>När du besöker en sida eller en funktion där användare går och vad de på?

![Använd trafikflöden för användaren för att förstå där användare klickar på](./media/app-insights-usage-flows/flows-one-step.png)

Om din första händelsen är en sidvy, är den första kolumnen (”steg 1) i visualiseringen ett snabbt sätt att förstå vad användare gjorde när du besöker sidan. Försök att öppna webbplatsen i ett fönster bredvid visualisering användare flödar. Jämför förväntningar för hur användarna samverkar med sidan i listan över händelser i kolumnen ”steg 1”. En UI-element på sidan som verkar vara obetydlig för ditt team kan ofta vara bland de mest använda på sidan. Det kan vara en bra utgångspunkt för design förbättringar av webbplatsen.

Om din första händelsen är en anpassad händelse, visas den första kolumnen vad användare gjorde när du utför denna åtgärd. Precis som med sidvyer överväga om observerade beteendet för dina användare matchar teamets mål och förväntningar. Om din valda första händelsen är ”lägga till objekt till kundvagn”, till exempel se ut om ”gå till kassan” och ”slutföra köpet” visas i visualiseringen strax därefter. Om användarbeteende skiljer sig mycket från dina förväntningar, använda visualiseringen för att förstå hur användare får ”fångas” aktuella avsiktligt din plats.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Var finns de platser som användare omsättningsuppdateringar mest från platsen?

Håll utkik efter ”sessionen avslutats” noder som visas hög upp i en kolumn i visualiseringen särskilt tidigt i ett flöde. Det innebär att många användare som förmodligen churned från din plats när du har följt den föregående sökvägen för sidor och interaktioner Användargränssnittet. Ibland omsättningen - när du har slutfört ett inköp på en plats för e-handel, till exempel - men omsättning är vanligtvis ett tecken på problem, dåliga prestanda eller andra problem med din webbplats kan förbättras.

Tänk på att ”sessionen avslutats” noder endast baseras på telemetri som samlats in av Application Insights-resurs. Om Application Insights inte har fått telemetri för vissa användarinteraktioner, användare kan fortfarande har har åtgärdat med webbplatsen sig när verktyget användaren flödar står sessionen avslutades.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Finns det platser där användare Upprepa samma åtgärd flera gånger?

Leta efter en vyn sida eller anpassade händelsen upprepas i efterföljande steg i visualiseringen av många användare. Detta innebär vanligen att användare utför återkommande åtgärder på webbplatsen. Om du hittar upprepning tänka ändra designen för din webbplats eller lägga till nya funktioner för att minska upprepning. Om du hittar användare som utför återkommande åtgärder på varje rad i en tabellelement till exempel lägga till bulk redigera funktioner.

## <a name="common-questions"></a>Vanliga frågor

### <a name="why-do-steps-appear-disconnected"></a>Varför visas stegen frånkopplad?

![Användaren flöden med frånkopplade steg](./media/app-insights-usage-flows/flows-disconnected.png)

Om stegen (kolumner) i en visualisering användare flödar är frånkopplad, innebär det att ingen av sökvägarna som användare mellan stegen vidta var ofta förekommande ska visas. Om du vill visa mer sällan justerar händelser på visualiseringen så att steg som visas ansluten, du skjutreglaget ”detaljnivån” Redigera-menyn.

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Lagrar första händelsen representerar händelsen visas i en session första gången eller när den visas i en session?

Första händelsen i visualiseringen representerar bara första gången en användare skickas sidan vyn eller anpassade händelsen under en session. Om användarna kan skicka den första händelsen flera gånger i en session och sedan kolumnen ”steg 1” bara visar hur användare beter sig när den *första* inledande händelse, inte alla instanser.

## <a name="next-steps"></a>Nästa steg

* [Översikt över användning](app-insights-usage-overview.md)
* [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
* [Kvarhållning](app-insights-usage-retention.md)
* [Lägga till anpassade händelser i din app](app-insights-api-custom-events-metrics.md)