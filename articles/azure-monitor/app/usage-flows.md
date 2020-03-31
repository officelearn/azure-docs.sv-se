---
title: Analysera användarnavigeringsmönster med användarflöden i Azure Application Insights | Microsoft-dokument
description: Analysera hur användarna navigerar mellan sidorna och funktionerna i din webbapp.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a4a81a3e4c5759e444836162319abb97d83a4c74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671077"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analysera användarnavigeringsmönster med användarflöden i application insights

![Verktyget Användarflöden för programinsikter](./media/usage-flows/00001-flows.png)

Med verktyget Användarflöden visualiserar du hur användarna navigerar mellan sidorna och funktionerna på webbplatsen. Det är bra för att svara på frågor som:

* Hur navigerar användarna bort från en sida på din webbplats?
* Vad klickar användarna på en sida på din webbplats?
* Var finns de platser som användarna spottar mest från din webbplats?
* Finns det platser där användarna upprepar samma åtgärd om och om igen?

Verktyget Användarflöden startar från en första sidvy, en anpassad händelse eller ett undantag som du anger. Med tanke på den här första händelsen visar användarflöden de händelser som inträffade före och efteråt under användarsessioner. Linjer med varierande tjocklek visar hur många gånger varje sökväg följdes av användare. Särskilda **sessionsstartade** noder visar var de efterföljande noderna började en session. **SessionSlutna** noder visar hur många användare som inte har skickat några sidvisningar eller anpassade händelser efter den föregående noden, vilket markerar var användarna förmodligen lämnade webbplatsen.

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser för att använda verktyget Användarflöden. [Lär dig hur du konfigurerar appen så att den samlar in sidvisningar automatiskt med JavaScript-SDK för programinsikter](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Börja med att välja en första händelse

![Välj en första händelse för användarflöden](./media/usage-flows/00002-flows-initial-event.png)

Om du vill börja svara på frågor med verktyget Användarflöden väljer du en första sidvy, en anpassad händelse eller ett undantag som ska användas som utgångspunkt för visualiseringen:

1. Klicka på länken i rubriken **Vad gör användarna efter...?** eller klicka på knappen **Redigera.**
2. Välj en sidvy, en anpassad händelse eller ett undantag från listrutan **Inledande händelse.**
3. Klicka på **Skapa diagram**.

Kolumnen "Steg 1" i visualiseringen visar vad användarna gjorde oftast strax efter den första händelsen, som beställdes uppifrån och ned från de flesta till minst frekventa. Kolumnerna "Steg 2" och efterföljande visar vad användarna gjorde därefter, vilket skapar en bild av alla sätt som användarna har navigerat på din webbplats.

Som standard tar verktyget Användarflöden slumpmässigt bara de senaste 24 timmarna av sidvisningar och en anpassad händelse från webbplatsen. Du kan öka tidsintervallet och ändra balansen mellan prestanda och noggrannhet för slumpmässigt urval på Redigera-menyn.

Om några av sidvisningarna, anpassade händelser och undantag inte är relevanta för dig klickar du på **Krysset** på de noder som du vill dölja. När du har markerat de noder som du vill dölja klickar du på knappen **Skapa diagram** under visualiseringen. Om du vill visa alla noder som du har dolt klickar du på knappen **Redigera** och tittar sedan på avsnittet **Uteslutna händelser.**

Om sidvisningar eller anpassade händelser saknas som du förväntar dig att se i visualiseringen:

* Kontrollera avsnittet **Uteslutna händelser** på **Redigera-menyn.**
* Använd plusknapparna på **andra** noder för att inkludera mindre frekventa händelser i visualiseringen.
* Om sidvyn eller den anpassade händelse som du förväntar dig skickas sällan av användare kan **Edit** du prova att öka tidsintervallet för visualiseringen på Redigera-menyn.
* Kontrollera att sidvyn, den anpassade händelsen eller undantaget som du förväntar dig har konfigurerats för att samlas in av SDK för programinsikter i webbplatsens källkod. [Läs mer om hur du samlar in anpassade händelser.](../../azure-monitor/app/api-custom-events-metrics.md)

Om du vill se fler steg i visualiseringen använder du listrutan **Föregående steg** och **Nästa steg** ovanför visualiseringen.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Efter att ha besökt en sida eller funktion, vart tar användarna vägen och vad klickar de på?

![Använda användarflöden för att förstå var användarna klickar](./media/usage-flows/00003-flows-one-step.png)

Om den första händelsen är en sidvy är den första kolumnen (steg 1) i visualiseringen ett snabbt sätt att förstå vad användarna gjorde direkt efter att ha besökt sidan. Prova att öppna webbplatsen i ett fönster bredvid visualiseringen Användarflöden. Jämför dina förväntningar på hur användarna interagerar med sidan med listan över händelser i kolumnen Steg 1. Ofta kan ett gränssnittselement på sidan som verkar obetydligt för ditt team vara bland de mest använda på sidan. Det kan vara en bra utgångspunkt för designförbättringar på din webbplats.

Om den första händelsen är en anpassad händelse visar den första kolumnen vad användarna gjorde precis efter att de utfört åtgärden. Precis som med sidvisningar bör du överväga om användarnas observerade beteende matchar lagets mål och förväntningar. Om den valda första händelsen är "Lagt till objekt i kundvagnen", till exempel, titta för att se om "Gå till kassan" och "Slutfört köp" visas i visualiseringen kort därefter. Om användarbeteende skiljer sig från dina förväntningar använder du visualiseringen för att förstå hur användarna blir "fångade" av webbplatsens aktuella design.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Var finns de platser som användarna spottar mest från din webbplats?

Titta efter **sessionsslutna** noder som visas högt upp i en kolumn i visualiseringen, särskilt tidigt i ett flöde. Det innebär att många användare förmodligen har spottats från webbplatsen efter att ha följt föregående sökväg till sidor och gränssnittsinteraktioner. Ibland churn förväntas - efter att ha avslutat ett köp på en e-handel webbplats, till exempel - men oftast churn är ett tecken på designproblem, dåliga prestanda, eller andra problem med din webbplats som kan förbättras.

Tänk på att **sessionsslutda** noder endast baseras på telemetri som samlas in av den här application insights-resursen. Om Application Insights inte tar emot telemetri för vissa användarinteraktioner kan användarna fortfarande ha interagerat med din webbplats på de sätten efter att verktyget Användarflöden säger att sessionen avslutades.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Finns det platser där användarna upprepar samma åtgärd om och om igen?

Leta efter en sidvy eller en anpassad händelse som upprepas av många användare i efterföljande steg i visualiseringen. Det innebär vanligtvis att användarna utför repetitiva åtgärder på webbplatsen. Om du hittar upprepning, fundera på att ändra utformningen av din webbplats eller lägga till nya funktioner för att minska upprepning. Till exempel lägga till massredigeringsfunktioner om du upptäcker att användare utför repetitiva åtgärder på varje rad i ett tabellelement.

## <a name="common-questions"></a>Vanliga frågor

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Representerar den första händelsen första gången händelsen visas i en session, eller när den visas i en session?

Den första händelsen i visualiseringen representerar bara första gången en användare skickade sidvyn eller den anpassade händelsen under en session. Om användare kan skicka den första händelsen flera gånger i en session visar kolumnen "Steg 1" bara hur användare beter sig efter den *första* instansen av den första händelsen, inte alla instanser.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Några av noderna i min visualisering är för höga. Till exempel en nod som bara säger "Knapp klickade". Hur kan jag dela upp det i mer detaljerade noder?

Använd alternativen **Dela efter** på **Redigera-menyn:**

1. Välj den händelse som du vill dela upp på **Evenemangsmenyn.**
2. Välj en dimension på **Dimension-menyn.** Om du till exempel har en händelse som heter "Knappsklickad" provar du en anpassad egenskap som heter "Knappnamn".

## <a name="next-steps"></a>Nästa steg

* [Översikt över användning](usage-overview.md)
* [Användare, sessioner och händelser](usage-segmentation.md)
* [Kvarhållning](usage-retention.md)
* [Lägga till anpassade händelser i appen](../../azure-monitor/app/api-custom-events-metrics.md)
