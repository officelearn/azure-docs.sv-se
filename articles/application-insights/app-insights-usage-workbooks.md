---
title: Undersöka och dela användningsdata med interaktiva arbetsböcker i Azure Application Insights | Microsoft docs
description: Demografiska analys av användare på din webbapp.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/12/2017
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 016a26acc153fba1c38d926fd5389d02755c2ff5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648969"
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Undersöka och dela användningsdata med interaktiva arbetsböcker i Application Insights

Arbetsböcker kombinera [Azure Application Insights](app-insights-overview.md) datavisualiseringar, [analysfrågor](app-insights-analytics.md), och text i interaktiva dokument. Arbetsböcker kan redigeras av andra användare med åtkomst till samma Azure-resursen. Det innebär att frågorna och kontroller som används för att skapa en arbetsbok är tillgängliga för andra personer som läser arbetsboken, vilket gör dem lätta att utforska, utöka och söka efter fel.

Arbetsböcker är användbart för scenarier som:

* Utforska användningen av din app när du inte vet mätvärden i förväg: antal användare, lagringshastighet, konverteringstakten osv. Till skillnad från andra analytics-verktyg för användning i Application Insights kan arbetsböcker du kombinera flera typer av visualiseringar och analyser, vilket gör dem bra för den här typen av egen utforskning.
* Förklarar hur du utför en nyligen utgiven funktion till ditt team, av användaren som visar antal för viktiga interaktioner och andra mått.
* Dela resultatet av en A / B experimentera i din app med andra medlemmar i ditt team. Du kan förklara mål för experiment med text och sedan visa varje användningsstatistik och Analytics-fråga som används för att utvärdera experiment, tillsammans med tydliga pratbubblor för om varje mått var ovan - eller nedan-mål.
* Rapporterar effekten av ett avbrott på användningen av din app, kombinera data, text förklaring och en beskrivning av nästa steg för att förhindra avbrott i framtiden.

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser och använda arbetsböcker. [Lär dig hur du ställer in din app för att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Redigera, ändra sorteringen, kloning och ta bort arbetsbok avsnitt

En arbetsbok är en göras av avsnitt: oberoende redigerbara användning visualiseringar, diagram, tabeller, text eller Analytics frågeresultat.

Om du vill redigera innehållet i ett avsnitt i arbetsboken, klickar du på den **redigera** knappen nedanför och till höger om avsnittet arbetsboken.

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/app-insights-usage-workbooks/editing-controls.png)

1. När du är klar redigerar ett avsnitt, klickar du på **klar redigera** i det nedre vänstra hörnet i avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt, klickar du på den **klona det här avsnittet** ikon. Det är en bra sätt att iterera på en fråga utan att förlora tidigare iterationer för att skapa dubbla avsnitt.

3. Om du vill flytta upp ett avsnitt i en arbetsbok, klickar du på den **Flytta upp** eller **Flytta ned** ikon.

4. Om du vill ta bort ett avsnitt permanent, klickar du på den **ta bort** ikon.

## <a name="adding-usage-data-visualization-sections"></a>Att lägga till användning data visualisering avsnitt

Arbetsböcker erbjuder fyra typer av inbyggda användning analytics visualiseringar. Var och en får du svar på vanliga frågor om användningen av din app. Om du vill lägga till tabeller och diagram än följande fyra avsnitt, lägger du till Analytics-fråga avsnitt (se nedan).

Om du vill lägga till en användare, sessioner, händelser eller kvarhållning avsnittet i din arbetsbok, Använd den **Lägg till användare** eller andra motsvarande knapp längst ned i arbetsboken eller längst ned i ett avsnitt.

![I avsnittet för användare i arbetsböcker](./media/app-insights-usage-workbooks/users-section.png)

**Användare** avsnitt besvara ”hur många användare visas en sida eller använt en funktion av min webbplats”?

**Sessioner** avsnitt besvara ”hur många sessioner som användare tillbringar ser vissa sidan eller använder en funktion i Min webbplats”?

