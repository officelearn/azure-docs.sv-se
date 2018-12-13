---
title: 'Azure Time Series insikter URL: er - resursen Azure Time Series Insights anpassade vyer med parametriserade URL: er | Microsoft Docs'
description: Den här artikeln beskriver hur du utvecklar parametriserade URL:er i Azure Time Series Insights så att du enkelt kan dela en anpassad vy.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 5b43ba4c3f88c678b160e8aec442fa92b4822a3d
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274368"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Dela en anpassad vy med en parametriserad URL

Om du vill dela en anpassad vy i Time Series Insights Explorer kan du skapa en parametriserad URL för den anpassade vyn programmässigt.

Time Series Insights Explorer stöder URL-frågeparametrar så att du kan ange vyer i gränssnittet direkt från URL:en.  Med bara URL:en kan du till exempel ange en målmiljö, ett sökpredikat och önskat tidsintervall. När en användare klickar på den anpassade URL:en tillhandahåller gränssnittet en länk direkt till den tillgången i Time Series Insights-portalen.  Dataåtkomstprinciper tillämpas. 

## <a name="environment-id"></a>Miljö-ID

Parametern `environmentId=<guid>` anger målmiljö-ID:t.  Detta är en komponent för FQDN för dataåtkomst, och du hittar den längst upp till höger i miljööversikten i Azure Portal.  Det är allt som föregår `env.timeseries.azure.com`. Ett exempel på miljö-ID-parametern är `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tid

Du kan ange absoluta eller relativa tidsvärden med en parametriserad URL.

### <a name="absolute-time-values"></a>Absoluta tidsvärden

För absoluta tidsvärden använder du parametrarna `from=<integer>` och `to=<integer>`. 

`from=<integer>` är ett värde i JavaScript-millisekunder för starttiden för sökintervallet.

`to=<integer>` är ett värde i JavaScript-millisekunder för sluttiden för sökintervallet. 

Om du vill identifiera millisekunder i JavaScript för ett datum, se [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativa tidsvärden

För ett relativt tidsvärde använder du `relativeMillis=<value>`, där *värdet* anges i millisekunder i JavaScript från senaste data på serverdelen.

`&relativeMillis=3600000` visar till exempel data från de senaste 60 minuterna.

Godkända värden motsvarar **quick time**-menyn i Time Series Insights Explorer och inkluderar följande:

- 1800000 (senaste 30 minuterna)
- 3600000 (senaste 60 minuterna)
- 10800000 (senaste 3 timmarna)
- 21600000 (senaste 6 timmarna)
- 43200000 (senaste 12 timmarna)
- 86400000 (senaste 24 timmarna)
- 604800000 (senaste 7 dagarna)
- 2592000000 (senaste 30 dagarna)

### <a name="optional-parameters"></a>Valfria parametrar

Parametern `timeSeriesDefinitions=<collection of term objects>` anger villkoren för en Time Series Insights-vy, där:

- ”name”:”<string>”
  - Namnet på *villkoret*.
- ”splitBy”:”<string>”
  - Kolumnnamnet att *dela med*.
- ”measureName”:”<string>”
  - Kolumnnamnet för *mått*.
- ”predicate”:”<string>”
  - *Where*-sats för filtrering på serversidan.
- ”useSum”:”true”
  - Detta är en valfri parameter som anger att summering ska användas för din åtgärd.  Observera att om den valda åtgärden är ”Händelser” väljs antalet som standard.  Om ”Händelser” inte är markerat väljs medelvärdet som standard.  

Parametern multiChartStack = <true/false> aktiverar stapling i diagrammet och parametern multiChartSameScale = <true/false> aktiverar samma y-axelskala över villkoren i en valfri parameter.  

- 'multiChartStack=false'
  - True är aktiverat som standard så skicka False till stapel.
- 'multiChartStack=false&multiChartSameScale=true' 
  - Stapling måste aktiveras att använda samma skala för y-axeln i villkoren.  Det är false som standard, så skicka true aktiverar den här funktionen.  
  
Med timeBucketUnit=<Unit>&timeBucketSize=<integer>, kan du ändra intervallskjutreglaget för att ge en mer detaljerad eller jämnare och mer samlad diagramvy.  
- 'timeBucketUnit=<Unit>&timeBucketSize=<integer>'
  - Enheter (Units) = dagar, timmar, minuter, sekunder och millisekunder.  Inled alltid enheten med versal.
  - Definiera antalet enheter genom att skicka det önskade heltalet för timeBucketSize.  Observera att du jämnar upp till 7 dagar.  
  
Med parametern timezoneOffset=<integer> kan du ange tidszonen som diagrammet ska visas i som en förskjutning till UTC.  
  - 'timezoneOffset=-<integer>'
    - Heltalet är alltid i millisekunder.  
    - Observera den här funktionen är något annorlunda än vad vi aktivera i TSI-utforskaren där du kan välja lokal tid (webbläsartid) eller UTC.  
 
### <a name="examples"></a>Exempel

Om du till exempel vill lägga till tidsseriedefinitioner som en URL-parameter kan du använda följande:

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Med dessa exempel på tidsseriedefinitioner för 

- miljö-ID
- data från de senaste 60 minuterna
- villkor (F1PressureID, F2TempStation och F3VibrationPL) som utgör de valfria parametrarna
 
kan du skapa följande parametriserade URL för en vy:

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Om du hade använt Time Series Insights Explorer för att bygga vyn som beskrivs av föregående URL skulle den visas så här:

![Time Series Insights Explorer-villkor](media/parameterized-url/url1.png)

Den fullständiga vyn (inklusive diagrammet) skulle se ut så här:

![Diagramvy](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Nästa steg
[Fråga data med C#](time-series-insights-query-data-csharp.md)
