---
title: Time series-avvikelseidentifiering och prognostisering i Datautforskaren i Azure
description: Lär dig hur du analyserar time series-data för identifiering av avvikelser och prognostisering med Datautforskaren i Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: f40350129a12c7865051bcae80b74b6f9c069179
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233540"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Identifiering av avvikelser och prognostisering i Datautforskaren i Azure

Azure Data Explorer utför pågående insamling av telemetridata från molntjänster eller IoT-enheter. Dessa data analyseras för olika insikter, till exempel övervakning av tjänstehälsa, fysiska produktionsprocesser, användningstrender och load prognosen. Analysen utförs på tidsserier med valda mått för att hitta en avvikelse mönstret för mått i förhållande till dess vanliga normala baslinje-mönster. Azure Data Explorer innehåller inbyggt stöd för skapandet, bearbetning och analys av flera tidsserier. Det kan skapa och analysera tusentals tidsserier i sekunder, att aktivera nära realtid övervakning lösningar och arbetsflöden.

Den här artikeln beskriver Datautforskaren i Azure time series avvikelseidentifiering identifiering och prognostisering funktionerna. De tillämpliga tidsfunktionerna serien baseras på en robust välkända uppdelning modell, där varje ursprungliga tidsserier delas upp i säsongens, trend, och komponenter. Avvikelser upptäcks av extremvärden av återstående komponenten, medan prognostisering görs genom att extrapolera på säsongens och trend komponenter. Datautforskaren i Azure-implementering avsevärt förbättrar modellen grundläggande uppdelning av automatisk säsongsberoende identifiering, robust avvikare analys och vectorized implementering till processen tusentals tidsserier i sekunder.

## <a name="prerequisites"></a>Nödvändiga komponenter

Läs [Time series analys i Azure Data Explorer](/azure/data-explorer/time-series-analysis) för en översikt över time series-funktioner.

## <a name="time-series-decomposition-model"></a>Uppdelning av tidsseriemodell

Azure Data Explorer ursprunglig implementering för tidsserieförutsägelse och avvikelseidentifiering använder en modell för välkända uppdelning. Den här modellen används tidsserier mått som förväntas manifest periodiska och trend fungerar, till exempel tjänsttrafiken, komponenten pulsslag och IoT periodiska mått att göra prognoser för framtida måttvärden och identifiera avvikande värden. Regression processen antas som än den tidigare och okänt säsongens och trend beteende, time series distribueras slumpmässigt. Du kan göra prognoser om framtida måttvärden från på säsongens och trend komponenter, tillsammans med namnet baslinjen, och ignorerar den resterande delen. Du kan också identifiera avvikande värden baserat på avvikare analys med endast den resterande delen.
Använd funktionen för att skapa en uppdelning modell, [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). Den `series_decompose()` funktionen tar en uppsättning tidsserier och automatiskt delar upp varje tidsserier till dess säsongens, trend, återstående och grundläggande komponenter. 

Du kan till exempel dela upp trafik för en intern webbtjänst med hjälp av följande fråga:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Tidsseriefördelning](media/anomaly-detection/series-decompose-timechart.png)

* Den ursprungliga tidsserien är märkt med **num** (i rött). 
* Processen startar med automatisk identifiering av säsongsvärdet med hjälp av funktionen [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) och extraherar den **säsongens** mönstret (i lila).
* Säsongsknutet mönster subtraheras från den ursprungliga tidsserien och en linjär regression körs med hjälp av funktionen [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) att hitta den **trend** komponenten (i ljusblått).
* Subtraherar funktionen trenden och resten är den **sekretessrisken** komponenten (i grönt).
* Slutligen funktionen lägger till på säsongens och trend komponenter för att generera den **baslinje** (i blått).

## <a name="time-series-anomaly-detection"></a>Time series-avvikelseidentifiering

Funktionen [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) hittar avvikande punkter på en uppsättning tidsserier. Den här funktionen anropar `series_decompose()` att skapa uppdelning modellen och sedan körs [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) på kvarvarande komponenten. `series_outliers()` beräknar avvikelseidentifiering poängen för varje punkt i komponenten resterande med Tukeys avgränsningstecken test. Avvikelseidentifiering poäng över 1.5 eller under-1.5 tyda på en ovårdat avvikelseidentifiering ökning eller neka respektive. Avvikelseidentifiering resultat över 3.0 eller under-3.0 anger en stark avvikelseidentifiering. 

Följande fråga kan du identifiera avvikelser i interna webbtrafik för tjänsten:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Time series-avvikelseidentifiering](media/anomaly-detection/series-anomaly-detection.png)

* Ursprungliga tidsserien (i rött). 
* Baslinjen (säsongens + trend) komponenten (i blått).
* Avvikande punkter (i lila) ovanpå de ursprungliga tidsserien. Avvikande punkter avviker markant från förväntade baslinjevärdena.

## <a name="time-series-forecasting"></a>Tidsserier prognoser

Funktionen [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) förutsäger framtida värden av en uppsättning tidsserier. Den här funktionen anropar `series_decompose()` att skapa uppdelning modeller och klickar sedan för varje tidsserie extrapolates baslinje-komponenten i framtiden.

Följande fråga kan du förutsäga nästa vecka webbtrafik för tjänsten:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Tidsserier prognoser](media/anomaly-detection/series-forecasting.png)

* Ursprungliga mått (i rött). Framtida värden saknas och inställd på 0, som standard.
* Extrapolera baslinje-komponenten (i blått) för att förutsäga värden för nästa vecka.

## <a name="scalability"></a>Skalbarhet

Azure Data Explorer språk frågesyntaxen kan ett enda anrop till bearbeta flera tidsserier. Dess unika optimerad implementering möjliggör snabba prestanda, vilket är viktigt för effektiv avvikelseidentifiering och prognostisering när du övervakar tusentals räknare i scenarier i nästan realtid.

Följande fråga visar att bearbetningen av tre tidsserier samtidigt:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Time series skalbarhet](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Sammanfattning

Det här dokumentet beskriver den ursprungliga Azure Data Explorer funktioner för time series-avvikelseidentifiering och prognostisering. Varje ursprungliga tidsserier delas upp i säsongens, trend och återstående komponenter för att kunna identifiera avvikelser och/eller prognostisering. Dessa funktioner kan användas för nästan i realtid övervakningsscenarier, till exempel felinsamling, förebyggande underhåll och begäran och läsa in prognostisering.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Machine learning-funktioner](/azure/data-explorer/machine-learning-clustering) i Datautforskaren i Azure.
