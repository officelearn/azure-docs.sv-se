---
title: Analysera trender i Visual Studio | Microsoft Docs
description: Analysera, visualisera och utforska trender i Application Insights Telemetry i Visual Studio.
services: application-insights
documentationcenter: .net
author: numberbycolors
manager: douge
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/17/2017
ms.author: daviste
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: c0c1eb80685d18794bcae7acaa16b777ff21b3e6
ms.lasthandoff: 03/21/2017


---
# <a name="analyzing-trends-in-visual-studio"></a>Analysera trender i Visual Studio
Verktyget Application Insights Trends hjälper dig att visualisera hur webbappens viktiga telemetrihändelser ändras med tiden, så att du snabbt kan identifiera problem och inkonsekvenser. Med länkar till mer detaljerad diagnostisk information kan Trends hjälpa dig att förbättra dina apprestanda, spåra orsakerna till undantag och avslöja insikter från anpassade händelser.

![Exempel på fönster i Trends](./media/app-insights-visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Konfigurera webbappen för Application Insights

Om du inte redan har gjort det ska du [konfigurera webbappen för Application Insights](app-insights-overview.md). Då kan det skicka telemetri till Application Insights-portalen. Verktyget Trends läser telemetrin därifrån.

Application Insights Trends är tillgängligt i Visual Studio 2015 Update 3 och senare.

## <a name="open-application-insights-trends"></a>Öppna Application Insights Trends
Öppna fönstret Application Insights Trends:

* Från verktygsfältsknappen Application Insights väljer du **Utforska telemetritrender** eller
* Från snabbmenyn för projektet väljer du **Application Insights > Utforska telemetritrender** eller
* Från menyraden i Visual Studio väljer du **Visa > Andra fönster > Application Insights Trends**.

Du kan uppmanas att välja en resurs. Klicka på **Välj en resurs**, logga in med en Azure-prenumeration och välj sedan en Application Insights-resurs i listan som du vill analysera telemetritrender för.

## <a name="choose-a-trend-analysis"></a>Välj en trendanalys
![Meny över vanliga typer av trendanalyser](./media/app-insights-visual-studio-trends/app-insights-trends-1-750.png)

Kom igång genom att välja någon av fem vanliga trendanalyser, som var och en analyserar data från de senaste 24 timmarna:

* **Undersöka prestandaproblem med dina serverbegäranden** – Begäranden som görs till din tjänst, grupperade efter svarstider
* **Analysera fel i dina serverbegäranden** – Begäranden som görs till din tjänst, grupperade efter HTTP-svarskod
* **Undersök undantagen i ditt program** – Undantag från din tjänst, grupperade efter undantagstyp
* **Kontrollera prestanda för ditt programs beroenden** – Tjänster som anropas av din tjänst, grupperade efter svarstider
* **Granska dina anpassade händelser** – Anpassade händelser som du har konfigurerat för din tjänst, grupperade efter händelsetyp.

Dessa fördefinierade analyser är tillgängliga senare från knappen **Visa vanliga typer av telemetrianalyser** i det övre vänstra hörnet i Trends-fönstret.

## <a name="visualize-trends-in-your-application"></a>Visualisera trender i ditt program
Application Insights Trends skapar en tidsserievisualisering från din apps telemetri. Varje tidsserievisualisering visar en typ av telemetri, grupperad efter en egenskap för den telemetrin, över en viss tidsperiod. Du kanske till exempel vill visa serverbegäranden grupperade efter det land som de kommer från, under de senaste 24 timmarna. I det här exemplet representerar varje bubbla i visualiseringen ett antal serverbegäranden för ett visst land/en viss region under en timme.

Använd kontrollerna längst upp i fönstret för att justera vilka typer av telemetri som visas. Börja med att välja de typer av telemetri som du är intresserad av:

* **Typ av telemetri** – Serverbegäranden, undantag, beroenden eller anpassade händelser
* **Tidsintervall** – Allt från de senaste 30 minuterna till de senaste 3 dagarna
* **Gruppera efter** – Undantagstyp, problem-ID, land/region och mer.

Klicka på **Analysera telemetri** för att köra frågan.

Så här navigerar du mellan bubblor i visualiseringen:

* Klicka för att välja en bubbla. När du gör det uppdateras filtren längst ned i fönstret och en sammanfattning över bara de händelser som inträffat under en viss tidsperiod visas.
* Dubbelklicka på en bubblan om du vill gå till sökverktyget och se alla enskilda telemetrihändelser som inträffat under tidsperioden
* Tryck på Ctrl och klicka på en bubbla om du vill avmarkera den i visualiseringen.

> [!TIP]
> Trend- och sökverktyget fungerar tillsammans och hjälper till att ta reda på orsakerna till problem med din tjänst bland tusentals telemetrihändelser. Om t.ex. en av dina kunder en eftermiddag noterar att din app svarar sämre, kan du börja med Trends. Analysera begäranden som har gjorts till din tjänst under de senaste timmarna, grupperade efter svarstid. Se efter om det finns ett ovanligt stort antal långsamma begäranden. Dubbelklicka sedan på bubblan för att gå till sökverktyget, som filtrerar fram händelserna för dessa begäranden. Från sökverktyget kan du utforska innehållet i dessa begäranden och navigera till den kod som är involverad i att lösa problemet.
> 
> 

## <a name="filter"></a>Filter
Identifiera mer specifika trender med filterkontrollerna längst ned i fönstret. Om du vill använda ett filter klickar du på filtrets namn. Du kan snabbt växla mellan olika filter för att identifiera trender som kan vara dolda i en viss dimension av telemetrin. Om du använder ett filter i en dimension, som Undantagstyp, går det fortfarande att klicka på filter i andra dimensioner även om de ser ut som de är nedtonade. Om du vill ta bort ett filter klickar du på det igen. Tryck på Ctrl och klicka för att markera flera filter i samma dimension.

![Filter för trender](./media/app-insights-visual-studio-trends/TrendsFiltering-750.png)

Hur gör du om du vill använda flera filter? 

1. Tillämpa det första filtret. 
2. Klicka på knappen **Använd valda filter och fråga igen** vid namnet på dimensionen för ditt första filter. När du gör det skickas en fråga till telemetrin efter händelser som matchar det första filtret. 
3. Tillämpa ett andra filter. 
4. Upprepa stegen för att hitta trender i specifika delmängder av telemetrin. Till exempel serverbegäranden med namnet ”GET Home/Index” *och* som kommit från Tyskland *och* som tagit emot svarskod 500. 

Om du vill ta bort dessa filter klickar du på knappen **Ta bort valda filter och fråga igen** för dimensionen.

![Flera filter](./media/app-insights-visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Identifiera avvikelser
Verktyget Trends kan markera bubblor med händelser som är avvikande jämfört med andra bubblor i samma tidsserie. Välj **Antal i tidsenhet (markera avvikelser** eller **Procent i tidsenhet (markera avvikelser)**. Röda bubblor representerar bubblor med avvikande händelser. Avvikelser definieras som bubblor med ett antal/procentvärde som överstiger 2,1 gånger standardavvikelsen för antalet/procentvärdet under de föregående två tidsperioderna (48 timmar om du visar de senaste 24 timmarna osv.).

![Färgade punkter representerar avvikelser](./media/app-insights-visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> Att markera avvikelser är till särskilt stor hjälp när man försöker hitta avvikande mätvärden i tidsserier med små bubblor som annars kan se ungefär lika stora ut.  
> 
> 

## <a name="next"></a>Nästa steg
|  |  |
| --- | --- |
| **[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md)**<br/>Sök i telemetri, visa data i CodeLens och konfigurera Application Insights. Allt i Visual Studio. |![Högerklicka på projektet och välj Application Insights, Sök](./media/app-insights-visual-studio-trends/34.png) |
| **[Lägga till mer information](app-insights-asp-net-more.md)**<br/>Övervaka användning, tillgänglighet, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri. |![Visual Studio](./media/app-insights-visual-studio-trends/64.png) |
| **[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/>Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. |![Visual Studio](./media/app-insights-visual-studio-trends/62.png) |


