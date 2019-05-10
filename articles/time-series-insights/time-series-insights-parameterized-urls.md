---
title: Dela anpassade Azure Time Series Insights-vyer med parametriserade URL:er | Microsoft Docs
description: Den här artikeln beskriver hur du utvecklar parametriserade URL:er i Azure Time Series Insights så att du enkelt kan dela en anpassad vy.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: e70eb7ae73e88b37e649d519d0d0428554dd4ab3
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467526"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Dela en anpassad vy med en parametriserad URL

Om du vill dela en anpassad vy i Time Series Insights Explorer kan du skapa en parametriserad URL för den anpassade vyn programmässigt.

Time Series Insights Explorer stöder URL-frågeparametrar för att ange vyer i gränssnittet direkt från URL: en. Med bara URL:en kan du till exempel ange en målmiljö, ett sökpredikat och önskat tidsintervall. När en användare klickar på den anpassade URL:en tillhandahåller gränssnittet en länk direkt till den tillgången i Time Series Insights-portalen. Dataåtkomstprinciper tillämpas.

> [!TIP]
> * Visa den kostnadsfria [Time Series Insights demonstrera](https://insights.timeseries.azure.com/samples).
> * Läs den tillhörande [Time Series Insights Explorer](./time-series-insights-explorer.md) dokumentation.

## <a name="environment-id"></a>Miljö-ID

Parametern `environmentId=<guid>` anger målmiljö-ID:t. Det är en komponent i dataåtkomst FQDN och du hittar den i det övre högra hörnet i miljööversikten i Azure-portalen. Det är allt som föregår `env.timeseries.azure.com`.

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

Godkända värden motsvarar Time Series Insights explorer **snabb tid** -menyn och inkluderar:

* `1800000` (De senaste 30 minuterna)
* `3600000` (De senaste 60 minuterna)
* `10800000` (Senaste 3 timmarna)
* `21600000` (Senaste 6 timmarna)
* `43200000` (Senaste 12 timmarna)
* `86400000` (Senaste 24 timmarna)
* `604800000` (Senaste 7 dagarna)
* `2592000000` (Senaste 30 timmar)

### <a name="optional-parameters"></a>Valfria parametrar

Den `timeSeriesDefinitions=<collection of term objects>` parametern anger villkoren i en Time Series Insights-vy:

| Parameter | URL-objekt | Beskrivning |
| --- | --- | --- |
| **name** | `\<string>` | Namnet på *villkoret*. |
| **splitBy** | `\<string>` | Kolumnnamnet att *dela med*. |
| **measureName** | `\<string>` | Kolumnnamnet för *mått*. |
| **Predikatet** | `\<string>` | *Where*-sats för filtrering på serversidan. |
| **useSum** | `true` | En valfri parameter som anger att summering för ditt mått. </br>  Observera att om `Events` är det markerade måttet antal väljs som standard.  </br>  Om `Events` är inte markerat väljs medelvärdet som standard. |

* Den `multiChartStack=<true/false>` nyckel / värde-par aktiverar stapling i diagrammet.
* Den `multiChartSameScale=<true/false>` nyckel / värde-par aktiverar samma y-axelskala över villkoren i en valfri parameter.  
* Den `timeBucketUnit=<Unit>&timeBucketSize=<integer>` kan du ändra intervallskjutreglaget för att ge en mer detaljerad eller jämnare och mer samlad diagramvy.  
* Den `timezoneOffset=<integer>` parametern kan du ange tidszonen som diagrammet ska visas i som en förskjutning till UTC.

| Pair(s) | Beskrivning |
| --- | --- |
| `multiChartStack=false` | `true` är aktiverad som standard så skicka `false` stapla. |
| `multiChartStack=false&multiChartSameScale=true` | Stapling måste aktiveras att använda samma skala för y-axeln i villkoren.  Den har `false` som standard så skicka 'true' aktiverar den här funktionen. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Enheter (Units) = dagar, timmar, minuter, sekunder och millisekunder.  Inled alltid enheten med versal. </br> Definiera antalet enheter genom att skicka det önskade heltalet för timeBucketSize.  Observera att du jämnar upp till 7 dagar.  |
| `timezoneOffset=-<integer>` | Heltalet är alltid i millisekunder. </br> Observera den här funktionen är något annorlunda än vad vi aktivera i TSI-utforskaren där du kan välja lokal tid (webbläsartid) eller UTC. |

### <a name="examples"></a>Exempel

Lägg till för att lägga till tidsseriedefinitioner en TSI-miljö som en URL-parameter:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Använd exempel tidsseriedefinitioner för:

* Miljö-ID
* De senaste 60 minuterna av data
* Villkor (F1PressureID, F2TempStation och F3VibrationPL) som utgör de valfria parametrarna

Du kan skapa följande parametriserade URL för en vy:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Se Explorer live [med hjälp av URL: en](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

URL: en ovan beskriver och TSI-Utforskaren vy:

[![Time Series Insights explorer-villkor](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Den fullständiga vyn (inklusive diagrammet):

[![Diagramvy](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [fråga data med hjälp av C# ](time-series-insights-query-data-csharp.md).

* Lär dig mer om den [Time Series Insights Explorer](./time-series-insights-explorer.md).
