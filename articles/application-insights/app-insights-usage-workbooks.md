---
title: Undersöka och dela användningsdata med interaktiva arbetsböcker i Azure Application Insights | Microsoft docs
description: Demografisk analys av användare av ditt webbprogram.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin; daviste
ms.openlocfilehash: a871378b3e2cc0b34c925593c6f01952de3aa08e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Undersöka och dela användningsdata med interaktiva arbetsböcker i Application Insights

Kombinera arbetsböcker [Azure Application Insights](app-insights-overview.md) datavisualiseringar, [Analytics-frågor](app-insights-analytics.md), och text i interaktiva dokument. Arbetsböcker som kan redigeras av andra användare med åtkomst till samma Azure-resurs. Det innebär att frågorna och kontroller som används för att skapa en arbetsbok som är tillgängliga för andra personer som läser arbetsboken, gör dem lättare att utforska, utöka och Sök efter fel.

Arbetsböcker som är användbara för scenarier som:

* Utforska användningen av din app när du inte vet mätvärdena som är av intresse i förväg: antal användare, kvarhållningsgrad, konvertering priser och så vidare. Till skillnad från andra analytics-verktyg för användning i Application Insights kan arbetsböcker du kombinera flera typer av grafik och analyser, gör dem utmärkt för den här typen av Friform undersökning.
* Förklarar hur en funktion som nyligen utgivna utförs för gruppen, som visar användaren antal för viktiga interaktioner och andra mått.
* Dela resultatet av en A / B experiment i din app med andra medlemmar i gruppen. Du kan förklarar syftet med experiment med text och sedan visa varje användning mått och Analytics-fråga som används för att utvärdera experiment, tillsammans med tydliga pratbubblor för om varje mått har över - eller nedan-mål.
* Rapportering effekten av ett avbrott på användningen av din app genom att kombinera data, text förklaring och en beskrivning av nästa steg för att förhindra avbrott i framtiden.

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser att använda arbetsböcker. [Lär dig att konfigurera din app att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Redigera, ordna om, kloning och ta bort arbetsboken avsnitt

En arbetsbok är en gjorda av avsnitt: oberoende redigerbara användning visualiseringar, diagram, tabeller, text eller Analytics frågeresultat.

Om du vill redigera innehållet i en arbetsbok-avsnittet, klickar du på den **redigera** knappen nedanför och till höger om avsnittet arbetsboken.

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/app-insights-usage-workbooks/editing-controls.png)

1. När du är klar redigerar ett avsnitt, klickar du på **klar redigera** i det nedre vänstra hörnet i avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt, klickar du på den **klona det här avsnittet** ikon. Att skapa dubbla avsnitt är ett bra sätt att iterera på en fråga utan att förlora tidigare iterationer.

3. Om du vill flytta upp ett avsnitt i en arbetsbok klickar du på den **Flytta upp** eller **Flytta ned** ikon.

4. Om du vill ta bort ett avsnitt permanent klickar du på den **ta bort** ikon.

## <a name="adding-usage-data-visualization-sections"></a>Lägga till användning data visualiseringen avsnitt

Arbetsböcker erbjuder fyra typer av inbyggda användning analytics visualiseringar. Varje svar på vanliga frågor om användningen av din app. Lägg till Analytics query avsnitt (se nedan) för att lägga till tabeller och diagram än följande fyra avsnitt.

Om du vill lägga till en användare, sessioner, händelser eller kvarhållning avsnittet till din arbetsbok, Använd den **Lägg till användare** eller andra motsvarande knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

![Avsnittet för användare i arbetsböcker](./media/app-insights-usage-workbooks/users-section.png)

**Användare** avsnitt besvara ”hur många användare visas en sida eller använda vissa funktion i Min plats”?

**Sessioner** avsnitt besvara ”hur många sessioner användarna lägger visar vissa sida eller en funktion i Min webbplats”?

**Händelser** avsnitt besvara ”hur många gånger användarna visa vissa sidan eller använda vissa funktioner i Min plats”?

