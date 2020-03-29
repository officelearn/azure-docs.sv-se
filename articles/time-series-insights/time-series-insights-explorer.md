---
title: Utforska data med utforskaren - Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du använder Utforskaren för Azure Time Series Insights för att visa dina IoT-data.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b8c2ba54fcc69ba126bf5f68aed99b25f8156155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046173"
---
# <a name="azure-time-series-insights-explorer"></a>Utforskaren för Azure Time Series Insights

I den här artikeln beskrivs funktionerna och alternativen i allmän tillgänglighet för [webbappen Azure](https://insights.timeseries.azure.com/)Time Series Insights Explorer . Informationsutforskaren Time Series Insights visar de kraftfulla datavisualiseringsfunktionerna som tillhandahålls av tjänsten och kan nås i din egen miljö.

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som gör det enkelt att utforska och analysera miljarder IoT-händelser samtidigt. Det ger dig en global bild av dina data, vilket gör att du snabbt kan validera din IoT-lösning och undvika kostsamma driftstopp till verksamhetskritiska enheter. Du kan upptäcka dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid. Informationsutforskaren Time Series Insights är för närvarande i offentlig förhandsversion.

> [!TIP]
> För en guidad tur genom demonstrationsmiljön, läs [snabbstarten azure time series insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Lär dig mer om att fråga data med hjälp av utforskaren Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Spela upp videon <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Komma igång med Time Series Insights med hjälp av en Azure IoT Solution Accelerator".</a>

## <a name="prerequisites"></a>Krav

Innan du kan använda Utforskaren för Time Series Insights måste du:

- Skapa en time series insights-miljö. Mer information finns i [Så här kommer du igång med Time Series Insights](./time-series-insights-get-started.md).
- [Ge åtkomst](time-series-insights-data-access.md) till ditt konto i miljön.
- Lägg till en [IoT-hubb](time-series-insights-how-to-add-an-event-source-iothub.md) eller [händelsehubbhändelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) i den.

## <a name="explore-and-query-data"></a>Utforska och fråga data

Inom några minuter efter att du har anslutit händelsekällan till time series insights-miljön kan du utforska och fråga dina tidsseriedata.

1. Starta genom att öppna [utforskaren Time Series Insights](https://insights.timeseries.azure.com/) i webbläsaren. Välj en miljö till vänster om fönstret. Alla miljöer som du har åtkomst till visas i alfabetisk ordning.

1. När du har valt en miljö använder du antingen konfigurationerna **Från** och **Till** högst upp eller väljer och drar över det tidsintervall du vill använda. Välj förstoringsglaset i det övre högra hörnet eller högerklicka på den valda tidsspannet och välj **Sök**.

1. Du kan också uppdatera tillgängligheten automatiskt varje minut genom att välja knappen **Auto on.** Knappen **Auto on** gäller bara tillgänglighetsdiagrammet, inte innehållet i huvudvisualiseringen.

1. Azure-molnikonen tar dig till din miljö i Azure-portalen.

   [![Miljöval i Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Därefter visas ett diagram som visar antalet händelser under den valda tidsintervallet. Här har du ett antal kontroller:

    - **Panelen Termer Redaktör:** Termen utrymme är där du frågar din miljö. Den finns till vänster på skärmen:
      - **MÅTT**: Den här listrutan visar alla numeriska kolumner (**Dubblar**).
      - **SPLIT BY**: Den här listrutan visar kategoriska kolumner (**Strängar**).
      - Du kan aktivera steginterpolering, visa minimum och maximum och justera y-axeln från kontrollpanelen **bredvid MEASURE**. Du kan också justera om data som visas är ett antal, medelvärde eller summan av data.
      - Du kan lägga till upp till fem termer för att visa på samma x-axel. Välj **Lägg till** om du vill lägga till en ny term eller använd knappen **Klona** den här termen för att lägga till en kopia av en befintlig term.

        [![Termer val, filtrering och frågepanel](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikat:** Använd predikatet för att snabbt filtrera dina händelser med hjälp av uppsättningen operanden som anges i följande tabell. Om du gör en sökning genom att välja eller klicka uppdateras predikatet automatiskt baserat på den sökningen. Operandtyper som stöds är:

         |Åtgärd  |Typer som stöds  |Anteckningar  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Dubbel**, **DateTime**, **Tidsspann**       |         |
         |**=**, **!=**,**<>**     | **Sträng**, **Bool**, **Dubbel,** **DateTime**, **Tidsspanna**, **NULL**        |         |
         |**IN**     | **Sträng**, **Bool**, **Dubbel,** **DateTime**, **Tidsspanna**, **NULL**        |  Alla operander ska vara av samma typ eller vara **NULL-konstant.**        |
         |**Hsa**     | **Sträng**        |  Endast konstant stränglitteraler tillåts på höger sida. Tom sträng och **NULL** är inte tillåtna.       |

      - **Exempelfrågor**

         [![Exempel GA frågor](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Du kan använda skjutreglaget **Intervallstorlek** för att zooma in och ut i intervall över samma tidsperiod. Skjutreglaget ger mer exakt kontroll av rörelse mellan stora tidssegment som visar jämna trender ner till segment så små som millisekunden, vilket gör att du kan visa och analysera detaljerade, högupplösta nedskärningar av dina data. Skjutreglagets standardstartpunkt anges som den mest optimala vyn av data från markeringen till balansupplösning, frågehastighet och granularitet.

1. Verktyget **Tidspensel** gör det enkelt att navigera från en tidsspann till en annan.

1. Välj ikonen **Spara** om du vill spara den aktuella frågan och dela den med andra användare av miljön. När du väljer **ikonen Öppna** kan du granska alla sparade frågor och eventuella delade frågor från andra användare i miljöer som du har åtkomst till.

   [![Frågor](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualisera data

1. Använd **verktyget Perspektivvy** för en samtidig vy med upp till fyra unika frågor. Knappen **Perspektivvy** finns i diagrammets övre högra hörn.

   [![Välj frågor som ska läggas till i perspektivfönstret](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Visa ett diagram för att visuellt **Chart** utforska dina data och använd diagramverktygen:

    - **Markera** eller **klicka på** en viss tidsspanna eller en enda dataserie.
    - Inom ett tidsintervall val kan du zooma eller utforska händelser.
    - I en dataserie kan du dela serien med en annan kolumn, lägga till serien som en ny term, bara visa den valda serien, utesluta den valda serien, pinga den serien eller utforska händelser från den valda serien.
    - I filterområdet till vänster om diagrammet kan du granska alla visade dataserier och ändra ordning efter värde eller namn. Du kan också visa alla dataserier eller fästa eller ej fästa serier. Du kan välja en enda dataserie och dela serien efter en annan kolumn, lägga till serien som en ny term, bara visa den valda serien, utesluta den valda serien, fästa den serien eller utforska händelser från den valda serien.
    - När du visar flera termer samtidigt kan du stapla, avacka, granska ytterligare data om en dataserie och använda samma y-axel i alla termer. Använd knapparna i diagrammets övre högra hörn.

    [![Diagramverktyg övre högra hörnet alternativinställningar](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Använd **heatmap för** att snabbt upptäcka unika eller avvikande dataserier i en viss fråga. Endast en sökterm kan visualiseras som en värmekarta.

    [![GA explorer heatmap-diagram](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. När du utforskar händelser genom att välja eller högerklicka görs **panelen HÄNDELSER** tillgängligt. Här kan du granska alla dina råa händelser och exportera dina händelser som JSON- eller CSV-filer. Time Series Insights lagrar alla rådata.

    [![Händelser](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Välj fliken **STATS** när du har utforskat händelser för att visa mönster och kolumnstatistik.

    - **Mönster**: Den här funktionen visar proaktivt de mest statistiskt signifikanta mönstren i en vald dataregion. Du behöver inte titta på tusentals händelser för att förstå vilka mönster som kräver mest tid och energi. Med Time Series Insights kan du hoppa direkt in i dessa statistiskt signifikanta mönster för att fortsätta göra en analys. Den här funktionen är också användbar för obduktionsundersökningar av historiska data.
    - **Kolumnstatistik:** Kolumnstatistik ger diagram och tabeller som bryter ned data från varje kolumn i den valda dataserien över det valda tidsintervallet.

      [![STATISTIK-stapeldiagram och alternativ](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Nu har du lärt dig mer om de viktigaste funktionerna, konfigurationsinställningarna och visningsalternativen som är tillgängliga i webbappen Time Series Insights Explorer.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [diagnostiserar och löser problem](time-series-insights-diagnose-and-solve-problems.md) i time series insights-miljön.

- Ta [snabbstartsturen Azure Time Series Insights.](time-series-quickstart.md)
