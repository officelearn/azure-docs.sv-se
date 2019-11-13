---
title: Visualisera data i Preview Explorer – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om funktioner och alternativ som är tillgängliga i Azure Time Series Insights Preview Explorer.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: f09de6fee4c9de39d98ec66714686ab1e805c4cd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014887"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualisera data i utforskaren (förhandsversion)

I det här dokumentet beskrivs funktionerna i användar gränssnittet och användar upplevelsen och gränssnittet i Azure Time Series Insights [demonstration-webbappen](https://insights.timeseries.azure.com/preview/demo)för för hands versionen. Mer specifikt diskuterar den layout för det värdbaserade exemplet, gränssnitts anpassnings alternativ och navigering genom den tillhandahållna demon.

## <a name="prerequisites"></a>Krav

För att komma igång med Azure Time Series Insights Preview Explorer måste du:

* Har en Time Series Insights-miljö ställer in. Om du vill veta mer om hur du konfigurerar en instans kan du prova med självstudien [Azure Time Series Insights för hands version](./time-series-insights-update-create-environment.md) .
* [Ge data åtkomst](./time-series-insights-data-access.md) till den Time Series Insightss miljö som du har skapat för kontot. Du kan ge åtkomst till andra samt för att själv.
* Lägg till en händelse källa i Time Series Insightss miljön för att skicka data till miljön:
  * Lär dig [hur du ansluter till en Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Lär dig [hur du ansluter till en IoT-hubb](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Lär dig mer om Preview Explorer

Förhandsversion av Azure Time Series Insights explorer består av följande element:

[![Utforskarvyn](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Miljö panel</a>: visar Azure Time Series Insights miljöer.
- <a href="#navigation-menu">Navigerings meny</a>: Använd den för att växla mellan sidorna **analysera** och **modeller** .
- <a href="#hierarchy-tree">Hierarkiträd</a>: Använd det för att välja en speciell modell och data element som ska visas i diagrammet.
- <a href="#preview-well">Tids serie källa</a>: visar de data element som är markerade i tabell format med färg kodning.
- <a href="#preview-chart">Diagrammet panelen</a>: Visar aktuella fungerande diagrammet.
- <a href="#time-editor-panel">Tids linje</a>: Använd den för att ändra din arbets tids period.
- <a href="#navigation-panel">App-fältet</a>: innehåller alternativ för användar hantering, till exempel aktuell klient. Du kan använda den för att ändra tema-och språk inställningar.

## <a name="environment-drop-down-list"></a>List rutan miljö

I list rutan miljö visas alla Time Series Insights-miljöer som du har åtkomst till. Listan innehåller miljöer där du betalar per användning, till exempel för hands versionen Time Series Insights. Listan innehåller även S1/S2-miljöer, som är allmänt tillgängliga.

1. Välj den nedrullningsbara pilen bredvid den miljö som visas.

   [![kontroll panelen](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Välj sedan den miljö som du vill använda.

## <a name="navigation-menu"></a>Navigeringsmeny

  [![navigerings menyn](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Använd navigerings menyn för att välja mellan två vyer:

* **Analysera**: Använd det för att rita och utföra omfattande analyser på dina modellerade eller avmodellerade tids serie data.
* **Modell**: Använd den för att skicka nya Time Series Insights förhands gransknings typer, hierarkier och instanser till din Time Series Insightss modell.

## <a name="hierarchy-tree"></a>Hierarkiträd

Hierarkiträdet visar markerade data element som innehåller modeller, vissa enheter och sensorer på dina enheter.

### <a name="model-search-panel"></a>Panelen modell sökning

Du kan använda modell Sök panelen för att enkelt söka efter och navigera i tids serie modellens hierarki för att hitta de tids serie instanser som du vill visa i diagrammet. När du har valt dina instanser läggs de till i både det aktuella diagrammet och data källan.

  [![modell Sök panelen](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Modell redigering

Azure Time Series Insights för hands versionen stöder åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) i din tids serie modell.

* **Tids serie modell typ**: du kan använda Time Series Insights typer för att definiera variabler eller formler för att utföra beräkningar. De är kopplade till en specifik Time Series Insights-instans. En typ kan ha en eller flera variabler.
* **Time Series-modell hierarkin**: hierarkier är systematisk organisationer i dina data. Hierarkier visas relationerna mellan olika enheter i din Time Series Insights-data.
* **Time Series-modell instans**: instanser är tidsserier själva. I de flesta fall är de de som är **DeviceID** eller **AssetID**, som är den unika identifieraren för till gången i miljön.

Läs mer om Time Series-modell i [gånger serien modeller](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Bra för hands version

Visar instans fälten och andra metadata som är associerade med valda Time Series Insights instanser. Genom att markera kryss rutorna på höger sida kan du dölja eller visa vissa instanser från det aktuella diagrammet. Du kan också ta bort vissa data element från dina aktuella data genom att välja den röda **borttagningen** (pappers korgen) på vänster sida av elementet.

  [![för hands versions källa](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Om du vill konfigurera om layouten för sidan **analysera** diagram väljer du ikonen med punkter i det övre högra hörnet:

  [layout alternativ för ![telemetri](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Om du ser följande meddelande har instansen inga data under den valda tids perioden. Du löser problemet genom att öka tidsintervallet eller bekräfta att instansen skickar data.
>
> ![Ingen data avisering](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Förhandsgranska diagram

Med diagrammet kan du Visa Time Series Insights förekomster som rader. Du kan minimera kontroll panelen för miljö panelen, data modellen och tids rymden genom att välja webb kontrollerna för att göra diagrammet större.

  [Översikt över ![för hands versions diagram](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Valt datum intervall**: styr vilka data element som är tillgängliga för visualisering.

- **Skjutreglaget för inre datum intervall**: Använd de två slut punkts kontrollerna genom att dra dem under den tidsrymd du vill ha.

- **Time span Dölj kontrollen**: komprimerar och expanderar time span panelen redigeraren.

- **Kontroll av y-axelns format**: växlar mellan tillgängliga visnings alternativ för y-axeln:

    * `Default`: varje rad har en enskild y-axel.
    * `Stacked`: Använd den för att stapla flera rader på samma y-axel, med data för y-axeln, baserat på den valda linjen.
    * `Shared`: alla y-axelns data visas tillsammans.

- **Aktuella dataelement**: dataelementet för valda och dess tillhörande information.

Om du vill öka detalj nivån till en viss data sektor, vänsterklickar du på en data punkt i den aktuella grafen och drar sedan det valda fältet till önskad slut punkt. Högerklicka på det grå markerade fältet och välj **Zooma**, som du ser i följande bild:

  [Zooma ![för hands versions diagram](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

När du har utfört **zoomnings** åtgärden visas den valda data uppsättningen. Välj format kontrollen y-axel för att gå igenom de tre y-axelns representationer av dina Time Series Insights data.

  [![för hands version i diagrammet y-axel](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Här kan du se ett exempel på delade Y-axlar:

  [![förhandsgranska delade Y-axlar](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Tids redigerings panel

När du arbetar med Time Series Insights Preview måste välja du först ett tidsintervall. Det valda tidsintervallet styr den data uppsättning som är tillgänglig för manipulering med Time Series Insights Preview-widgetar. Följande webb kontroller är tillgängliga i Time Series Insights för hands version för att välja arbets tids intervall:

  [panel för ![val av tid](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Skjutreglaget för inre datum intervall**: Använd de två slut punkts kontrollerna genom att dra dem under den tidsrymd du vill ha. Det här interna datum intervallet begränsas av skjutreglaget för yttre datum intervall.

1. **Öka och minska datum intervallet knappar**: öka eller minska din tid sträcker sig över genom att välja någon av knapparna för intervallet.

1. **Tids period, Dölj kontroll**: med den här webb kontrollen kan du dölja alla kontroller förutom det inre datum intervallets skjutreglage.

1. **Skjutreglage för yttre datum intervall**: Använd slut punkts kontrollerna för att välja det yttre datum intervallet, som är tillgängligt för den interna datum intervalls kontrollen.

1. **Snabb tid för datum intervall**: Använd den för att snabbt växla mellan förinställda tids markeringar, till exempel de senaste **30 minuterna**, de **senaste 12 timmarna**eller ett **anpassat intervall**. Ändra det här värdet ändras också de tillgängliga intervall intervall som beskrivs i verktyget intervallstorlek skjutreglaget.

1. **Skjutreglaget för intervall storlek**: Använd det för att zooma in och ut ur intervall över samma tidsintervall. Den här åtgärden ger mer exakt kontroll av flytt mellan stora sektorer tid. Den visar jämna trender nedåt till sektorer som små i millisekunder. Du kan använda den för att se detaljerade och högupplösta klipp med dina data. Skjutreglagets standard startpunkt har angetts som den mest optimala vy av data från ditt val som balanserar upplösning, fråga hastighet och granularitet.

1. **Datum intervall till-och-från webb kontroll**: med den här webb kontrollen kan du enkelt välja de datum-och tidsintervall som du vill använda. Du kan också använda kontrollen för att växla mellan olika tidszoner. När du har gjort ändringarna som ska gälla för din aktuella arbets yta väljer du **Spara**.

   [![till och från markerings panelen](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigerings panel

Navigerings panelen för Time Series Insights för hands version visas överst i din Time Series Insights-app. Det innehåller följande funktioner:

### <a name="current-session-share-link-control"></a>Aktuella dela länken sessionskontroll

  [![delnings ikon](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Välj den nya **delnings** ikonen för att dela en URL-länk med ditt team.

  [![dela din instans-URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Klient-avsnitt

  [![klient val](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Visar din aktuella Time Series Insights inloggnings konto information.
* Använd den för att växla mellan tillgängliga Time Series Insights teman.
* Använd den för att visa [demonstrations-webbappen](https://insights.timeseries.azure.com/preview/demo)för för hands versionen.

### <a name="theme-selection"></a>Temaval

Välj din profil ikon som finns i det övre högra hörnet för att välja ett nytt tema. Välj sedan **ändra tema**.

  [![tema val](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Val av språk är också tillgängligt genom att välja din profil ikon.

Azure Time Series Insights Preview stöder två teman:

* **Ljust tema**: standard temat visas i det här dokumentet.
* **Mörkt tema**: återger Utforskaren så som visas här:

  [![valt mörkt tema](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 Miljökontroller

### <a name="preview-terms-panel"></a>Panelen villkor för förhands granskning

Det här avsnittet gäller enbart för befintliga S1/S2-miljöer som försöker använda Utforskaren i uppdaterade Användargränssnittet. Du kanske vill använda den allmänt tillgängliga produkten och för hands versionen i kombination. Vi har lagt till vissa funktioner i befintliga användargränssnittet till den uppdaterade explorer, men du kan hämta den fullständiga UI-upplevelsen för S1/S2-miljö i befintlig Time Series Insights explorer. 

I stället för hierarkin ser du panelen Time Series Insights villkor, där du definierar frågor i din miljö. Använd den för att filtrera dina data baserat på ett predikat.

  [![där panelen fråga](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Time Series Insights Preview redigeraren termpanelen använder följande parametrar:

**Där**: Använd WHERE-satsen för att snabbt filtrera händelser med hjälp av en uppsättning operander som anges i följande tabell. Om du gör en sökning genom att välja en operand uppdateras predikatet automatiskt utifrån sökningen. De operands typer som stöds är följande:

| Åtgärd | Typer som stöds   | Anteckningar |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Sträng, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Sträng, Bool, Double, DateTime, TimeSpan, NULL | Alla operander bör vara av samma typ eller vara konstanten NULL. |
| `HAS` | Sträng | Endast strängar med konstanta strängar är tillåtna på höger sida. En tom sträng och NULL är inte tillåtet. |

Mer information om frågor och data typer som stöds finns i [tids serie uttryck (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exempel på WHERE-satser

  [![WHERE-sats exempel](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mått**: en nedrullningsbar lista som visar alla numeriska kolumner (**dubbla**) som du kan använda som element för det aktuella diagrammet.

**Dela efter**: den här List rutan visar alla tillgängliga kategoriska-kolumner (strängar) i din modell som du kan gruppera dina data efter. Du kan lägga till upp till fem villkor för att visa på samma x-axel. Ange de parametrar som du vill använda och välj sedan **Lägg** till för att lägga till en ny term.

  [![frågad och filtrerad vy en](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Du kan visa och dölja element i diagram panelen genom att välja ikonen synlig, som du ser i följande bild. Om du vill ta bort frågor helt väljer du det röda **krysset**.

  [![frågad och filtrerad vy två](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [lagring och ingress](./time-series-insights-update-storage-ingress.md) i Azure Time Series Insights för hands versionen.
- Läs Time Series Insights för hands versions dokument för [data modellering](./time-series-insights-update-tsm.md).
- Lär dig [att diagnostisera och felsöka](./time-series-insights-update-how-to-troubleshoot.md) Time Series Insights-instansen.
