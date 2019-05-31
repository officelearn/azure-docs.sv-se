---
title: Visualisera data i förhandsversionen av Azure Time Series Insights-Utforskaren | Microsoft Docs
description: Den här artikeln beskrivs funktioner och alternativ som är tillgängliga i förhandsversionen av Azure Time Series Insights explorer webbapp.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399865"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualisera data i explorer Preview

Det här dokumentet beskrivs användargränssnittet och funktioner för användarupplevelse och gränssnittet för förhandsversionen av Azure Time Series Insights [demo webbapp](https://insights.timeseries.azure.com/preview/demo). Mer specifikt behandlar den layouten för de värdbaserade exemplet, alternativ för anpassning av användargränssnitt och navigering genom den tillhandahållna demonstrationen.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång med förhandsversionen av Azure Time Series Insights explorer, måste du:

* Har en Time Series Insights-miljö ställer in. Om du vill veta mer om att etablera en instans kan prova den [förhandsversion av Azure Time Series Insights](./time-series-insights-update-create-environment.md) självstudien.
* [Dataåtkomst](./time-series-insights-data-access.md) i Time Series Insights-miljön som du skapade för kontot. Du kan ge åtkomst till andra samt för att själv.
* Lägg till en händelsekälla till Time Series Insights-miljö för att skicka data till miljön:
  * Lär dig [hur du ansluter till en händelsehubb](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Lär dig [hur du ansluter till en IoT-hubb](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Lär dig mer om förhandsversion explorer

Förhandsversion av Azure Time Series Insights explorer består av följande element:

[![Explorer-vy](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Miljö panelen</a>: Visar dina Azure Time Series Insights-miljöer.
- <a href="#navigation-menu">Navigeringsmenyn</a>: Använda den för att växla mellan den **analysera** och **modellen** sidor.
- <a href="#hierarchy-tree">Hierarkiträdet</a>: Du kan använda den för att välja specifika modell och data element för diagrammet.
- <a href="#preview-well">Time series bra</a>: Visar dataelement på markerade i tabellformat med färgkodning.
- <a href="#preview-chart">Diagrammet panelen</a>: Visar aktuella fungerande diagrammet.
- <a href="#time-editor-panel">Timeline</a>: Du kan använda den för att ändra din fungerande tidsintervall.
- <a href="#navigation-panel">Appfältet</a>: Innehåller dina alternativ för användare, till exempel aktuell klient. Du kan använda den för att ändra tema och språkinställningar.

## <a name="environment-drop-down-list"></a>Listrutan för miljö

Listrutan miljö visar alla Time Series Insights-miljöer som du har åtkomst till. Listan innehåller användningsbaserad miljöer, till exempel Time Series Insights Preview. Listan innehåller också S1/S2-miljöer, som är allmänt tillgängliga.

1. Välj listrutepilen bredvid miljön visas.

   [![Kontrollpanelen](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Välj den miljö du vill.

## <a name="navigation-menu"></a>Navigeringsmeny

  [![På navigeringsmenyn](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Använd navigeringsmenyn för att välja mellan två vyer:

* **Analysera**: Du kan använda den för att skapa diagram över och utföra omfattande analysfunktioner på din modellerade eller unmodeled time series-data.
* **Modellen**: Du kan använda den för att skicka nya förhandsversionen av Time Series Insights-typer, hierarkier och instanser i din Time Series Insights-modell.

## <a name="hierarchy-tree"></a>Hierarkiträdet

Hierarkiträdet visar valda dataelement som inkluderar modeller, specifika enheter och sensorer på dina enheter.

### <a name="model-search-panel"></a>Sökruta för modellen

Du kan använda modellen sökruta för att enkelt söka efter och navigera hierarkin Time Series-modell för att hitta specifika tid-series-instanser som du vill visa i diagrammet. När du har valt dina instanser läggs de till både det aktuella diagrammet och den också.

  [![Sökruta för modellen](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Redigering av modellen

Som förhandsversion av Azure Time Series Insights stöder fullständig för att skapa, läsa, uppdatera och ta bort CRUD-åtgärder på din Time Series-modell.

* **Time Series-modell typ**: Du kan använda Time Series Insights-typer för att definiera variabler eller formler för att göra beräkningar. De är associerade med en viss Time Series Insights-instans. En typ kan ha en eller flera variabler.
* **Time Series-modell hierarkin**: Hierarkier är systematisk organisationer i dina data. Hierarkier visas relationerna mellan olika enheter i din Time Series Insights-data.
* **Time Series-modell instans**: Instanser är tidsserier själva. I de flesta fall är den **DeviceID** eller **AssetID**, vilket är den unika identifieraren för tillgången i miljön.

Läs mer om Time Series-modell i [gånger serien modeller](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Förhandsgranska bra

Prognostiseringen visar instansfält och andra metadata som är associerade med valda Time Series Insights-instanser. Du kan dölja eller visa specifika instanser från det aktuella diagrammet genom att markera kryssrutorna på höger sida. Du kan också ta bort specifika dataelement från dina aktuella data genom att välja röda **ta bort** (Papperskorgen) kontrollen på vänster sida av elementet.

  [![Förhandsversionen och](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Konfigurera om layouten för din **analysera** diagrammets sidan, väljer du ellipserna-ikonen i det övre högra hörnet:

  [![Telemetri layoutalternativ](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Om du ser följande meddelande innehåller instansen inte några data under det tidsintervall som valts. Lös problemet genom att öka tidsintervallet eller bekräfta att instansen är push-överföring av data.
>
> ![Meddelande om inga data](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Förhandsgranskningsdiagrammet

Diagrammet, kan du visa Time Series Insights-instanser som rader. Du kan dölja miljö panelen, datamodellen och time span på Kontrollpanelen genom att välja webbkontroller och göra diagrammet större.

  [![Förhandsversion av diagrammet översikt](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Valda datumintervallet**: Styr vilka dataelement är tillgängliga för visualisering.

- **Inre datum intervallet skjutreglaget verktyget**: Använd två endpoint-kontroller genom att dra dem över det tidsintervall som önskas.

- **Time span Dölj kontrollen**: Komprimerar och expanderar time span panelen redigeraren.

- **Y-axeln Formatera kontroll**: Växlar mellan alternativen tillgängliga y-axeln:

    * `Default`: Varje rad har en enskild y-axeln.
    * `Stacked`: Du kan använda den för att flera rader på samma y-axeln,-stack med y-axeln data ändring baserat på den rad som valts.
    * `Shared`: Alla y-axeln data som visas tillsammans.

- **Aktuella dataelement**: Dataelementet för valda och dess tillhörande information.

Att gå vidare till en specifik datasektor vänsterklicka på en datapunkt i det aktuella diagrammet och dra sedan det markerade området till slutpunkten för ditt val. Högerklicka på det grå markerat området och välj **Zooma**, enligt den här följande bild:

  [![Förhandsversion av diagrammet zoom](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

När du har utfört den **Zooma** åtgärd, som du ser att dina valda data som har angetts. Välj format-kontroll y-axeln för att växla mellan de tre y-axeln framställningar av dina Time Series Insights-data.

  [![Förhandsversion av diagrammet y-axeln](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Här kan du se ett exempel på delade Y-axlarna:

  [![Förhandsgranska delade Y-axlarna](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Tid redigeraren panel

När du arbetar med Time Series Insights Preview måste välja du först ett tidsintervall. Det valda tidsintervallet styr den datauppsättning som är tillgängliga för manipulering med förhandsversionen av Time Series Insights-widgetar. Följande webbkontroller är tillgängliga i förhandsversionen av Time Series Insights för att välja din fungerande tidsintervall:

  [![Panelen för val av tid](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Inre datum intervallet skjutreglaget verktyget**: Använd två endpoint-kontroller genom att dra dem över det tidsintervall som önskas. Den här inre datumintervall begränsas av skjutreglage för yttre datum intervall.

1. **Öka och minska datum intervallet knappar**: Öka eller minska din tidsintervall genom att välja någon av knapparna för intervallet.

1. **Time span Dölj kontrollen**: Här kontrollen kan du dölja alla kontroller utom verktyget inre datum intervallet skjutreglaget.

1. **Skjutreglage för yttre datum intervallet**: Använd endpoint-kontroller för att välja det yttersta datumintervallet som är tillgängliga för din inre Datumkontroll för intervallet.

1. **Snabbgånger datumintervall listrutan**: Använda den för att snabbt växla mellan förinställda time span val, till exempel senaste **30 minuter**, **senaste 12 timmarna**, eller en **anpassat intervall**. Ändra det här värdet ändras också de tillgängliga intervall intervall som beskrivs i verktyget intervallstorlek skjutreglaget.

1. **Intervallstorlek skjutreglaget**: Du kan använda den för att zooma in och ut intervall över samma tidsintervall. Den här åtgärden ger mer exakt kontroll av flytt mellan stora sektorer tid. Den visar smooth trender ned till segment som är så litet som en millisekund. Du kan använda den för att se detaljerade, högupplösta rabatter för dina data. Skjutreglagets standard startpunkt har angetts som den mest optimala vy av data från ditt val som balanserar upplösning, fråga hastighet och granularitet.

1. **Intervall för-och-från webben kontroll**: Den här kontrollen, kan du enkelt välja datum- och tidsintervall som önskas. Du kan också använda kontrollen för att växla mellan olika tidszoner. När du gör ändringar som ska tillämpas till din aktuella arbetsyta, väljer **spara**.

   [![Till och från panelen för val av](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigeringsfönster

Navigeringsfönster förhandsversionen av Time Series Insights visas överst i din Time Series Insights-app. Det innehåller följande funktioner.

### <a name="current-session-share-link-control"></a>Aktuella dela länken sessionskontroll

  [![Delningsikon](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Välj den nya **dela** ikon för att dela en URL-länk med ditt team.

  [![Dela din instans-URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Klient-avsnitt

  [![Val av klient](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Visar din aktuella Time Series Insights inloggningskonto information.
* Du kan använda den för att växla mellan de tillgängliga Temana för Time Series Insights.
* Använda den för att visa förhandsversionen [demo webbapp](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Temaval

Välj din profilikonen som finns i det övre högra hörnet för att välja ett nytt tema. Välj **Ändra tema**.

  [![Temaval](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Val av språk är också tillgängligt genom att välja profilikonen.

Azure Time Series Insights Preview stöder två teman:

* **Ljust tema**: Standardtemat som visas i hela dokumentet.
* **Mörkt tema**: Återges i Utforskaren som visas här:

  [![Valda mörkt tema](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 Miljökontroller

### <a name="preview-terms-panel"></a>Förhandsgranska termpanel

Det här avsnittet gäller enbart för befintliga S1/S2-miljöer som försöker använda Utforskaren i uppdaterade Användargränssnittet. Du kanske vill använda allmänt tillgängliga produkten och förhandsversion i kombination. Vi har lagt till vissa funktioner i befintliga användargränssnittet till den uppdaterade explorer, men du kan hämta den fullständiga UI-upplevelsen för S1/S2-miljö i befintlig Time Series Insights explorer. 

I stället för hierarkin, kan du se panelen Time Series Insights villkoren som definierar frågor i din miljö. Du kan använda den för att filtrera dina data baserat på ett predikat.

  [![Fråga där panelen](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Time Series Insights Preview redigeraren termpanelen använder följande parametrar:

**Where**: Använda where-satsen för att snabbt filtrera händelser med hjälp av uppsättningen operander visas i följande tabell. Om du gör en sökning genom att välja en operand uppdateras predikatet automatiskt utifrån sökningen. Operandtyperna som stöds är:

| Åtgärd | Typer som stöds   | Anteckningar |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Sträng, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Sträng, Bool, Double, DateTime, TimeSpan, NULL | Alla operander bör vara av samma typ eller vara konstanten NULL. |
| `HAS` | String | Endast konstant stränglitteraler tillåts på höger sida. Tom sträng och NULL är inte tillåtna. |

Läs mer om stöds frågeåtgärder och datatyper i [Time Series uttryck (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exempel på var satser

  [![Där satsen exempel](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Måttet**: En nedrullningsbar listruta som visar de numeriska kolumnerna (**Double-värden**) du kan använda som element för din aktuella diagrammet.

**Dela efter**: Den här listan visar alla tillgängliga kategoriska kolumner (strängar) i din modell som du kan gruppera dina data genom. Du kan lägga till upp till fem villkoren för att visa på samma x-axeln. Ange de parametrar du vill och välj sedan **Lägg till** att lägga till ett nytt villkor.

  [![Efterfrågade och filtrerad vy en](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Du kan visa och dölja element i panelen diagram genom att välja ikonen visas enligt följande bild. Om du vill ta bort frågor, väljer du röda **X**.

  [![Efterfrågade och filtrerad vy två](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [storage och ingående](./time-series-insights-update-storage-ingress.md) i förhandsversionen av Azure Time Series Insights.
- Läsa dokumentet Time Series Insights Preview på [datamodellering](./time-series-insights-update-tsm.md).
- Lär dig [diagnostisera och felsöka](./time-series-insights-update-how-to-troubleshoot.md) din Time Series Insights-instans.
