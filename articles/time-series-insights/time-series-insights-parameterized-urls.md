---
title: Dela anpassade vyer med parameteriserade webbadresser – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du skapar parameteriserade URL:er för att enkelt dela anpassade utforskarevyer i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 10616c8003d9bbbe42cb70bd1bac4193044907c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416996"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Dela en anpassad vy med en parametriserad URL

Om du vill dela en anpassad vy i Time Series Insights Explorer kan du programmässigt skapa en parameteriserad URL för den anpassade vyn.

Time Series Insights Explorer stöder URL-frågeparametrar för att ange vyer i upplevelsen direkt från URL:en. Med bara URL:en kan du till exempel ange en målmiljö, ett sökpredikat och önskat tidsintervall. När en användare väljer den anpassade URL:en tillhandahåller gränssnittet en länk direkt till den tillgången i time series insights-portalen. Dataåtkomstprinciper tillämpas.

> [!TIP]
> * Visa den kostnadsfria [demonstrationen För Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Läs dokumentationen för [Time Series Insights Explorer.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>Miljö-ID

Parametern `environmentId=<guid>` anger målmiljö-ID:t. Det är en komponent i dataåtkomst FQDN, och du kan hitta den i det övre högra hörnet av miljön översikt i Azure-portalen. Det är allt som `env.timeseries.azure.com`föregår.

Ett exempel på miljö-ID-parametern är `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tid

Du kan ange absoluta eller relativa tidsvärden med en parametriserad URL.

### <a name="absolute-time-values"></a>Absoluta tidsvärden

För absoluta tidsvärden använder du parametrarna `from=<integer>` och `to=<integer>`.

* `from=<integer>` är ett värde i JavaScript-millisekunder för starttiden för sökintervallet.
* `to=<integer>` är ett värde i JavaScript-millisekunder för sluttiden för sökintervallet.

> [!TIP]
> Om du enkelt vill översätta datum till JavaScript-millisekunder provar [du Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativa tidsvärden

För ett relativt tidsvärde använder du `relativeMillis=<value>`, där *värdet* finns i JavaScript-millisekunder från den senaste tidsstämpeln som tagits emot från API:et.

`&relativeMillis=3600000` visar till exempel data från de senaste 60 minuterna.

Godkända värden motsvarar **snabbtidsmenyn** för Time Series Insights explorer och inkluderar:

* `1800000`(Senaste 30 minuter)
* `3600000`(Senaste 60 minuter)
* `10800000`(Senaste 3 timmarna)
* `21600000`(Senaste 6 timmarna)
* `43200000`(Senaste 12 timmarna)
* `86400000`(Senaste 24 timmarna)
* `604800000`(Senaste 7 dagarna)
* `2592000000`(Senaste 30 timmarna)

### <a name="optional-parameters"></a>Valfria parametrar

Parametern `timeSeriesDefinitions=<collection of term objects>` anger predikattermer som ska visas i en time series insights-vy:

| Parameter | URL-objekt | Beskrivning |
| --- | --- | --- |
| **Namn** | `\<string>` | Namnet på *villkoret*. |
| **splitBy (splitBy)** | `\<string>` | Kolumnnamnet att *dela med*. |
| **mätNamn** | `\<string>` | Kolumnnamnet för *mått*. |
| **Predikat** | `\<string>` | *Where*-sats för filtrering på serversidan. |
| **useSum** | `true` | En valfri parameter som anger att summan används för ditt mått. |

> [!NOTE]
> Om `Events` är det valda **useSum-måttet** väljs antal som standard.  
> Om `Events` inte markeras väljs medelvärdet som standard. |

* Nyckel-värde-paret `multiChartStack=<true/false>` möjliggör stapling i diagrammet.
* Nyckel-värde-paret `multiChartSameScale=<true/false>` möjliggör samma Y-axelskala över termer inom en valfri parameter.  
* Med `timeBucketUnit=<Unit>&timeBucketSize=<integer>` möjlighet kan du justera intervallreglaget så att det ger en mer detaljerad eller jämnare, mer aggregerad vy av diagrammet.  
* Med `timezoneOffset=<integer>` parametern kan du ange tidszonen för diagrammet som ska visas som en förskjutning till UTC.

| Par(er) | Beskrivning |
| --- | --- |
| `multiChartStack=false` | `true`är aktiverat som standard `false` så passera till stack. |
| `multiChartStack=false&multiChartSameScale=true` | Stapling måste aktiveras att använda samma skala för y-axeln i villkoren.  Det är `false` som standard, `true` så att skicka aktiverar den här funktionen. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Enheter = `days` `hours`, `minutes` `seconds`, `milliseconds`, , .  Inled alltid enheten med versal. </br> Definiera antalet enheter genom att skicka önskat heltal för **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Heltalet är alltid i millisekunder. |

> [!NOTE]
> **timeBucketUnit-värden** kan jämnas ut upp till 7 dagar.
> **tidszonoffset-värden** är varken UTC eller lokal tid.

### <a name="examples"></a>Exempel

Om du vill lägga till tidsseriedefinitioner i en Time Series Insights-miljö som url-parameter lägger du till:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Använd exempeltidsseriedefinitionerna för:

* Miljö-ID
* De sista 60 minuterna av data
* Termerna **(F1PressureID**, **F2TempStation**och **F3VibrationPL**) som omfattar de valfria parametrarna

Du kan skapa följande parameteriserade URL för en vy:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Parameteriserad URL för Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Se Explorer live [med url-exemplet](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) ovan.

Url:en ovan beskriver och visar utforskarvyn Time Series Insights. 

* De parameteriserade predikaten.

  [![Time Series Insights explorer parameteriserade predikater.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Den delade fullständiga diagramvyn.

  [![Den delade fullständiga diagramvyn.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [frågar data med C#](time-series-insights-query-data-csharp.md).

* Lär dig mer om [Time Series Insights Explorer](./time-series-insights-explorer.md).
