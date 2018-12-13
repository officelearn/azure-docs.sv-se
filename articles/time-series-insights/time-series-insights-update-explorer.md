---
title: Visualisera data i förhandsversionen av Azure Time Series Insights-Utforskaren | Microsoft Docs
description: Den här artikeln beskrivs funktioner och alternativ som är tillgängliga i förhandsversionen av Azure Time Series Insights explorer webbapp.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 3a2958d838f0646ac030421b3785fc11a1466dd6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086733"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualisera data i explorer Preview

Den här artikeln beskrivs funktioner och alternativ som är tillgängliga i förhandsversionen av Azure Time Series Insights [explorer webbapp](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Förutsättningar

Innan du använder förhandsversionen av Azure Time Series Insights explorer måste du:

* Har en Time Series Insights-miljö ställer in. Mer information finns i [Självstudier: Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md).
* Tillhandahålla dataåtkomst i Time Series Insights-miljön som du skapade för kontot. Du kan ge åtkomst till andra samt för att själv.
* Lägga till en händelsekälla till Time Series Insights-miljö för att skicka data till miljön.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Lär dig mer om förhandsversionen av Azure Time Series Insights explorer

  ![Explorer-ett][1]

Förhandsversion av Azure Time Series Insights explorer består av följande element:

* **Navigeringsfältet**: kan du växla mellan sidorna analytics och modellen.
* **Hierarkiträdet**: du kan välja specifika dataelement för diagrammet.
* **Time series bra**: Visar dina valda dataelement.
* **Diagrammet panelen**: Visar aktuella fungerande diagrammet.
* **Tidslinje**: kan du ändra din fungerande tidsintervall.
* **Appfältet**: innehåller dina alternativ för användare, till exempel aktuell klient, och du kan ändra tema och språkinställningar.

## <a name="time-series-insights-preview-environment-panel"></a>Time Series Insights Preview miljö panelen

Miljö-panelen visar alla Time Series Insights-miljöer som du har åtkomst till. Listan innehåller användningsbaserad miljöer förhandsversionen och S1/S2-miljöer (GA). Klicka bara på Time Series Insights-miljö som du vill använda.

  ![Explorer två][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Time Series Insights Preview navigeringsmenyn

  ![3-Explorer][3]

Du kan växla mellan Time Series Insights-appar med navigeringsmenyn:

* **Analysera**: låter dig skapa diagram över och utföra omfattande analysfunktioner på din modellerade eller unmodeled time series-data.

* **Modellen**: låter dig skicka ny Time Series Insights Preview-typer, hierarkier och instanser till din Time Series Insights-modell.

## <a name="time-series-insights-preview-model-authoring"></a>Time Series Insights Preview modellredigering

Med den här appen kan du utföra åtgärder för skapa, läsa, uppdatera och ta bort (CRUD) på din Time Series-modell.  

* **Time Series-modell typ**: Time Series Insights typer kan definiera variabler eller formler för att göra beräkningar. De är associerade med en viss Time Series Insights-instans. En typ kan ha en eller flera variabler.
* **Time Series-modell hierarkin**: hierarkier är systematisk organisationer i dina data. Hierarkier visas relationerna mellan olika enheter i din Time Series Insights-data.
* **Time Series-modell instans**: instanser är tidsserier själva. I de flesta fall är de DeviceID eller AssetID, vilket är den unika identifieraren för tillgången i miljön.

Läs mer om Time Series-modell i [gånger serien modeller](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Sökruta för Time Series Insights Preview-modell

Sökruta modell kan du enkelt söka och navigera hierarkin Time Series-modell för att hitta specifika tid-series-instanser som du vill visa i diagrammet. När du väljer dina instanser, läggs de till både det aktuella diagrammet och den också.

  ![Explorer-fyra][4]

## <a name="time-series-insights-preview-well"></a>Time Series Insights Preview bra

Prognostiseringen visar instansfält och andra metadata som är associerade med valda time series-instanser. Markera kryssrutorna till höger kan du dölja eller visa specifika instanser från det aktuella diagrammet. Du kan också ta bort specifika dataelement från dina aktuella data också genom att klicka på kontrollen rött x till höger om elementet.

  ![Explorer fem][5]

Du kan också Fäll ut panelen telemetri för att få en bättre lodrät vy av element i dina data även.

  ![Explorer-sex][6]

> [!NOTE]
> Om du ser följande meddelande har inte några data under det tidsintervall som valts i instansen. Du kan öka tidsintervallet eller bekräfta att instansen är push-överföra data för att lösa problemet.
>
> ![Explorer-sju][7]

## <a name="time-series-insights-preview-chart"></a>Time Series Insights förhandsgranskningsdiagrammet

Diagrammet, kan du visa time series-instanser som rader. Du kan dölja miljö panelen, datamodellen och time span på Kontrollpanelen genom att klicka på webbkontroller att större.

  ![Explorer-8][8]

1. **Valda datumintervallet**: kontroller vilka dataelement är tillgängliga för visualisering.

1. **Inre datum intervallet skjutreglaget verktyget**: använda två endpoint-kontroller genom att dra dem under den önskade tiden.

1. **Time span Dölj kontrollen**: komprimerar och expanderar time span panelen redigeraren.

1. **Y-axeln Formatera kontroll**: växlar mellan de tillgängliga alternativen för y-axeln vy:

    * `Default`: Varje rad har en enskild y-axeln.
    * `Stacked`: Om du vill stapla flera rader på samma y-axeln, med det y-axeln data som ändras baserat på den rad som valts.
    * `Shared`: Alla y-axeln data som visas tillsammans.

1. **Aktuella dataelement**: dataelementet för valda och dess tillhörande information.

Du ytterligare detaljer i en specifik datasektor genom vänsterklicka en datapunkt i det aktuella diagrammet och sedan dra det markerade området till slutpunkten för ditt val. Högerklicka på det grå, valda området och klickar på zoomning som visas i den här följande bild:

  ![Explorer-nio][9]

När du utför åtgärden zoomning, visas den valda datamängden. Klicka på y-axeln format kontrollen om du vill gå igenom de tre y-axeln framställningar av dina Time Series Insights-data.

  ![Explorer-tio][10]

Här kan du se ett exempel på delade y-axlar:

  ![Explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Panelen för Time Series Insights Preview tid redigeraren

När du arbetar med Time Series Insights Preview måste välja du först ett tidsintervall. Det valda tidsintervallet styr den datauppsättning som är tillgängliga för manipulering med förhandsversionen av Time Series Insights-widgetar. Följande webbkontroller är tillgängliga i förhandsversionen av Time Series Insights för att välja din fungerande tidsintervall.

  ![Explorer-tolv][12]

1. **Inre datumintervall skjutreglaget verktyget**: använda två endpoint-kontroller genom att dra dem under den önskade tiden. Den här inre datumintervall begränsas av yttre datumintervall skjutreglage.

1. **Öka och minska datum intervallet knappar**: öka eller minska din tid sträcker sig över genom att välja någon av knapparna för intervallet.

1. **Time span Dölj kontrollen**: här kontrollen kan du dölja alla kontroller utom verktyget inre datumintervall skjutreglaget.

1. **Skjutreglage för datumintervall för yttre**: Använd endpoint-kontroller för att välja det yttre-datumintervallet, som är tillgängliga för din inre datumintervall kontroll.

1. **Snabbtider datum intervallet listrutan**: gör att du snabbt växla mellan förinställda time span val, till exempel de senaste 30 minuterna, de senaste 12 timmarna eller ett anpassat intervall. Ändra det här värdet ändras också de tillgängliga intervall intervall som beskrivs i verktyget intervallstorlek skjutreglaget.

1. **Intervallstorlek skjutreglaget**: kan du zooma in och ut ur intervall via samma angivna tidsintervallet. Den här åtgärden ger mer exakt kontroll av flytt mellan stora sektorer tid. Smidig trender ned till segment som är så litet som en millisekund, så att du kan se detaljerad, högupplösta rabatter för dina data visas. Skjutreglagets standard startpunkt har angetts som den mest optimala vy av data från ditt val som balanserar upplösning, fråga hastighet och granularitet.

1. **Datumintervall till och från webbkontrollen**: här kontrollen kan du enkelt klicka och välj din önskade datum- och tidsintervall. Du kan också använda kontrollen för att växla mellan olika tidszoner. När du har gjort ändringarna ska tillämpas på din aktuella arbetsyta väljer **spara**.

  ![Explorer-13][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Time Series Insights Preview navigeringsfönster

Navigeringsfönster förhandsversionen av Time Series Insights tillhandahåller följande funktioner:

  ![Explorer-14][14]

### <a name="current-session-share-link-control"></a>Aktuella dela länken sessionskontroll

  ![Explorer-femton][15]

Välj länken webbkontrollen (markerat) för att generera en URL för att spara eller dela din Azure Time Series Insights aktiv session, vilket innefattar:

* Valda tidsintervallet
* Valda intervallstorlek
* Markerade data också

### <a name="tenant-section"></a>Klient-avsnitt

  ![Explorer-sexton][16]

* Visar din aktuella kontoinformation för Time Series Insights-inloggning.
* Du kan välja mellan de tillgängliga Temana för Time Series Insights.

### <a name="theme-selection"></a>Temaval

Azure Time Series Insights Preview stöder två teman:

* **Ljust tema**: standardtemat som visas i hela dokumentet.
* **Mörkt tema**: återges i Utforskaren som visas här:

  ![Explorer-seventeen][17]

Här kan du också ändra mellan språk som stöds.

## <a name="s1s2-environment-controls"></a>S1/S2 Miljökontroller

### <a name="time-series-insights-preview-terms-panel"></a>Time Series Insights Preview termpanel

Det här avsnittet gäller enbart för befintliga S1/S2-miljöer som försöker använda Utforskaren i uppdaterade Användargränssnittet. Du kanske vill använda GA produkt och Preview i kombination. Vi har lagt till vissa funktioner i befintliga användargränssnittet till den uppdaterade explorer, men du kan hämta den fullständiga UI-upplevelsen för S1/S2-miljö i befintlig Time Series Insights explorer.  

I stället för hierarkin visas panelen Time Series Insights villkoren som definierar frågor i din miljö. Du kan filtrera dina data baserat på ett predikat.

  ![Explorer-upp till arton][18]

Time Series Insights Preview redigeraren termpanelen använder följande parametrar:

**Där**: where sats kan du snabbt filtrera händelser med hjälp av uppsättningen operander visas i följande tabell. Om du gör en sökning genom att välja en operand uppdateras predikatet automatiskt utifrån sökningen. Operandtyperna som stöds är:

| Åtgärd | Typer som stöds   | Anteckningar |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Sträng, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Sträng, Bool, Double, DateTime, TimeSpan, NULL | Alla operander bör vara av samma typ eller vara konstanten NULL. |
| `HAS` | Sträng | Endast konstant stränglitteraler tillåts på höger sida. Tom sträng och NULL är inte tillåtna. |

Läs mer om stöds frågetyper för drift och genom att läsa [Time Series uttryck (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exempel på var satser

  ![Explorer-nitton][19]

**Måttet**: den här listan visar alla numeriska kolumner (**Double-värden**) som du kan använda som element för din aktuella diagrammet.

**Dela efter**: den här listan visar alla tillgängliga kategoriska kolumner (strängar) i din modell som du kan gruppera dina data genom. Du kan lägga till upp till fem villkoren för att visa på samma x-axeln. Ange önskade parametrar och välj sedan **Lägg till** att lägga till ett nytt villkor.

  ![Explorer-20][20]

Du kan visa och dölja element i panelen diagram genom att välja ikonen visas enligt följande bild. Du kan helt ta bort frågor genom att klicka på röda **x**.

  ![Explorer-20-en][21]

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:
* [Azure Time Series Insights Preview storage och ingående](./time-series-insights-update-storage-ingress.md)
* [Datamodellering](./time-series-insights-update-tsm.md)
* [Diagnostisera och felsöka](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
