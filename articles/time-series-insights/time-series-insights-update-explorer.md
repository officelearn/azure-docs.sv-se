---
title: Visualisera data i utforskaren för förhandsversionen – Insikter i Azure Time Series | Microsoft-dokument
description: Lär dig mer om funktioner och alternativ som är tillgängliga i utforskaren för förhandsversionen av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861769"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Förhandsutforskare för Azure Time Series Insights

I den här artikeln beskrivs de olika funktioner och alternativ som är tillgängliga i [demowebbprogrammet](https://insights.timeseries.azure.com/preview/demo)för azure time series insights .

## <a name="prerequisites"></a>Krav

Om du vill komma igång med förhandsversionen av Azure Time Series Insights måste du:

* Ha en time series insights-miljö etablerad. Läs mer om att etablera en instans genom att läsa azure [time series insights preview-självstudiekursen.](./time-series-insights-update-create-environment.md)
* [Ge dataåtkomst](./time-series-insights-data-access.md) till time series insights-miljön som du skapade för kontot. Du kan ge tillgång till andra och till dig själv.
* Lägg till en händelsekälla i time series insights-miljön för att skicka data till miljön:
  * Lär dig hur du [ansluter till en händelsehubb](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Lär dig hur du [ansluter till en IoT-hubb](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Utforska förhandsgranskningsutforskaren för Time Series Insights

Förhandsutforskaren för Azure Time Series Insights består av följande sju element:

[![Översikt över utforskaren för förhandsversionen av Time Series Insights](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Miljöpanel:](#1-environment-panel)Visar alla dina Azure Time Series Insights-miljöer.
1. [Navigeringsfält:](#2-navigation-bar)Låter dig växla mellan **analys-** och **modellsidorna.**
1. [Hierarkiträd och sökpanel:](#3-hierarchy-tree-and-search-panel)Gör att du kan välja och söka efter specifika dataelement som ska kartläggas.
1. [Tidsserier väl:](#4-time-series-well)Visar alla dina markerade dataelement.
1. [Diagrampanel](#5-chart-panel): Visar det aktuella arbetsdiagrammet.
1. [Tidslinje](#6-time-editor-panel): Gör att du kan ändra arbetstidsintervallet.
1. [Appfältet](#7-app-bar): Innehåller dina alternativ för användarhantering (till exempel aktuell klient) och låter dig ändra dem och språkinställningar.


## <a name="1-environment-panel"></a>1. Miljöpanel

På miljöpanelen visas alla Time Series Insights-miljöer som du har åtkomst till. Listan innehåller förhandsversionsmiljöer som du är (förhandsgranska) samt S1/S2-miljöer (allmän tillgänglighet). Välj bara den Time Series Insights-miljö som du vill använda för att omedelbart tas dit.

1. Markera nedpilen bredvid den miljö som visas.

   [![Panelen Miljö](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Välj sedan den miljö du vill ha.

## <a name="2-navigation-bar"></a>2. Navigeringsfält

  [![Navigeringsfältet](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Använd navigeringsfältet för att välja mellan två vyer:

* **Analysera**: Använd den för att kartlägga och utföra omfattande analyser på dina modellerade eller obyggda tidsseriedata.
* **Modell:** Använd den för att skicka nya förhandsgranskningstyper, hierarkier och instanser till time series insights-modellen.

### <a name="model-authoring"></a>Modell författande

Förhandsversionen av Azure Time Series Insights stöder fullständiga åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på din Time Series-modell.

[![Modellsökpanelen](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ av tidsseriemodell:** Du kan använda typer av Time Series Insights för att definiera variabler eller formler för att göra beräkningar. De är associerade med en viss Time Series Insights-instans. En typ kan ha en eller flera variabler.
* **Tidsseriemodellhierarki:** Hierarkier är systematiska organisationer för dina data. Hierarkier visar relationerna mellan olika entiteter i data för Time Series Insights.
* **Time Series-modellinstans:** Instanser är själva tidsserierna. I de flesta fall är de **DeviceID** eller **AssetID**, som är den unika identifieraren för tillgången i miljön.

Om du vill veta mer om Time Series-modellen läser du [Times Series Models](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Hierarkiträd och sökpanel

Med hierarkiträdet och sökpanelen kan du enkelt söka och navigera i [hierarkin i tidsseriemodellen](./time-series-insights-update-tsm.md) för att hitta de specifika tidsserieinstanser som du vill visa i diagrammet. När du väljer dina instanser läggs de inte bara till i det aktuella diagrammet utan läggs också till i databrunnen. 

[![Hierarkiträd och sökpanel](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

I sökresultatfönstret kan du också visa dina resultat i en hierarkivy eller en listvy som gör det enkelt att hitta de instanser du vill visa.
 
## <a name="4-time-series-well"></a>4. Tidsserier väl

Brunnen visar förekomstfält och andra metadata som är associerade med valda Time Series Insights-instanser. Genom att markera kryssrutorna till höger kan du dölja eller visa specifika förekomster från det aktuella diagrammet. 

  [![Förhandsgranskningsbrunn](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Du kan ta bort specifika dataelement från dina aktuella data väl genom att välja den röda **kontrollen Ta bort** (papperskorgen) till vänster om elementet. Med brunnen kan du också styra hur varje element visas i diagrammet. Du kan välja att lägga till min/max skuggor, datapunkter, flytta elementet i tid och visualisera förekomsten ett stegvis sätt. 

Dessutom kan du enkelt skapa tidsskift och utspridda tomter.  

  [![Väl layoutalternativ](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Om följande meddelande visas har instansen inga data under den valda tidsperioden. Lös problemet genom att öka tidsperioden eller bekräfta att instansen driver data.
>
> ![Ingen dataanmälan](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Diagrampanel

I diagrammet kan du visa tidsserieförekomster som linjer. Du kan dölja miljöpanelen, datamodellen och kontrollpanelen för tidsspannet genom att klicka på webbkontrollerna för att göra diagrammet större. 

  [![Översikt över förhandsgranskningsdiagram](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Diagramtyp**: Styr vilka dataelement som är tillgängliga för visualisering.

1. **Intervallstorlek**: Skjutreglaget För intervallstorlek kan du zooma in och ut i intervall under samma tidsperiod. Detta ger mer exakt kontroll av rörelse mellan stora segment av tid som visar jämna trender ner till segment så små som millisekunden, så att du kan granska detaljerade, högupplösta nedskärningar av dina data. Skjutreglagets standardstartpunkt anges som den mest optimala vyn av data från markeringen. balanseringsupplösning, frågehastighet och granularitet.

1. **Zooma och panorera:** Markera den här kontrollen för att zooma och panorera diagrammet.

1. **Y-axelkontroll**: Växlar genom de tillgängliga y-axelvyalternativen:

    * `Stacked`: Varje rad har en enskild Y-axel.
    * `Overlap`: Använd den för att stapla flera rader på samma Y-axel, med Y-axelns data som ändras baserat på raden markerad.
    * `Shared`: Alla Y-axliga data visas tillsammans.

1. **Markörelement:** Det markerade dataelementet och dess tillhörande information.

Du kan öka detaljnivån ytterligare i ett visst datasegment genom **att vänsterklicka** på en datapunkt i det aktuella diagrammet medan du håller ned musen och sedan drar det markerade området till den slutpunkt du väljer. **Högerklicka på** det blå, markerade området och välj sedan **Zooma** enligt nedan. Du kan också visa och hämta telemetrihändelserna i den valda tidsspannet.

  [![Zooma för förhandsgranskningsdiagram](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

När du har utför **zoomningsåtgärden** visas den valda datauppsättningen. Välj formatkontrollen som ska växla mellan de tre y-axelrepresentationerna i time series insights-data.

  [![Förhandsgranska diagramy-axeln](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Här finns ett exempel på ett **överlappande diagram:**

  [![Alternativet Överlappande diagram](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Knappen **Fler åtgärder** utökas för att visa alternativen Hämta som **CSV**, Anslut till Power **BI**, **Visa diagramdata som en tabell**och Utforska alternativ för **råhändelser.**

  [![Fler åtgärder alternativ](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Läs mer om alternativet **Anslut till Power BI** i Time Series [Insights inbyggda Power BI-kontakt](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Panelen Tidsredigerare

När du arbetar med Time Series Insights väljer du först ett tidsintervall. Det valda tidsintervallet styr den datauppsättning som är tillgänglig för manipulering med uppdateringswidgetarna Time Series Insights.

  [![Panelen för val av tid](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> En del av tidslinjen markeras i en gul eller orange färg för att ange hur mycket data som är tillgängligt i varmt lager.

Följande webbkontroller är tillgängliga i time series insights-uppdateringen för att välja din arbetstidsintervall. 

  [![Kontroll av prospekteringsborrning](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Skjutreglaget för det inre datumintervallet**: Använd de två slutpunktskontrollerna genom att dra dem över det tidsperioder du vill ha. Det här inre datumintervallet begränsas av skjutreglaget för det yttre datumintervallet.

1. **Öka och minska knapparna för datumintervall:** Öka eller minska tidsintervallet genom att välja endera knappen för det intervall du vill ha.

1. **Kontroll för tidsspannkollaps:** Med den här webbkontrollen kan du dölja alla kontroller utom skjutreglaget för det inre datumintervallet.

1. **Skjutreglagekontroll för yttre datumintervall**: Använd slutpunktskontrollerna för att välja det yttre datumintervallet, som kommer att vara tillgängligt för den inre datumintervallkontrollen.

1. **Skjutreglagekontroll för tidsintervall:** Använd den för att snabbt växla mellan förinställda tidsspannval, till exempel de **senaste 30 minuterna,** de **senaste 12 timmarna**eller ett **anpassat intervall**. Om du ändrar det här värdet ändras också de tillgängliga intervallintervall som beskrivs i skjutreglaget för intervallstorlek.

   [![Till och från urvalspanelen](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Appfältet

Navigeringspanelen Förhandsgranskning av Time Series Insights visas högst upp i appen Time Series Insights. Det ger följande funktioner:

### <a name="current-session-share-link-control"></a>Länkkontroll för aktuell sessionsresurs

  [![Ikonen Dela](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Välj den nya **ikonen Dela** om du vill dela en URL-länk med ditt team.

  [![Dela instans-URL:en](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Avsnittet Hyresgäst

  [![Val av klient](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Visar inloggningsuppgifter för dina aktuella time series-insikter.
* Använd den för att växla mellan de tillgängliga temana Time Series Insights.
* Använd den för att visa appen Förhandsgranska [demo.](https://insights.timeseries.azure.com/preview/demo)

### <a name="theme-selection"></a>Val av tema

Om du vill välja ett nytt tema väljer du din profilikon i det övre högra hörnet. Välj sedan **Ändra tema**.

  [![Val av tema](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Språkval är också tillgängligt genom att välja din profilikon.

Förhandsversionen av Azure Time Series Insights stöder två teman:

* **Ljust tema**: Standardtemat som visas i hela det här dokumentet.
* **Mörkt tema**: Återger utforskaren som visas här:

  [![Valt mörkt tema](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Miljökontroller för S1/S2

### <a name="preview-terms-panel"></a>Panelen Förhandsgranska termer

Det här avsnittet gäller endast befintliga S1/S2-miljöer som försöker använda utforskaren i det uppdaterade användargränssnittet. Du kanske vill använda den allmänt tillgängliga produkten och Förhandsgranska i kombination. Vi har lagt till några funktioner från det befintliga användargränssnittet till den uppdaterade utforskaren, men du kan få den fullständiga användargränssnittsupplevelsen för S1/S2-miljön i den befintliga Time Series Insights-utforskaren. 

I stället för hierarkin visas termpanelen Time Series Insights. Med termpanelen kan du definiera frågor i din miljö. Använd den för att också filtrera data baserat på ett predikat.

  [![Var frågepanelen](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Time Series Insights Preview-termer redigeringspanelen tar följande parametrar:

**Var:** Använd varsatsen för att snabbt filtrera dina händelser med hjälp av uppsättningen operanden som anges i följande tabell. Om du gör en sökning genom att välja en operande uppdateras predikatet automatiskt baserat på den sökningen. Operandtyper som stöds är följande:

| Åtgärd | Typer som stöds   | Anteckningar |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Dubbel, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Sträng, Bool, Dubbel, DateTime, Timespan, NULL |
| `IN` | Sträng, Bool, Dubbel, DateTime, Timespan, NULL | Alla operander ska vara av samma typ eller vara NULL-konstant. |
| `HAS` | String | Endast konstant stränglitteraler är tillåtna på höger sida. Tom sträng och NULL är inte tillåtna. |

Om du vill veta mer om frågeåtgärder och datatyper som stöds läser du [TSX (Time Series Expression).](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="examples-of-where-clauses"></a>Exempel på varsatser

  [![Om satser exempel](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Mått:** En listruta som visar alla numeriska kolumner (**Dubblar)** som du kan använda som element för det aktuella diagrammet.

**Dela med**: Den här listrutan visar alla tillgängliga kategoriska kolumner (Strängar) i modellen som du kan gruppera dina data efter. Du kan lägga till upp till fem termer för att visa på samma x-axel. Ange önskade parametrar och välj sedan **Lägg till** för att lägga till en ny term.

  [![Efterfrågad och filtrerad vy ett](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Du kan visa och dölja element på diagrampanelen genom att markera den synliga ikonen, som visas i följande bild. Om du vill ta bort frågor helt markerar du det röda **X.**

  [![Avbryta ett frågat och filtrerat alternativ](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [lagring och inträngning i](./time-series-insights-update-storage-ingress.md) förhandsversionen av Azure Time Series Insights.

- Läs förhandsgranskningsdokumentet Förhandsgranskning av Tidsseriestatistik om [datamodellering](./time-series-insights-update-tsm.md).

- Läs om hur du [diagnostiserar och felsöker](./time-series-insights-update-how-to-troubleshoot.md) time series insights-instansen.