**Händelser** avsnitt besvara ”hur många gånger användarna visa vissa sidan eller använda vissa funktioner i Min webbplats”?

De olika typerna tre avsnitt erbjuder samma uppsättning kontroller och visualiseringar:

* [Läs mer om hur du redigerar användare, sessioner och händelser avsnitt](app-insights-usage-segmentation.md)
* Växla huvuddiagram, histogram rutnät, automatisk insikter och exemplet användare visualiseringar med den **visa diagrammet**, **Visa rutnät**, **visa insikter**, och **Exempel på dessa användare** kryssrutorna högst upp i varje avsnitt.

![Kvarhållning avsnittet i arbetsböcker](./media/app-insights-usage-workbooks/retention-section.png)

**Kvarhållning** avsnitt besvara ”för personer som visade vissa sidan eller använt en funktion på dagen eller veckan, hur många Kom tillbaka en efterföljande dag eller en vecka”?

* [Läs mer om hur du redigerar kvarhållning avsnitt](app-insights-usage-retention.md)
* Växla den valfria övergripande kvarhållning diagram med hjälp av den **visa övergripande kvarhållningsdiagram** kryssrutan överst i avsnittet.

## <a name="adding-application-insights-analytics-sections"></a>Att lägga till Application Insights Analytics-avsnitt

![Analytics-avsnittet i arbetsböcker](./media/app-insights-usage-workbooks/analytics-section.png)

Om du vill lägga till ett avsnitt för Application Insights Analytics-fråga till din arbetsbok, använda den **Lägg till analysfråga** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

Analytics-fråga avsnitt kan du lägga till valfri frågor över dina Application Insights-data i arbetsböcker. Den här flexibiliteten innebär Analytics-fråga avsnitt ska vara får för att besvara några frågor om din än de fyra som anges ovan för användare, sessioner, händelser och kvarhållning, t.ex.:

* Hur många undantag webbplatsen throw under samma tidsperiod som en nedgång i användningen?
* Vad var distributionen av sidans belastning för användare som visar några sidan?
* Hur många användare visas en uppsättning sidor på webbplatsen, men inte en annan uppsättning sidor? Detta kan vara användbar för att förstå om du har kluster av användare som använder olika delmängder av webbplatsens funktioner (Använd den `join` operator med den `kind=leftanti` modifieraren i Log Analytics-frågespråket).

Använd den [Log Analytics frågespråksreferens](https://docs.loganalytics.io/) mer information om hur du skriver frågor.

## <a name="adding-text-and-markdown-sections"></a>Att lägga till text- och Markdown-avsnitt

Att lägga till rubriker, förklaringar och kommentarer till dina arbetsböcker omvandla hjälper dig att en uppsättning tabeller och diagram till en berättelse. Delar av texten i stödet för arbetsböcker på [markdownsyntax](https://daringfireball.net/projects/markdown/) för textformatering som rubriker, fet stil, kursiv stil och punktlistor.

Lägg till ett textavsnitt i din arbetsbok, använda den **Lägg till text** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med ditt team

Arbetsböcker sparas i en Application Insights-resurs i den **Mina rapporter** avsnitt som är privata för dig eller i den **delade rapporter** avsnitt som är tillgängliga för alla med åtkomst till den Application Insights-resurs. Om du vill visa alla arbetsböcker i resursen, klickar du på den **öppna** knappen i åtgärdsfältet.

Dela en arbetsbok som för närvarande finns i **Mina rapporter**:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill dela
3. Klicka på **flytta till delade rapporter**.

Om du vill dela en arbetsbok med en länk eller via e-post, klickar du på **dela** i åtgärdsfältet. Tänk på att mottagarna av länken behöver åtkomst till den här resursen i Azure portal för att visa arbetsboken. När du vill redigera mottagarna behöver minst deltagarbehörighet för resursen.

Att fästa en länk till en arbetsbok till en Azure-instrumentpanelen:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill fästa
3. Klicka på **fäst på instrumentpanelen**.

## <a name="next-steps"></a>Nästa steg

## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    - [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)
    
