---
title: Tids serie avvikelse identifiering & prognoser i Azure Datautforskaren
description: Lär dig hur du analyserar tids serie data för avvikelse identifiering och prognoser med hjälp av Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194165"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Avvikelse identifiering och prognoser i Azure Datautforskaren

Azure Datautforskaren utför pågående insamling av telemetridata från moln tjänster eller IoT-enheter. Dessa data analyseras för olika insikter, till exempel övervakning av tjänstens hälsa, fysiska produktions processer, användnings trender och belastnings prognos. Analysen görs i tids serier med valda mått för att hitta ett avvikelse mönster för måttet i förhållande till dess typiska normala bas linje mönster. Azure Datautforskaren innehåller inbyggt stöd för att skapa, manipulera och analysera flera tids serier. Det kan skapa och analysera tusentals tids serier på några sekunder, vilket möjliggör övervakning av lösningar och arbets flöden i nära real tid.

Den här artikeln beskriver funktioner för identifiering och prognostisering av Azure Datautforskaren Time Series-avvikelser. Tillämpliga Time Series-funktioner baseras på en robust välkänd nedbrytnings modell där varje ursprunglig tids serie fördelas i säsongs-, trend-och rest-komponenter. Avvikelser upptäcks genom avvikande Mät värden på resten av komponenten, medan prognostisering görs genom att extrapolera säsongs-och trend komponenterna. Azure Datautforskaren-implementeringen förbättrar avsevärt den grundläggande diskompositions modellen genom automatisk identifiering av säsongs beroende, robust avvikare-analys och Vector-implementering för att bearbeta tusentals tids serier på några sekunder.

## <a name="prerequisites"></a>Förutsättningar

Läsning av [tids serie analys i Azure datautforskaren](/azure/data-explorer/time-series-analysis) en översikt över Time Series-funktioner.

## <a name="time-series-decomposition-model"></a>Modell för tids serie nedbrytning

Azure Datautforskaren inbyggd implementering för tids serie förutsägelse och avvikelse identifiering använder en välkänd nedbrytnings modell. Den här modellen används i tids serier med mått som förväntas ha manifestet för periodisk och trend, till exempel tjänst trafik, komponent pulsslag och IoT periodiska mätningar för att beräkna framtida mått värden och identifiera avvikande värden. Antagandet av denna Regressions process är att det andra än det tidigare kända säsongs-och trend beteendet är att tids serien distribueras slumpmässigt. Du kan sedan beräkna framtida mått värden från säsongs-och trend komponenterna, gemensamt namngivna bas linjen och ignorera resten av delen. Du kan också identifiera avvikande värden baserat på avvikare analys med enbart resten av delen.
Om du vill skapa en diskompositions modell använder du funktionen [`series_decompose()`](/azure/kusto/query/series-decomposefunction). Funktionen `series_decompose()` tar en uppsättning tids serier och avbildar automatiskt varje tids serie till dess säsongs-, trend-, rest-och bas linje komponenter. 

Du kan till exempel dela upp trafiken för en intern webb tjänst genom att använda följande fråga:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

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

![Tids serie sammansättning](media/anomaly-detection/series-decompose-timechart.png)

* Den ursprungliga tids serien är märkt med **NUM** (i rött). 
* Processen börjar med automatisk identifiering av säsongs beroende med hjälp av funktionen [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) och extraherar **säsongs** mönstret (i lila).
* Säsongs mönstret subtraheras från den ursprungliga tids serien och en linjär regression körs med hjälp av funktionen [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) för att hitta **trend** komponenten (i ljus blått).
* Funktionen subtraherar trenden och resten är den **kvarvarande** komponenten (i grönt).
* Slutligen lägger funktionen till säsongs-och trend komponenterna för att generera **bas linjen** (i blått).

## <a name="time-series-anomaly-detection"></a>Avvikelse identifiering i Time Series

Funktionen [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) hittar avvikande punkter i en uppsättning tids serier. Den här funktionen anropar `series_decompose()` för att bygga modellen för nedbrytning och sedan köra [`series_outliers()`](/azure/kusto/query/series-outliersfunction) på resten av komponenten. `series_outliers()` beräknar avvikelser för varje punkt i resten av komponenten med Tukey: s stängsel-test. Avvikelse resultat över 1,5 eller under-1,5 indikerar en viss avvikelse ökning eller Avböj. Avvikelser i resultatet över 3,0 eller under-3,0 indikerar en stark avvikelse. 

Med följande fråga kan du identifiera avvikelser i intern webb tjänst trafik:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

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

![Avvikelse identifiering i Time Series](media/anomaly-detection/series-anomaly-detection.png)

* Den ursprungliga tids serien (i rött). 
* Bas linje komponenten (säsongs + trend) (i blått).
* Avvikande punkter (i lila) ovanpå den ursprungliga tids serien. Avvikande punkter avviker kraftigt från förväntade bas linje värden.

## <a name="time-series-forecasting"></a>Prognosticering för tids serier

Funktionen [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) förutsäger framtida värden för en uppsättning tids serier. Den här funktionen anropar `series_decompose()` för att bygga diskompositions modellen och för varje tids serie, extrapolerar bas linje komponenten i framtiden.

Med följande fråga kan du förutsäga nästa veckas webb tjänst trafik:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Prognosticering för tids serier](media/anomaly-detection/series-forecasting.png)

* Ursprungligt mått (i rött). Framtida värden saknas och anges till 0 som standard.
* Extrapolera bas linje komponenten (i blått) för att förutsäga nästa veckas värden.

## <a name="scalability"></a>Skalbarhet

Med språksyntaxen för Azure Datautforskaren Query kan ett enda anrop bearbeta flera tids serier. Den unika optimerade implementeringen ger snabb prestanda, som är kritisk för effektiv identifiering och prognoser vid övervakning av tusentals räknare i nära real tids scenarier.

Följande fråga visar bearbetningen av tre tids serier samtidigt:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

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

![Skalbarhet för Time Series](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Sammanfattning

Det här dokumentet innehåller inbyggda Azure Datautforskaren-funktioner för tids serie avvikelse identifiering och prognoser. Varje ursprunglig tids serie delas upp i säsongs-, trend-och rest-komponenter för att identifiera avvikelser och/eller prognoser. Dessa funktioner kan användas för scenarier i nära real tids övervakning, till exempel fel identifiering, förutsägelse underhåll och belastnings prognoser.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Machine Learning-funktioner](/azure/data-explorer/machine-learning-clustering) i Azure datautforskaren.
