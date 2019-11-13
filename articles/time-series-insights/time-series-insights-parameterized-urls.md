---
title: 'Dela anpassade vyer med parametriserade URL: er – Azure Time Series Insights | Microsoft Docs'
description: 'Lär dig att utveckla parametriserade URL: er i Azure Time Series Insights så att du enkelt kan dela anpassade vyer.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: 145af35f8c36d7f4659c3937209cb0d4d5b221a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006378"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Dela en anpassad vy med en parametriserad URL

Om du vill dela en anpassad vy i Time Series Insights Explorer kan du program mässigt skapa en parametriserad URL för den anpassade vyn.

Time Series Insights Explorer stöder URL-frågeparametrar för att ange vyer i upplevelsen direkt från URL: en. Med bara URL:en kan du till exempel ange en målmiljö, ett sökpredikat och önskat tidsintervall. När en användare väljer den anpassade URL: en, tillhandahåller gränssnittet en länk direkt till den till gången i Time Series Insights portalen. Dataåtkomstprinciper tillämpas.

> [!TIP]
> * Visa den kostnads fria [Time Series Insights demonstrationen](https://insights.timeseries.azure.com/samples).
> * Läs dokumentationen om den medföljande [Time Series Insights Explorer](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>Miljö-ID

Parametern `environmentId=<guid>` anger målmiljö-ID:t. Det är en komponent i data åtkomst-FQDN, och du hittar den i det övre högra hörnet i miljö översikten i Azure Portal. Det är allt som föregår `env.timeseries.azure.com`.

Ett exempel på miljö-ID-parametern är `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tid

Du kan ange absoluta eller relativa tidsvärden med en parametriserad URL.

### <a name="absolute-time-values"></a>Absoluta tidsvärden

För absoluta tidsvärden använder du parametrarna `from=<integer>` och `to=<integer>`.

* `from=<integer>` är ett värde i JavaScript-millisekunder för starttiden för sökintervallet.
* `to=<integer>` är ett värde i JavaScript-millisekunder för sluttiden för sökintervallet.

Om du vill identifiera millisekunder i JavaScript för ett datum, se [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativa tidsvärden

För ett relativt tidsvärde använder du `relativeMillis=<value>`, där *värdet* anges i millisekunder i JavaScript från senaste data på serverdelen.

`&relativeMillis=3600000` visar till exempel data från de senaste 60 minuterna.

Godkända värden motsvarar **snabb tids** menyn i Time Series Insights Explorer och inkluderar:

* `1800000` (senaste 30 minuterna)
* `3600000` (senaste 60 minuter)
* `10800000` (senaste 3 timmarna)
* `21600000` (senaste 6 timmarna)
* `43200000` (senaste 12 timmarna)
* `86400000` (senaste 24 timmarna)
* `604800000` (senaste 7 dagarna)
* `2592000000` (senaste 30 timmarna)

### <a name="optional-parameters"></a>Valfria parametrar

Parametern `timeSeriesDefinitions=<collection of term objects>` anger villkoren i en Time Series Insights vy:

| Parameter | URL-objekt | Beskrivning |
| --- | --- | --- |
| **name** | `\<string>` | Namnet på *villkoret*. |
| **splitBy** | `\<string>` | Kolumnnamnet att *dela med*. |
| **measureName** | `\<string>` | Kolumnnamnet för *mått*. |
| **predikatet** | `\<string>` | *Where*-sats för filtrering på serversidan. |
| **useSum** | `true` | En valfri parameter som anger att summan ska används för måttet. </br>  Observera att om `Events` är det valda måttet, väljs antal som standard.  </br>  Om `Events` inte är markerat väljs Average som standard. |

* `multiChartStack=<true/false>` nyckel-värdeparet aktiverar stackning i diagrammet.
* `multiChartSameScale=<true/false>` nyckel-värdeparet aktiverar samma skala för Y-axeln mellan termer inom en valfri parameter.  
* Med `timeBucketUnit=<Unit>&timeBucketSize=<integer>` kan du justera skjutreglaget intervall så att det ger en mer detaljerad eller smidigare, mer sammanslagen vy av diagrammet.  
* Med parametern `timezoneOffset=<integer>` kan du ange tids zonen för diagrammet som ska visas som en förskjutning till UTC.

| Par (er) | Beskrivning |
| --- | --- |
| `multiChartStack=false` | `true` är aktiverat som standard så pass `false` stacken. |
| `multiChartStack=false&multiChartSameScale=true` | Stapling måste aktiveras att använda samma skala för y-axeln i villkoren.  Det är `false` som standard, så att skicka "true" aktiverar den här funktionen. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Enheter (Units) = dagar, timmar, minuter, sekunder och millisekunder.  Inled alltid enheten med versal. </br> Definiera antalet enheter genom att skicka det önskade heltalet för timeBucketSize.  Observera att du jämnar upp till 7 dagar.  |
| `timezoneOffset=-<integer>` | Heltalet är alltid i millisekunder. </br> OBS! den här funktionen skiljer sig något från vad vi aktiverar i Time Series Insights Explorer, där du kan välja lokal (webbläsarens tid) eller UTC. |

### <a name="examples"></a>Exempel

Lägg till tids serie definitioner i en Time Series Insights miljö som en URL-parameter genom att lägga till:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Använd exempel tid serie definitioner för:

* Miljö-ID
* De senaste 60 minuterna data
* Villkoren (F1PressureID, F2TempStation och F3VibrationPL) som utgör de valfria parametrarna

Du kan skapa följande parametriserade URL för en vy:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Se Explorer Live [med URL: en](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

URL: en ovan beskriver och bygger vyn Time Series Insights Explorer:

[Villkor för ![Time Series Insights Explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Fullständig vy (inklusive diagrammet):

[![diagramvy](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [frågar data C#med hjälp av ](time-series-insights-query-data-csharp.md).

* Lär dig mer om [Time Series Insights Explorer](./time-series-insights-explorer.md).