De olika typerna tre avsnitt har samma uppsättning kontroller och visualiseringar:

* [Lär dig mer om hur du redigerar avsnitten användare, sessioner och händelser](app-insights-usage-segmentation.md)
* Växla huvuddiagram, histogram rutnät, automatisk insikter och exempel användare visualiseringar med hjälp av den **visa diagrammet**, **Visa rutnät**, **visa insikter**, och **exempel för dessa användare** kryssrutorna längst upp i varje avsnitt.

![Kvarhållning avsnitt i arbetsböcker](./media/app-insights-usage-workbooks/retention-section.png)

**Kvarhållning** avsnitt besvara ”till personer som visade vissa sidan eller vissa funktion som används på en dag eller en vecka, hur många Kom tillbaka senare dag eller vecka”?

* [Lär dig mer om hur du redigerar kvarhållning avsnitt](app-insights-usage-retention.md)
* Växla den valfria övergripande kvarhållning diagram med hjälp av den **visa övergripande kvarhållning diagram** kryssrutan längst upp i avsnittet.

## <a name="adding-application-insights-analytics-sections"></a>Lägga till Application Insights Analytics avsnitt

![Analytics-avsnittet i arbetsböcker](./media/app-insights-usage-workbooks/analytics-section.png)

Om du vill lägga till ett avsnitt för Application Insights Analytics-fråga till din arbetsbok, Använd den **lägga till Analytics query** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

Analytics query avsnitt kan du lägga till valfri frågor över Application Insights-data i arbetsböcker. Den här flexibiliteten innebär Analytics query avsnitt ska vara din gå till för att svara på frågor om din plats än fyra som anges ovan för användare, sessioner, händelser och kvarhållning, t.ex.:

* Hur många undantag webbplatsen utlösa under samma tid som en nedgång i användningen?
* Vad hette sidinläsningstider för användare som visar vissa sida distributionen?
* Hur många användare visas en uppsättning sidor på webbplatsen, men inte en annan uppsättning sidor? Detta kan vara användbar för att förstå om du har kluster av användare som använder olika delar av din webbplats funktioner (använda den `join` operator med det `kind=leftanti` modifierare i Log Analytics-frågespråket).

Använd den [logganalys fråga Språkreferens](https://docs.loganalytics.io/) vill veta mer om hur du skriver frågor.

## <a name="adding-text-and-markdown-sections"></a>Lägga till text och Markdown-avsnitt

Lägga till rubriker, beskrivningar och kommentarer i arbetsböckerna aktivera hjälper till att en uppsättning tabeller och diagram i en. Delar av texten i stödet för arbetsböcker i [markdownsyntax](https://daringfireball.net/projects/markdown/) för textformatering som rubriker, fetstil, kursiv stil och punktlista.

Lägg till ett textavsnitt i din arbetsbok, använda den **lägga till text** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med din grupp

Arbetsböcker sparas i Application Insights-resurs i antingen den **Mina rapporter** avsnitt som är privat dig eller i den **delade rapporter** avsnitt som är tillgänglig för alla med åtkomst till Application Insights-resursen. Visa alla arbetsböcker i resursen, klicka på den **öppna** knappen i åtgärdsfältet.

Dela en arbetsbok som för närvarande i **Mina rapporter**:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill dela
3. Klicka på **flytta till delade rapporter**.

Om du vill dela en arbetsbok med en länk eller via e-post, klickar du på **dela** i åtgärdsfältet. Tänk på att mottagarna av länken behöver åtkomst till den här resursen i Azure portal för att visa arbetsboken. Om du vill göra ändringar, måste du använda minst bidragsgivarbehörighet för resursen.

Så här fäster du en länk till en arbetsbok i en Azure-instrumentpanel:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill fästa
3. Klicka på **fäst på instrumentpanelen**.

## <a name="next-steps"></a>Nästa steg

## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du skickar redan anpassade händelser eller sidvisningar, utforska verktygen användning om du vill veta hur användarna använder din tjänst.
    - [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)
    
