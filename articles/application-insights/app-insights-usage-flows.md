---
title: Analysera navigering användarmönster med användaren flödar i Azure Application Insights | Microsoft docs
description: Analysera hur användarna navigera mellan sidor och funktioner i ditt webbprogram.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 5913039d3ab288c2fc8366073b340538989512ee
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analysera navigering användarmönster med användaren flödar i Application Insights

![Application Insights användaren flödar verktyget](./media/app-insights-usage-flows/00001-flows.png)

Verktyget användaren flödar visualizes hur användarna navigera mellan sidor och funktioner på din plats. Det är bra för att besvara frågor som:

* Hur användarna navigera bort från en sida på webbplatsen?
* Vad användare klicka på en sida på webbplatsen?
* Var finns de platser som användare omsättningsuppdateringar mest från platsen?
* Finns det platser där användare Upprepa samma åtgärd flera gånger?

Verktyget flödar användaren startar från en vyn första sida, anpassade händelsen eller undantag som du anger. Första händelsen får visar användaren flödar händelser som inträffade före och därefter under användarsessioner. Rader med olika tjocklek visar hur många gånger varje sökväg har följt av användare. Särskilda **Session igång** noderna visar där efterföljande noder började en session. **Sessionen avslutat** noderna visar hur många användare skickas ingen sidvisningar eller anpassade händelser efter den föregående noden syntaxmarkering där användare förmodligen kvar din plats.

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser du använder verktyget trafikflöden för användaren. [Lär dig att konfigurera din app att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Starta genom att välja en inledande händelse

![Välj en inledande händelse för användaren flödar](./media/app-insights-usage-flows/00002-flows-initial-event.png)

Välj en första sidan Visa, anpassade händelsen eller undantag ska fungera som startpunkt för visualiseringen påbörja besvara frågor med verktyget användaren flödar:

1. Klicka på länken i den **vad användarna gör efter...?**  title, eller klicka på den **redigera** knappen.
2. Välj vyn sida, anpassade händelsen och undantag från den **första händelsen** listrutan.
3. Klicka på **skapa diagram**.

Kolumnen ”steg 1” för visualiseringen visar vad användare har oftast bara efter händelsen inledande sorterade uppifrån och ned från mest till minst ofta. ”Steg 2” och efterföljande kolumner visar vad användarna har därefter skapa en bild av alla sätt användare har navigerat till din plats.

Som standard exempel verktyget användaren flödar slumpmässigt endast under de senaste 24 timmarna sidvisningar och anpassade händelsen från webbplatsen. Du kan öka tidsintervallet och ändra balans mellan prestanda och noggrannhet för slumpmässiga prov i Redigera-menyn.

Om några av de sidvisningar, anpassade händelser och undantag inte är relevant för dig, klickar du på den **X** på noder som du vill dölja. När du har valt de noder som du vill dölja, klicka på den **skapa diagram** nedan visualiseringen. Klicka för att se alla noder som du har dolt den **redigera** knappen titta sedan på den **exkluderade händelser** avsnitt.

Om sidvisningar eller anpassade händelser saknar som du förväntar dig att se på visualiseringen:

* Kontrollera den **exkluderade händelser** under den **redigera** menyn.
* Använd knapparna plus på **andra** noder för att inkludera mer sällan händelser i visualiseringen.
* Om vyn sida eller anpassade händelsen som du förväntar dig skickas sällan av användare, försök att öka tidsintervall för visualisering i den **redigera** menyn.
* Kontrollera att sidan Visa, anpassade händelsen eller undantag du förväntar dig att ställa in som ska samlas in av Application Insights SDK i källkoden för din webbplats. [Läs mer om att samla in anpassade händelser.](app-insights-api-custom-events-metrics.md)

Om du vill se fler steg i visualiseringen använder den **föregående steg** och **nästa steg** nedrullningsbara listorna över visualiseringen.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>När du besöker en sida eller en funktion där användare går och vad de på?

![Använd trafikflöden för användaren för att förstå där användare klickar på](./media/app-insights-usage-flows/00003-flows-one-step.png)

Om din första händelsen är en sidvy, är den första kolumnen (”steg 1) i visualiseringen ett snabbt sätt att förstå vad användare gjorde när du besöker sidan. Försök att öppna webbplatsen i ett fönster bredvid visualisering användare flödar. Jämför förväntningar för hur användarna samverkar med sidan i listan över händelser i kolumnen ”steg 1”. En UI-element på sidan som verkar vara obetydlig för ditt team kan ofta vara bland de mest använda på sidan. Det kan vara en bra utgångspunkt för design förbättringar av webbplatsen.

Om din första händelsen är en anpassad händelse, visas den första kolumnen vad användare gjorde när du utför denna åtgärd. Precis som med sidvyer överväga om observerade beteendet för dina användare matchar teamets mål och förväntningar. Om din valda första händelsen är ”lägga till objekt till kundvagn”, till exempel se ut om ”gå till kassan” och ”slutföra köpet” visas i visualiseringen strax därefter. Om användaren skiljer sig från dina förväntningar, använda visualiseringen för att förstå hur användare får ”fångas” aktuella avsiktligt din plats.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Var finns de platser som användare omsättningsuppdateringar mest från platsen?

Bevaka **sessionen avslutats** noder som visas hög upp i en kolumn i visualiseringen särskilt tidigt i ett flöde. Det innebär att många användare som förmodligen churned från din plats när du har följt den föregående sökvägen för sidor och interaktioner Användargränssnittet. Ibland omsättningen - när du har slutfört ett inköp på en plats för e-handel, till exempel - men omsättning är vanligtvis ett tecken på problem, dåliga prestanda eller andra problem med din webbplats kan förbättras.

Kom ihåg som **sessionen avslutats** noder endast baseras på telemetri som samlats in av Application Insights-resurs. Om Application Insights inte har fått telemetri för vissa användarinteraktioner, användare kan fortfarande har har åtgärdat med webbplatsen sig när verktyget användaren flödar står sessionen avslutades.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Finns det platser där användare Upprepa samma åtgärd flera gånger?

Leta efter en vyn sida eller anpassade händelsen upprepas i efterföljande steg i visualiseringen av många användare. Detta innebär vanligen att användare utför återkommande åtgärder på webbplatsen. Om du hittar upprepning tänka ändra designen för din webbplats eller lägga till nya funktioner för att minska upprepning. Om du hittar användare som utför återkommande åtgärder på varje rad i en tabellelement till exempel lägga till bulk redigera funktioner.

## <a name="common-questions"></a>Vanliga frågor

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Lagrar första händelsen representerar händelsen visas i en session första gången eller när den visas i en session?

Första händelsen i visualiseringen representerar bara första gången en användare skickas sidan vyn eller anpassade händelsen under en session. Om användarna kan skicka den första händelsen flera gånger i en session och sedan kolumnen ”steg 1” bara visar hur användare beter sig när den *första* inledande händelse, inte alla instanser.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Vissa noder i min visualiseringen är för hög. Till exempel en nod som bara säger ”knappen Clicked”. Hur kan jag dela upp det i mer detaljerad noder?

Använd den **delning efter** alternativ i den **redigera** menyn:

1. Välj den händelse som du vill dela upp den **händelse** menyn.
2. Välj en dimension i den **Dimension** menyn. Exempelvis om du har en händelse som kallas ”Klicka på knappen” prova en anpassad egenskap som heter ”knappnamn”.

## <a name="next-steps"></a>Nästa steg

* [Översikt över användning](app-insights-usage-overview.md)
* [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
* [Kvarhållning](app-insights-usage-retention.md)
* [Lägga till anpassade händelser i din app](app-insights-api-custom-events-metrics.md)