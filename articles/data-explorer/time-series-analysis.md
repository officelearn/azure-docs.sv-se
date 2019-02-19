---
title: Azure Data Explorer analys av tidsserier
description: 'Lär dig mer om analys av tidsserier i Datautforskaren i Azure '
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: b7d498b34fa3e247d5d4688f8d87213e7707fd86
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408790"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analys av tidsserier i Datautforskaren i Azure

Azure Data Explorer (ADX) utför pågående insamling av telemetridata från molntjänster eller IoT-enheter. Dessa data kan analyseras för olika insikter som tjänstehälsa för övervakning, fysiska produktionsprocesser och användningstrender. Analysen utförs på tidsserier med valda mått för att hitta en avvikelse i mönstret jämfört med användningsmönster vanliga baslinje.
ADX innehåller inbyggt stöd för skapandet, bearbetning och analys av flera tidsserier. I det här avsnittet lär du dig hur ADX används för att skapa och analysera **tusentals tidsserier på några sekunder**, vilket gör att nästan i realtid övervakar lösningar och arbetsflöden.

## <a name="time-series-creation"></a>Time series-skapandet

I det här avsnittet ska vi skapa en stor uppsättning tidsserier enkelt och intuitivt sätt med hjälp av den `make-series` operatorn och det egna saknade värden efter behov.
Det första steget i analys av tidsserier är att partitionera och omvandla den ursprungliga tabellen telemetri till en uppsättning tidsserier. Tabellen innehåller vanligtvis en kolumn för tidsstämpel, sammanhangsberoende dimensioner och valfritt mått. Dimensioner används för att partitionera data. Målet är att skapa tusentals tidsserier per partition med jämna mellanrum.

Indatatabellen *demo_make_series1* innehåller 600 K poster av godtyckligt webbtrafik för tjänsten. Använd kommandot nedan om du till exempel 10 poster:

```kusto
demo_make_series1 | take 10 
```

Den resulterande tabellen innehåller en kolumn för tidsstämpel, tre sammanhangsberoende dimensionskolumner och inga mått:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Tidsstämpel | BrowserVer | OsVer | Land/region |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Storbritannien |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Storbritannien |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Litauen |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Indien |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Nederländerna |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Indien |

Eftersom det finns inga mått, kan vi bara skapa en uppsättning tidsserier som representerar trafik antal, partitioneras efter OS med följande fråga:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Använd den [ `make-series` ](/azure/kusto/query/make-seriesoperator) operator för att skapa en uppsättning tre tidsserie, där:
    - `num=count()`: time series av trafik
    - `range(min_t, max_t, 1h)`: tidsserier skapas i 1 timme lagerplatser i tidsintervallet (äldsta och senaste tidsstämplar av poster)
    - `default=0`: Ange fyllning metod för saknas lagerplatser skapa tidsserier. Du kan också använda [ `series_fill_const()` ](/azure/kusto/query/series-fill-constfunction), [ `series_fill_forward()` ](/azure/kusto/query/series-fill-forwardfunction), [ `series_fill_backward()` ](/azure/kusto/query/series-fill-backwardfunction) och [ `series_fill_linear()` ](/azure/kusto/query/series-fill-linearfunction) för ändringar
    - `byOsVer`: partition av Operativsystemet
- Faktiska data för tidsseriestruktur är en matris av aggregerat värde per varje gång bin. Vi använder `render timechart` för visualisering.

Vi har tre partitioner i tabellen ovan. Vi kan skapa en separat tidsserie: Windows 10 (röd), 7 (blå) och 8.1 (grön) för varje version av Operativsystemet som visas i diagrammet:

![Time series-partition](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Analys av tidsfunktioner-serien

I det här avsnittet ska vi utföra antal typiska bearbetning av funktioner.
När en uppsättning tidsserier skapas ADX stöder en växande lista över funktioner som kan bearbeta och analysera dem som finns i den [time series-dokumentationen](/azure/kusto/query/machine-learning-and-tsa). Vi visar några representativa funktioner för att bearbeta och analysera tidsserier.

### <a name="filtering"></a>Filtrering

Filtrering är ett vanligt vid signal bearbetnings- och användbar för tidsserier bearbetningsåtgärder (till exempel smooth en bort störande signal, ändra identifiering).
- Det finns två allmänna filtrering funktioner:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Använda FILNAMNSDELEN filter. Används för enkel beräkning av glidande medelvärde och differentiering tidsserien för identifiering av ändring av.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Tillämpar IIR filter. Används för exponentiell utjämning och kumulativa summan.
- `Extend` tidsserier genom att lägga till en ny glidande genomsnittlig serie av storlek på 5 lagerplatser (med namnet *ma_num*) till frågan:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Tidsserier filtrering](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressionsanalys

Har stöd för ADX segmenterade linjär regressionsanalyser för att beräkna trenden för tidsserier.
- Använd [series_fit_line()](/azure/kusto/query/series-fit-linefunction) så att de passar bäst raden till en tidsserie för identifiering av allmänna trenden.
- Använd [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) att identifiera trend ändringar, i förhållande till i baslinjen, som är användbara i övervakningsscenarier.

Exempel på `series_fit_line()` och `series_fit_2lines()` funktioner i en time series-fråga:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Time series regression](media/time-series-analysis/time-series-regression.png)

