---
title: Analysera trender i Visual Studio | Microsoft Docs
description: Analysera, visualisera och utforska trender i Application Insights Telemetry i Visual Studio.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: 07848d0b1a2677595eba0da53402993103289d4f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318172"
---
# <a name="analyzing-trends-in-visual-studio"></a>Analysera trender i Visual Studio
Verktyget Application Insights Trends hjälper dig att visualisera hur webbappens viktiga telemetrihändelser ändras med tiden, så att du snabbt kan identifiera problem och inkonsekvenser. Med länkar till mer detaljerad diagnostiskinformation kan Trends hjälpa dig att förbättra dina apprestanda, spåra orsakerna till undantag och få insikter från anpassade händelser.

![Exempel på fönster i Trends](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Konfigurera webbappen för Application Insights

Om du inte redan har gjort det ska du [konfigurera webbappen för Application Insights](./app-insights-overview.md). Då kan det skicka telemetri till Application Insights-portalen. Verktyget Trends läser telemetrin därifrån.

Application Insights Trends är tillgängligt i Visual Studio 2015 Update 3 och senare.

## <a name="open-application-insights-trends"></a>Öppna Application Insights Trends
Öppna fönstret Application Insights Trends:

* Från verktygsfältsknappen Application Insights väljer du **Utforska telemetritrender** eller
* Från snabbmenyn för projektet väljer du **Application Insights > Utforska telemetritrender** eller
* Från menyraden i Visual Studio väljer du **Visa > Andra fönster > Application Insights Trends**.

Du kan uppmanas att välja en resurs. Klicka på **Välj en resurs**, logga in med en Azure-prenumeration och välj sedan en Application Insights-resurs i listan som du vill analysera telemetritrender för.

## <a name="choose-a-trend-analysis"></a>Välj en trendanalys
![Meny över vanliga typer av trendanalyser](./media/visual-studio-trends/app-insights-trends-1-750.png)

Sätt igång genom att välja någon av fem vanliga trendanalyser, som var och en analyserar data från de senaste 24 timmarna:

* **Undersöka prestandaproblem med dina serverbegäranden** – Begäranden som görs till din tjänst, grupperade efter svarstider
* **Analysera fel i dina serverbegäranden** – Begäranden som görs till din tjänst, grupperade efter HTTP-svarskod
* **Undersök undantagen i ditt program** – Undantag från din tjänst, grupperade efter undantagstyp
* **Kontrollera prestanda för ditt programs beroenden** – Tjänster som anropas av din tjänst, grupperade efter svarstider
* **Granska dina anpassade händelser** – Anpassade händelser som du har konfigurerat för din tjänst, grupperade efter händelsetyp.

Dessa fördefinierade analyser är tillgängliga senare från knappen **View common types of telemetry analysis** (Visa vanliga typer av telemetrianalyser) i det övre vänstra hörnet i Trends-fönstret.

## <a name="visualize-trends-in-your-application"></a>Visualisera trender i ditt program
Application Insights Trends skapar en tidsserievisualisering från din apps telemetri. Varje tidsserievisualisering visar en typ av telemetri, grupperad efter en egenskap för den telemetrin, över en viss tidsperiod. Du kanske till exempel vill visa server begär Anden, grupperade efter det land/den region som de kom från, under de senaste 24 timmarna. I det här exemplet representerar varje bubbla i visualiseringen ett antal serverbegäranden för ett visst land/en viss region under en timme.

Använd kontrollerna längst upp i fönstret för att välja vilka telemetrityper som visas. Börja med att välja de telemetrityper som du är intresserad av:

* **Typ av telemetri** – server begär Anden, undantag, beroenden eller anpassade händelser
* **Tidsintervall** – Allt från de senaste 30 minuterna till de senaste 3 dagarna
* **Gruppera efter** – Undantagstyp, problem-ID, land/region och mer.

Klicka sedan på **analysera telemetri** för att köra frågan.

Så här navigerar du mellan bubblor i visualiseringen:

* Klicka för att välja en bubbla. När du gör det uppdateras filtren längst ned i fönstret och en sammanfattning över bara de händelser som inträffat under en viss tidsperiod visas.
* Dubbelklicka på en bubbla för att navigera till sökverktyget och se alla enskilda telemetri händelser som inträffat under den tids perioden
* Tryck på Ctrl och klicka på en bubbla om du vill avmarkera den i visualiseringen.

> [!TIP]
> Trend- och sökverktyget fungerar tillsammans och hjälper till att ta reda på orsakerna till problem med din tjänst bland tusentals telemetrihändelser. Om t.ex. en av dina kunder en eftermiddag noterar att din app svarar sämre, kan du börja med Trends. Analysera begäranden som har gjorts till din tjänst under de senaste timmarna, grupperade efter svarstid. Se efter om det finns ett ovanligt stort antal långsamma begäranden. Dubbelklicka sedan på bubblan för att gå till sökverktyget, som filtrerar fram händelserna för dessa begäranden. Från sökverktyget kan du utforska innehållet i dessa begäranden och navigera till den kod som är involverad i att lösa problemet.
> 
> 

## <a name="filter"></a>Filter
Identifiera mer specifika trender med filterkontrollerna längst ned i fönstret. Om du vill använda ett filter klickar du på filtrets namn. Du kan snabbt växla mellan olika filter för att identifiera trender som kan vara dolda i en viss dimension av telemetrin. Om du använder ett filter i en dimension, t. ex. undantags typ, så förblir filter i andra dimensioner klickata även om de visas som nedtonade. Om du vill ta bort ett filter klickar du på det igen. Tryck på Ctrl och klicka för att markera flera filter i samma dimension.

![Filter för trender](./media/visual-studio-trends/TrendsFiltering-750.png)

Hur gör du om du vill använda flera filter? 

1. Tillämpa det första filtret. 
2. Klicka på knappen **Apply selected filters and query again** (Använd valda filter och fråga igen) vid namnet på dimensionen för ditt första filter. När du gör det skickas en fråga till telemetrin efter händelser som matchar det första filtret. 
3. Tillämpa ett andra filter. 
4. Upprepa samma steg för att hitta trender i specifika deluppsättningar av telemetrin. Till exempel serverbegäranden med namnet ”GET Home/Index” *och* som kommit från Tyskland *och* som tagit emot svarskod 500. 

Om du vill ta bort filtren klickar du på knappen **Remove selected filters and query again** (Ta bort valda filter och fråga igen) för dimensionen.

![Flera filter](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Identifiera avvikelser
Verktyget Trends kan märka ut bubblor med händelser som avviker från andra bubblor i samma tidsserie. Välj **Antal i tidsenhet (markera avvikelser** eller **Procent i tidsenhet (markera avvikelser)**. Röda bubblor representerar avvikelser. Avvikelser definieras som bubblor med antal/procent som överstiger 2,1 gånger standard avvikelsen för antal/procent som inträffat under de senaste två tids perioderna (48 timmar om du visar de senaste 24 timmarna osv.).

![Färgade punkter representerar avvikelser](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> Att markera avvikelser är till särskilt stor hjälp när man försöker hitta avvikande mätvärden i tidsserier med små bubblor som annars kan se ungefär lika stora ut.  
> 
> 

## <a name="next-steps"></a><a name="next"></a>Nästa steg
|  |  |
| --- | --- |
| **[Arbeta med Application Insights i Visual Studio](./visual-studio.md)**<br/>Sök i telemetri, visa data i CodeLens och konfigurera Application Insights. Allt i Visual Studio. |![Högerklicka på projektet och välj Application Insights, Sök](./media/visual-studio-trends/34.png) |
| **[Lägga till mer information](./asp-net-more.md)**<br/>Övervaka användning, tillgänglighet, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri. |![Visual Studio](./media/visual-studio-trends/64.png) |
| **[Arbeta med Application Insights-portalen](./overview-dashboard.md)**<br/>Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. |![Visual Studio](./media/visual-studio-trends/62.png) |

