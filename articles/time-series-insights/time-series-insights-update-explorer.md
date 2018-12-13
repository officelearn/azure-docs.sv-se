---
title: Visualisera data i Azure Time Series Insights explorer uppdatering | Microsoft Docs
description: Uppdatering av Azure Time Series Insights explorer
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 48f3be2de2f23cef311994c603d50544b5d13a6e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879799"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualisera data i explorer (förhandsversion)

Den här artikeln beskrivs funktioner och alternativ som är tillgängliga i den Azure Time Series Insights (förhandsversion) [explorer webbprogram](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Förutsättningar

Innan du använder Utforskaren för Azure Time Series Insights (förhandsversion), måste du:

* Har en Time Series Insights-miljö som har etablerats. Lär dig mer om att etablera en här Time Series Insights-miljö.
* Tillhandahålla dataåtkomst i Time Series Insights-miljön som du skapade för kontot. Åtkomst kan anges till andra samt själv.
* Lägga till en händelsekälla till Time Series Insights-miljö för att skicka data till miljön.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Lär dig mer om Azure Time Series Insights (förhandsversion)-explorer

  ![Explorer-ett][1]

Azure Time Series Insights (förhandsversion) explorer har delats upp i följande sju element:

1. Navigeringsfältet i TSD (förhandsversion) kan du växla mellan sidor för analys och modellen.
1. TSI (förhandsversion) hierarkiträdet Välj specifika dataelement för diagrammet.
1. TSI (förhandsversion) tidsserier visar även alla markerade dataelement.
1. TSI (förhandsversion) diagrammet panelen visar aktuella fungerande diagrammet.
1. Tidslinje för TSI (förhandsversion) kan du ändra din fungerande tidsintervall.
1. TSI (förhandsversion) appfältet innehåller din alternativen för användarhantering (till exempel aktuell klient) och kan du ändra tema och språkinställningar.

## <a name="tsi-preview-environment-panel"></a>TSI (förhandsversion) miljö panelen

Miljö-panelen visar alla TSI-miljöer som du har åtkomst till. Detta omfattar en lista över användningsbaserad miljöer (förhandsversion) samt S1/S2-miljöer (GA). Klicka bara på TSI-miljö som du vill använda.

  ![Explorer två][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Navigeringsmenyn för Time Series Insights (förhandsversion)

  ![3-Explorer][3]

På navigeringsmenyn kan du växla mellan TSI-program:

* Analysera – kan du skapa diagram över & utföra omfattande analysfunktioner på din modellerade eller unmodeled time series-data.

* Modell - låter dig nya TSI-uppdateringstyperna, hierarkier och instanser i TSI-modellen.

## <a name="time-series-insights-update-model-authoring"></a>Time Series Insights uppdatera redigeringen av modellen

Det här programmet ger dig möjligheten att utföra CRUD-åtgärder på din Time Series-modell.  

* Typ av TSM – TSI-typer kan definiera variabler eller formler för att göra beräkningar, de är associerade med en viss TSI-instans. En typ kan ha en eller flera variabler.
* TSM hierarki - hierarkier är systematisk organisationer i dina data. Hierarkier visas relationerna mellan olika enheter i TSI-data.
* TSM instans - instanser är tidsserier själva. I de flesta fall är det här är den **DeviceID** eller **AssetID**, vilket är den unika identifieraren för tillgången i miljön.

Mer information om TSM [gånger serien modeller](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Sökruta för Time Series Insights (förhandsversion) modell

Sökruta modell kan du enkelt söka och navigera i hierarkin TSM för att hitta specifika tid-series-instanser som du vill visa i diagrammet. När du väljer dina instanser, läggs inte bara till det aktuella diagrammet men läggs också bra till data.

  ![Explorer-fyra][4]

## <a name="time-series-insights-preview-well"></a>Time Series Insights (förhandsversion) och

Prognostiseringen visar instansfält och andra metadata som är associerade med valda time series-instanser. Kryssrutorna till höger kan du dölja eller visa specifika instanser från det aktuella diagrammet. Du kan också ta bort specifika dataelement från dina aktuella data också genom att klicka på kontrollen rött x till höger om elementet.

  ![Explorer fem][5]

Du kan också Fäll ut panelen telemetri för att få en bättre lodrät vy av element i dina data även.

  ![Explorer-sex][6]

> [!NOTE]
> Om du ser följande ikon har inte några data under det tidsintervall som valts i instansen.
> Om du vill åtgärda, du kan öka det tidsintervall som valts och/eller bekräfta att instansen är att skicka data.

  ![Explorer-sju][7]

## <a name="time-series-insights-preview-chart"></a>Diagram för Time Series Insights (förhandsversion)

Diagrammet kan du visa time series-instanser som rader. Du kan dölja miljö panelen, datamodellen och time span på Kontrollpanelen genom att klicka på webbkontroller att större.

  ![Explorer-8][8]

1. **Valda datumintervallet** – den markerade datumintervall för panelen diagram, den här kontroller vilka dataelement blir tillgängliga för visualisering.

1. **Inre datum intervallet skjutreglaget verktyget** – Använd två endpoint-kontroller genom att klicka och dra dem över önskad tid sträcker sig över.

1. **Time span Dölj kontrollen** – den här kontrollen döljer och expanderar time span panelen redigeraren.

1. **Y-axeln Formatera kontroll** – Klicka på den här kontrollen för att gå igenom de tillgängliga alternativen för y-axeln vy. Alternativen för y-axeln vyn är:

    * `Default`  -varje rad har en enskild y-axeln
    * `Stacked` – Du kan använda flera rader på samma y-axeln, med y-axeln data som ändras baserat på den rad som valts
    * `Shared` – Alla y-axeln data som visas tillsammans

1. **Aktuella dataelement** – den markerade dataelementet och dess tillhörande information.

Du ytterligare detaljer i en specifik datasektor av vänsterklicka en datapunkt i det aktuella diagrammet samtidigt som du håller ned musknappen och dra sedan det markerade området till slutpunkten för ditt val. Högerklicka på det grå, valda området och klicka på zoomning som visas nedan. Du kan också:

  ![Explorer-nio][9]

När du utför åtgärden zoomning, kommer du nu se valda datauppsättningen. Klicka på y-axeln Formatera kontroll att växla mellan tre olika y-axeln representationer av TSI-data.

  ![Explorer-tio][10]

Här kan du se ett exempel på en delad y-axlar.

  ![Explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Time Series Insights (förhandsversion) tid redigeraren panelen

När du arbetar med TSI måste väljer du först ett tidsintervall. Det valda tidsintervallet ska styra den datauppsättning som är tillgängliga för manipulering med TSI update widgetar. Följande webbkontroller är tillgängliga i TSI-uppdateringen för att välja din fungerande tidsintervall.

  ![Explorer-tolv][12]

1. **Inre datumintervall skjutreglaget verktyget** – Använd två endpoint-kontroller genom att klicka och dra dem över önskad tid sträcker sig över. Den här ”inre” datumintervall ska vara begränsade av skjutreglage för ”yttre datum” intervall som anges nedan.

1. Öka och minska datum intervallet knappar ** - öka eller minska din tidsintervall genom att klicka på någon av knapparna för intervallet.

1. **Time span Dölj kontrollen** -här kontrollen kan du dölja alla kontroller utom verktyget inre datum intervallet skjutreglaget.

1. **Skjutreglage för datumintervall för yttre** -med slutpunkt-kontroller välja det yttre datumintervallet som är tillgängliga för din ”inre” intervallet kontroll.

1. **Snabbgånger datum intervallet listrutan** -ger dig möjlighet att snabbt växla mellan förinställda time span alternativ, såsom de senaste 30 minuterna, senaste 12 timmarna, anpassat intervall, osv. Ändra det här värdet ändras också de tillgängliga intervall intervall som beskrivs i verktyget intervall storlek skjutreglaget bredvid.

1. **Intervallstorlek skjutreglaget** -intervall storlek skjutreglaget verktyget gör det möjligt att zooma in och ut ur intervall över samma tidsintervall. Detta ger mer exakt kontroll över flödet mellan stora sektorer tid som visar smooth trender ned till segment som är så litet som Millisekunden, så att du kan se detaljerad, högupplösta rabatter för dina data. Skjutreglagets standard startpunkt som har angetts som den mest optimala vy över data från markeringen. belastningsutjämning upplösning, fråga hastighet och granularitet.

1. **Datumintervall till och från webbkontrollen** – den här kontrollen kan du enkelt klickar du på och välj din önskade datum- och tidsintervall. Du kan också använda kontrollen för att växla mellan olika tidszoner. När du har gjort ändringarna du vill tillämpa på din aktuella arbetsutrymme klickar du på knappen.

  ![Explorer-13][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Navigeringsfönster för Time Series Insights (förhandsversion)

Navigeringsfönstret TSI uppdatering innehåller följande funktioner:

  ![Explorer-14][14]

### <a name="current-session-share-link-control"></a>Aktuella dela länken sessionskontroll

  ![Explorer-femton][15]

Klicka på webbkontrollen för inringad länk för att generera en URL för att spara eller dela din Azure TSI aktiv session, vilket innefattar:

* Valda tidsintervallet
* Valda intervallstorlek
* Markerade data också

### <a name="tenant-section"></a>Klient-avsnitt

  ![Explorer-sexton][16]

* Visar din aktuella kontoinformation för TSI-inloggning
* Kan du växla mellan de tillgängliga Temana för TSI.

### <a name="theme-selection"></a>Temaval

Azure TSI (förhandsversion) stöder två teman:

* **Ljust tema**: Detta är det standardtema som visas i hela dokumentet.
* **Mörkt tema**: det här alternativet visas i Utforskaren i ett mörkt tema som visas nedan:

  ![Explorer-seventeen][17]

Här kan du också ändra mellan språk som stöds.

## <a name="s1s2-environment-controls"></a>S1/S2 Miljökontroller

### <a name="time-series-insights-preview-terms-panel"></a>Termpanel för Time Series Insights (förhandsversion)

Det här avsnittet gäller bara för befintliga S1/S2-miljöer som försöker använda Utforskaren i uppdaterade Användargränssnittet. Du kanske vill göra detta för att använda GA-produkten och uppdatera (förhandsversion) tillsammans med varandra. Vi har lagt till vissa funktioner i befintliga användargränssnittet till den uppdaterade explorer, men vet du kan alltid hämta den fullständiga UI-upplevelsen för S1/S2-miljö i befintliga TSI-Utforskaren.  

I stället för hierarkin visas panelen TSI villkor. Det är här du definierar frågor i din miljö. Det ger dig möjlighet att filtrera dina data baserat med hjälp av ett predikat.

  ![Explorer-upp till arton][18]

Termpanelen redigeraren TSI (förhandsversion) använder följande parametrar

**Där**: where sats kan du snabbt filtrera händelser med hjälp av uppsättningen operander som anges nedan. Om du gör en sökning genom att välja/att klicka på predikat som automatiskt uppdateras baserat på sökningen. Operandtyperna som stöds är:

| Åtgärd | Typer som stöds   | Anteckningar |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` |    Double, DateTime, TimeSpan  | |
| `=`, `!=`, `<>`   | Sträng, Bool, Double, DateTime, TimeSpan, NULL    |
| `IN` |    Sträng, Bool, Double, DateTime, TimeSpan, NULL |    Alla operander bör vara av samma typ eller vara konstanten NULL. |
| `HAS` |   Sträng |    Endast konstant stränglitteraler tillåts på höger sida. Tom sträng och NULL är inte tillåtna. |

### <a name="examples-of-where-clauses"></a>Exempel på var satser

  ![Explorer-nitton][19]

**Måttet**: detta nedrullningsbar listruta visar alla numeriska kolumner (**Double-värden**) som du kan använda som element för din aktuella diagrammet.

**Dela efter**: den här nedrullningsbara menyn visar alla kategoriska kolumner (strängar) i din modell tillgänglig, som du kan använda för att gruppera dina data genom. Du kan lägga till upp till fem villkoren för att visa på samma x-axeln. Ange önskade parametrar och sedan använda den **Lägg till** knappen för att lägga till ett nytt villkor.

  ![Explorer-20][20]

Du kan dölja och visa element från panelen diagram genom att klicka på ikonen visas enligt nedan. Du kan helt ta bort frågor genom att klicka på röda `X` visas nedan.

  ![Explorer-20-en][21]

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och ingående](./time-series-insights-update-storage-ingress.md).

Läs mer om [datamodellering](./time-series-insights-update-tsm.md).

Lär dig mer om [diagnostisera och felsöka](./time-series-insights-update-how-to-troubleshoot.md).

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