- Blå: ursprungliga tidsserier
- Grön: försedda rad
- Röd: två anpassade rader

> [!NOTE]
> Funktionen identifierade korrekt jump (nivåändring) punkt.

### <a name="seasonality-detection"></a>Säsongsberoende identifiering

Många mått att följa säsongens (periodiska) mönster. Trafik för användare av molntjänster innehåller vanligtvis dagliga och veckovisa mönster som är högsta runt mitten av arbetsdagen och lägsta nattetid och under helgen. IoT sensorer måttet i regelbundna intervall. Fysiska mått, till exempel temperatur, tryck eller fuktighet kan också visa säsongsbaserade beteenden.

I följande exempel gäller säsongsberoende identifiering på en månad trafik för en webbtjänst (2 timmar diskreta grupper):

```kusto
demo_series3
| render timechart 
```

![Time series säsongsberoende](media/time-series-analysis/time-series-seasonality.png)

- Använd [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) att automatiskt identifiera punkter i tidsserien. 
- Använd [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) om vet vi att ett mått ska ha specifika distinkta period(er) och vi vill kontrollera att de finns.

> [!NOTE]
> Det är ett fel om det angivna distinkta perioder som inte finns

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mvexpand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | perioder | Poäng | dagar |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funktionen identifierar dagliga och veckovisa säsongsberoende. Dagligen poängsätter mindre än veckan eftersom lördag och söndag skiljer sig från vardagar.

### <a name="element-wise-functions"></a>Element-Wise funktioner

Aritmetiskt och logiska åtgärder kan göras på en tidsserie. Med hjälp av [series_subtract()](/azure/kusto/query/series-subtractfunction) vi kan beräkna en resterande tidsserie, som är, skillnaden mellan ursprungliga rådata mått och en utjämnad och leta efter avvikelser i återstående signalen:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Time series-åtgärder](media/time-series-analysis/time-series-operations.png)

- Blå: ursprungliga tidsserier
- Röd: utjämnat för tidsserier
- Grön: resterande tidsserier

## <a name="time-series-workflow-at-scale"></a>Time series arbetsflöde i stor skala

Exemplet nedan visar hur dessa funktioner kan köra i stor skala på tusentals tidsserier i sekunder för avvikelseidentifiering. Om du vill se några exempel telemetri poster för en DB-tjänsten Läs antal mått fyra dagar för att köra följande fråga:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | LOC | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | LOC 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | LOC 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | LOC 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | LOC 9 | 371921734563783410 | 255 | 0 |

Och enkelt statistik:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | NUM | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Att skapa en tidsserie på 1 timme lagerplatser för skrivskyddade mått (totalt fyra dagar * 24 timmar = 96 punkter), resulterar i normala mönstret variationerna:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Tidsserier i stor skala](media/time-series-analysis/time-series-at-scale.png)

Beteendet ovan är vilseledande eftersom den enda normala tidsserien sammanställs från tusentals olika instanser som kan ha onormala mönster. Därför kan skapa vi en tidsserie per instans. En instans definieras av Loc (plats), anonOp (åtgärd) och DB (specifik dator).

Hur många tidsserier kan vi skapa?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Antal |
|   | 18339 |

Nu ska vi skapa en uppsättning 18339 tidsserier med skrivskyddade count-mått. Vi lägger till den `by` -satsen för att kontrollera-serien-instruktionen tillämpa linjär regression och välj upp två tidsserier som hade mest betydande minskning trend:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Tidsserier viktigaste två](media/time-series-analysis/time-series-top-2.png)

Visa instanser:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | LOC | Op | DB | lutning |
|   | LOC 15 | 37 | 1151 | -102743.910227889 |
|   | LOC 13 | 37 | 1249 | -86303.2334644601 |

På mindre än två minuter ADX analyseras nära 20 000 tidsserier och upptäcks två onormalt tidsserier där det lästa antalet plötsligt tas bort.

Dessa avancerade funktioner som kombineras med ADX snabba prestanda utgör en unik och kraftfull lösning för analys av tidsserier.
