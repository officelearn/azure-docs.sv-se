---
title: Analysera användarmönster för navigering med Användarflöden i Azure Application Insights | Microsoft docs
description: Analysera hur användarna navigerar mellan sidor och funktioner på din webbapp.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 5c057e4592939dea549c4e50c4ef1bdc5d6367fd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096266"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analysera användarmönster för navigering med Användarflöden i Application Insights

![Verktyget för Application Insights Användarflöden](./media/app-insights-usage-flows/00001-flows.png)

Verktyget Användarflöden visualiserar hur användarna navigerar mellan sidor och funktioner för din webbplats. Det är bra för att besvara frågor som:

* Hur användarna navigerar bort från en sida på webbplatsen?
* Vad användarna klicka på en sida på webbplatsen?
* Var finns de platser som användare omsättning mest från din plats?
* Finns det några platser där användare Upprepa samma åtgärd flera gånger?

Verktyget Användarflöden startar från en inledande Sidvisning, anpassad händelse eller undantag som du anger. Med den här inledande händelse kan visar Användarflöden de händelser som hände före och därefter under användarsessioner. Rader med olika tjocklek visar hur många gånger varje sökväg följdes av användare. Särskilda **Session igång** noderna visar där efterföljande noderna började en session. **Sessionen avslutat** noderna visar hur många användare skickas inga sidvisningar eller anpassade händelser efter den föregående noden markering där användare antagligen kvar din webbplats.

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser och använder verktyget Användarflöden. [Lär dig hur du ställer in din app för att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Börja med att välja en inledande händelse

![Välj en inledande händelse för Användarflöden](./media/app-insights-usage-flows/00002-flows-initial-event.png)

Välj ett inledande Sidvisning, anpassad händelse eller ett undantag som fungerar som startpunkt för visualiseringen för att börja svara på frågor med verktyget Användarflöden:

1. Klicka på länken i den **vad gör användarna efter...?**  rubrik, eller klicka på den **redigera** knappen.
2. Välj en Sidvisning, anpassad händelse eller undantag från den **första händelsen** listrutan.
3. Klicka på **skapa graph**.

Kolumnen ”steg 1” i visualiseringen visar vad användare gjorde oftast bara efter den första händelsen, sorterade uppifrån och ned från mest till minst frekvent. ”Steg 2” och efterföljande kolumner som visar vad användarna har därefter skapar du en överblick över alla de sätt har navigerat till en egen.

Som standard samplar verktyget Användarflöden slumpmässigt endast de senaste 24 timmarna sidvisningar och anpassade händelsen från din plats. Du kan öka tidsintervallet och ändra balans mellan prestanda och Precision för stickprov Redigera-menyn.

Om några av de sidvisningar och anpassade händelser undantag är inte relevant för dig, klickar du på den **X** på noder som du vill dölja. När du har valt de noder som du vill dölja, klickar du på den **skapa graph** knappen under visualiseringen. Klicka för att visa alla noder som du har dolt den **redigera** knappen och titta på den **exkluderade händelser** avsnittet.

Om sidvisningar eller anpassade händelser saknar som du förväntar dig att se i visualiseringen:

* Kontrollera den **exkluderade händelser** i avsnittet den **redigera** menyn.
* Använd knapparna plus på **andra** noder till att inkludera mer sällan händelser i visualiseringen.
* Om den Sidvisning eller anpassad händelse som du förväntar dig skickas sällan av användare, kan du prova att öka tidsintervallet för visualisering i den **redigera** menyn.
* Kontrollera att sidan Visa, anpassad händelse eller undantag du förväntar dig har ställts in som ska samlas in av Application Insights SDK i källkoden för din webbplats. [Läs mer om insamling av anpassade händelser.](app-insights-api-custom-events-metrics.md)

Om du vill se fler steg i visualiseringen använder den **föregående steg** och **nästa steg** listrutor ovanför visualiseringen.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>När du besöker en sida eller en funktion där användarna gå och vad de klickar på?

![Använd Användarflöden för att förstå där användarna klickar på](./media/app-insights-usage-flows/00003-flows-one-step.png)

Om din första händelsen är en Sidvisning, är den första kolumnen (”steg 1”) i visualiseringen ett snabbt sätt att förstå vad användare gjorde omedelbart efter att besöka sidan. Försök att öppna webbplatsen i ett fönster bredvid Användarflöden visualiseringen. Jämför dina förväntningar för hur användarna samverkar med sidan i listan över händelser i kolumnen ”steg 1”. Ofta kan ett UI-element på sidan verkar obetydlig till ditt team bli bland de mest använda på sidan. Det kan vara en bra utgångspunkt för designförbättringar till din webbplats.

Om dina inledande händelse är en anpassad händelse, visar den första kolumnen vad användare gjorde när du utför åtgärden. Precis som med sidvisningar, Överväg om observerade beteendet för dina användare matchar teamets mål och förväntningar. Om din valda inledande händelse är ”har lagts till objektet till kundvagnen”, till exempel se ut om ”gå till kassan” och ”slutfört köp” visas i visualiseringen strax därefter. Om användaren skiljer sig från dina förväntningar, använda visualiseringen för att förstå hur användare får ”inmålad” aktuella avsiktligt din webbplats.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Var finns de platser som användare omsättning mest från din plats?

Håll utkik efter **sessionen avslutades** noder som visas högt upp i en kolumn i visualiseringen, särskilt tidigt i ett flöde. Det innebär att många användare som förmodligen churned från din plats när du har följt föregående sökväg för sidor och interaktioner med Användargränssnittet. Ibland omsättningen - när du har slutfört ett inköp på en plats för e-handel, till exempel - men omsättning är vanligtvis ett tecken på problem, dåliga prestanda eller andra problem med din webbplats kan förbättras.

Kom ihåg som **sessionen avslutades** noder baseras bara på telemetri som samlas in av den här Application Insights-resursen. Om Application Insights inte får telemetri för vissa användarinteraktioner, kan användare fortfarande har interagerat med din webbplats på dessa sätt när verktyget Användarflöden säger sessionen avslutades.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Finns det några platser där användare Upprepa samma åtgärd flera gånger?

Leta efter en Sidvisning eller anpassad händelse som upprepas av många användare i efterföljande steg i visualiseringen. Detta innebär vanligen att användare utför repetitiva åtgärder på webbplatsen. Om du hittar upprepning tänka på ändra utformningen av din webbplats eller lägga till nya funktioner för att minska upprepning. Till exempel att lägga till redigera massfunktion om du hittar användare som utför återkommande åtgärder på varje rad i en tabellelementet.

## <a name="common-questions"></a>Vanliga frågor

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Varken inledande händelse representerar händelsen visas i en session första gången eller när som helst som det visas i en session?

Den första händelsen i visualiseringen visar endast första gången en användare skickade den Sidvisning eller anpassad händelse under en session. Om användarna kan skicka den första händelsen flera gånger i en session och sedan ”steg 1”-kolumnen visar bara hur användarna ska fungera efter den *första* inledande händelse, inte alla instanser.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Vissa av noderna i min visualisering är för hög nivå. Exempelvis kan en nod som bara står ”knappen Clicked”. Hur kan jag dela upp det i mer detaljerad noder?

Använd den **dela efter** alternativ i den **redigera** menyn:

1. Den händelse som du vill bryta ned den **händelse** menyn.
2. Välj en dimension i den **Dimension** menyn. Om du har en händelse som kallas ”på knappen” Prova till exempel en anpassad egenskap med namnet ”knappen Name”.

## <a name="next-steps"></a>Nästa steg

* [Användningsöversikten](app-insights-usage-overview.md)
* [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
* [Kvarhållning](app-insights-usage-retention.md)
* [Att lägga till anpassade händelser i din app](app-insights-api-custom-events-metrics.md)