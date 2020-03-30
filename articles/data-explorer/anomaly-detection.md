---
title: Avvikelseidentifiering av tidsserier & prognoser i Azure Data Explorer
description: Lär dig hur du analyserar tidsseriedata för avvikelseidentifiering och prognoser med Hjälp av Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194165"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Identifiering och prognostisering av avvikelser i Azure Data Explorer

Azure Data Explorer utför pågående insamling av telemetridata från molntjänster eller IoT-enheter. Dessa data analyseras för olika insikter som övervakning av tjänstens hälsa, fysiska produktionsprocesser, användningstrender och belastningsprognos. Analysen görs i tidsserier med valda mått för att hitta ett avvikelsemönster för måttet i förhållande till dess typiska normala baslinjemönster. Azure Data Explorer innehåller inbyggt stöd för skapande, manipulering och analys av flera tidsserier. Det kan skapa och analysera tusentals tidsserier på några sekunder, vilket möjliggör nära realtid övervakningslösningar och arbetsflöden.

I den här artikeln beskrivs funktionerna för avvikelseidentifiering och prognostisering av Azure Data Explorer. De tillämpliga tidsseriefunktionerna baseras på en robust välkänd nedbrytningsmodell, där varje ursprunglig tidsserie bryts ned till säsongsbetonade, trendmässiga och restkomponenter. Avvikelser upptäcks av extremvärden på restkomponenten, medan prognoser görs genom att extrapolera säsongs- och trendkomponenterna. Azure Data Explorer-implementeringen förbättrar avsevärt den grundläggande nedbrytningsmodellen genom automatisk säsongsidentifiering, robust avvikelseanalys och vektoriserad implementering för att bearbeta tusentals tidsserier på några sekunder.

## <a name="prerequisites"></a>Krav

Läs [tidsserieanalys i Azure Data Explorer](/azure/data-explorer/time-series-analysis) för en översikt över tidsseriefunktioner.

## <a name="time-series-decomposition-model"></a>Nedbrytningsmodell för tidsserier

Azure Data Explorer native implementation for time series prediction and anomaly detection uses a well-known de nedbrytning model. Den här modellen tillämpas på tidsserier med mått som förväntas manifestera periodiska och trendmässiga beteenden, till exempel servicetrafik, komponentslagslag och IoT-periodiska mätningar för att prognostisera framtida måttvärden och identifiera avvikande värden. Antagandet av denna regressionsprocess är att andra än det tidigare kända säsongs- och trendbeteendet är tidsserierna slumpmässigt fördelade. Du kan sedan prognostisera framtida måttvärden från säsongs- och trendkomponenter, kollektivt namngivna originalplan, och ignorera den återstående delen. Du kan också identifiera avvikande värden baserat på avvikare analys med endast restdelen.
Om du vill skapa en sönderdelningsmodell använder du funktionen [`series_decompose()`](/azure/kusto/query/series-decomposefunction). Funktionen `series_decompose()` tar en uppsättning tidsserier och bryts automatiskt ned varje tidsserie till sina säsongs-, trend-, rest- och baslinjekomponenter. 

Du kan till exempel bryta ned trafiken för en intern webbtjänst med hjälp av följande fråga:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

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

* Den ursprungliga tidsserien är märkt **num** (i rött). 
* Processen börjar med automatisk detektering av [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) säsongsvariationer med hjälp av funktionen och extraherar **säsongsmönstret** (i lila).
* Säsongsmönstret subtraheras från den ursprungliga tidsserien och en linjär [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) regression körs med hjälp av funktionen för att hitta **trendkomponenten** (i ljusblått).
* Funktionen subtraherar trenden och resten är **restkomponenten** (i grönt).
* Slutligen lägger funktionen till säsongs- och trendkomponenter för att generera **baslinjen** (i blått).

## <a name="time-series-anomaly-detection"></a>Avvikelseidentifiering av tidsserier

Funktionen [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) hittar avvikande punkter på en uppsättning tidsserier. Den här `series_decompose()` funktionen anropar för att [`series_outliers()`](/azure/kusto/query/series-outliersfunction) bygga nedbrytningsmodellen och körs sedan på restkomponenten. `series_outliers()`beräknar avvikelsepoäng för varje punkt på restkomponenten med Tukeys stakettest. Avvikelsepoäng över 1,5 eller lägre -1,5 indikerar en mild avvikelse ökning eller nedgång respektive. Avvikelsepoäng över 3,0 eller under -3,0 indikerar en stark anomali. 

Med följande fråga kan du identifiera avvikelser i intern webbtjänsttrafik:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

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

![Avvikelseidentifiering av tidsserier](media/anomaly-detection/series-anomaly-detection.png)

* Den ursprungliga tidsserien (i rött). 
* Baslinjekomponenten (säsong + trend) (i blått).
* De avvikande punkterna (i lila) ovanpå den ursprungliga tidsserien. De avvikande punkterna avviker avsevärt från de förväntade baslinjevärdena.

## <a name="time-series-forecasting"></a>Prognostisering av tidsserier

Funktionen [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) förutsäger framtida värden för en uppsättning tidsserier. Den här `series_decompose()` funktionen anropar för att bygga nedbrytningsmodellen och extrapolerar sedan baslinjekomponenten i framtiden för varje tidsserie.

Med följande fråga kan du förutsäga nästa veckas webbtjänsttrafik:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

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

![Prognostisering av tidsserier](media/anomaly-detection/series-forecasting.png)

* Originalmått (i rött). Framtida värden saknas och anges som standard 0.
* Extrapolera baslinjekomponenten (i blått) för att förutsäga nästa veckas värden.

## <a name="scalability"></a>Skalbarhet

Azure Data Explorer frågespråk syntax gör det möjligt för ett enda anrop att bearbeta flera tidsserier. Dess unika optimerade implementering möjliggör snabb prestanda, vilket är avgörande för effektiv avvikelseidentifiering och prognoser vid övervakning av tusentals räknare i nästan realtidsscenarier.

Följande fråga visar bearbetningen av tre tidsserier samtidigt:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

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

![Skalbarhet för tidsserier](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Sammanfattning

Det här dokumentet innehåller inbyggda Azure Data Explorer-funktioner för avvikelseidentifiering och prognostisering av tidsserier. Varje ursprunglig tidsserie bryts ned till säsongs-, trend- och restkomponenter för att upptäcka avvikelser och/eller prognoser. Dessa funktioner kan användas för övervakningsscenarier i nära realtid, till exempel felidentifiering, prediktivt underhåll och efterfråge- och belastningsprognoser.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [machine learning-funktioner](/azure/data-explorer/machine-learning-clustering) i Azure Data Explorer.
