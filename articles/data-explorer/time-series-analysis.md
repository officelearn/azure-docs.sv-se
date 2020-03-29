---
title: Analysera tidsseriedata med Azure Data Explorer
description: Lär dig hur du analyserar tidsseriedata i molnet med Hjälp av Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765491"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analys av tidsserier i Azure Data Explorer

Azure Data Explorer (ADX) utför pågående insamling av telemetridata från molntjänster eller IoT-enheter. Dessa data kan analyseras för olika insikter som övervakning av tjänstens hälsa, fysiska produktionsprocesser och användningstrender. Analys görs på tidsserier med valda mått för att hitta en avvikelse i mönstret jämfört med dess typiska baslinjemönster.
ADX innehåller inbyggt stöd för skapande, manipulering och analys av flera tidsserier. I det här avsnittet kan du lära dig hur ADX används för att skapa och analysera **tusentals tidsserier på några sekunder,** vilket möjliggör övervakningslösningar och arbetsflöden i nära realtid.

## <a name="time-series-creation"></a>Skapa tidsserier

I det här avsnittet skapar vi en stor uppsättning regelbundna tidsserier enkelt och intuitivt med hjälp av `make-series` operatören och fyll i saknade värden efter behov.
Det första steget i tidsserieanalys är att partitionera och omvandla den ursprungliga telemetritabellen till en uppsättning tidsserier. Tabellen innehåller vanligtvis en tidsstämpelkolumn, kontextuella dimensioner och valfria mått. Dimensionerna används för att partitionera data. Målet är att skapa tusentals tidsserier per partition med jämna mellanrum.

Indatatabellen *innehåller demo_make_series1* 600 000 poster för godtycklig webbtjänsttrafik. Använd kommandot nedan för att prova 10 poster:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA)**\]**

```kusto
demo_make_series1 | take 10 
```

Den resulterande tabellen innehåller en tidsstämpelkolumn, tre kontextuella dimensioner och inga mått:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | TimeStamp | WebbläsareVer | OsVer () | Land/region |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Storbritannien |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Storbritannien |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Republiken Litauen |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Indien |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Nederländerna |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Indien |

Eftersom det inte finns några mått kan vi bara skapa en uppsättning tidsserier som representerar själva trafikantalet, partitionerad av operativsystemet med hjälp av följande fråga:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Använd [`make-series`](/azure/kusto/query/make-seriesoperator) operatorn för att skapa en uppsättning med tre tidsserier, där:
    - `num=count()`: tidsserier av trafik
    - `range(min_t, max_t, 1h)`: tidsserier skapas i 1-timmarsfackplatser i tidsintervallet (äldsta och nyaste tidsstämplar för tabellposter)
    - `default=0`: Ange fyllningsmetod för saknade lagerplatser för att skapa vanliga tidsserier. Alternativt använda [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction) [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) och för ändringar
    - `byOsVer`: partition av OS
- Datastrukturen för faktiska tidsserier är en numerisk matris med det aggregerade värdet per tidslagerplats. Vi `render timechart` använder för visualisering.

I tabellen ovan har vi tre partitioner. Vi kan skapa en separat tidsserie: Windows 10 (röd), 7 (blå) och 8.1 (grön) för varje OS-version som visas i diagrammet:

![Tidsseriepartition](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Analysfunktioner för tidsserier

I det här avsnittet utför vi typiska seriebearbetningsfunktioner.
När en uppsättning tidsserier har skapats stöder ADX en växande lista över funktioner för att bearbeta och analysera dem som finns i [tidsseriedokumentationen](/azure/kusto/query/machine-learning-and-tsa). Vi kommer att beskriva några representativa funktioner för bearbetning och analys av tidsserier.

### <a name="filtering"></a>Filtrering

Filtrering är en vanlig praxis i signalbehandling och användbar för tidsseriebehandling uppgifter (till exempel jämna en bullrig signal, ändra upptäckt).
- Det finns två generiska filtreringsfunktioner:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Tillämpa FIR-filter. Används för enkel beräkning av glidande medelvärde och differentiering av tidsserierna för ändringsdetektering.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Tillämpa IIR-filter. Används för exponentiell utjämning och ackumulerad summa.
- `Extend`tidsserierna som angetts genom att lägga till en ny glidande medelmåttig serie med storlek 5-lagerplatser (med namnet *ma_num)* i frågan:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtrering av tidsserier](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressionsanalys

ADX stöder segmenterad linjär regressionsanalys för att uppskatta tidsseriens trend.
- Använd [series_fit_line()](/azure/kusto/query/series-fit-linefunction) för att passa den bästa raden till en tidsserie för allmän trendidentifiering.
- Använd [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) för att identifiera trendförändringar i förhållande till baslinjen som är användbara i övervakningsscenarier.

Exempel `series_fit_line()` på `series_fit_2lines()` och funktioner i en tidsseriefråga:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=)**\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Tillbakagångsregression av tidsserier](media/time-series-analysis/time-series-regression.png)

- Blå: ursprungliga tidsserier
- Grön: monterad linje
- Röd: två monterade linjer

> [!NOTE]
> Funktionen upptäckte korrekt hopppunkten (nivåändring).

### <a name="seasonality-detection"></a>Upptäckt av säsongsvariation

Många mätvärden följer säsongsmässiga (periodiska) mönster. Användartrafik av molntjänster innehåller vanligtvis dagliga och veckovisa mönster som är högst runt mitten av arbetsdagen och lägsta på natten och under helgen. IoT-sensorer mäter periodiska intervaller. Fysiska mätningar som temperatur, tryck eller luftfuktighet kan också visa säsongsbeteende.

I följande exempel används identifiering av säsongsvariationer för en månads trafik av en webbtjänst (tvåtimmarsfack):

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA)**\]**

```kusto
demo_series3
| render timechart 
```

![Säsongsvariationer i tidsserier](media/time-series-analysis/time-series-seasonality.png)

- Använd [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) för att automatiskt identifiera perioderna i tidsserierna. 
- Använd [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) om vi vet att ett mått ska ha specifika distinkta perioder och vi vill verifiera att de finns.

> [!NOTE]
> Det är en anomali om specifika distinkta perioder inte finns

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA)**\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Perioder | Noter | Dagar |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funktionen upptäcker dagliga och veckovisa säsongsvariationer. De dagliga poängen mindre än den veckovisa eftersom helgdagar skiljer sig från vardagar.

### <a name="element-wise-functions"></a>Element-wise funktioner

Aritmetiska och logiska operationer kan göras på en tidsserie. Med hjälp av [series_subtract()](/azure/kusto/query/series-subtractfunction) kan vi beräkna en resttidsserie, det vill än skillnaden mellan det ursprungliga råmetriska måttet och ett utjämnat, och leta efter avvikelser i restsignalen:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=)**\]**

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

![Tidsserier](media/time-series-analysis/time-series-operations.png)

- Blå: ursprungliga tidsserier
- Röd: jämnad tidsserie
- Grön: resttidsserier

## <a name="time-series-workflow-at-scale"></a>Tidsseriearbetsflöde i stor skala

Exemplet nedan visar hur dessa funktioner kan köras i skala på tusentals tidsserier på några sekunder för avvikelseidentifiering. Om du vill visa några exempeltelemetriposter för en DB-tjänsts läsantalsmått under fyra dagar kör du följande fråga:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==)**\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | Loc | anonOp (på) | Db | DataLäs |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (Plats 9) | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (Plats 9) | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (Plats 9) | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (Plats 9) | 371921734563783410 | 255 | 0 |

Och enkel statistik:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA)**\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Att bygga en tidsserie i 1-timmarsfack för läsmåttet (totalt fyra dagar * 24 timmar = 96 poäng), resulterar i normala mönsterfluktuationer:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Tidsserier i stor skala](media/time-series-analysis/time-series-at-scale.png)

Ovanstående beteende är missvisande, eftersom den enda normala tidsserien aggregeras från tusentals olika instanser som kan ha onormala mönster. Därför skapar vi en tidsserie per instans. En instans definieras av Loc (plats), anonOp (operation) och DB (specifik dator).

Hur många tidsserier kan vi skapa?

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=)**\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Antal |
|   | 18339 |

Nu ska vi skapa en uppsättning av 18339 tidsserier av läsräkningsmåttet. Vi lägger till klausulen `by` i uttalandet, tillämpar linjär regression och väljer de två översta tidsserierna som hade den mest betydande nedåtgående trenden:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Tidsserier topp två](media/time-series-analysis/time-series-top-2.png)

Visa instanserna:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA)**\]**

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
|   | Loc | Op | Db | Sluttningen |
|   | Plats 15 | 37 | 1151 | -102743.910227889 |
|   | Plats 13 | 37 | 1249 | -86303.2334644601 |

På mindre än två minuter analyserade ADX nära 20 000 tidsserier och upptäckte två onormala tidsserier där läsantalet plötsligt sjönk.

Dessa avancerade funktioner i kombination med ADX snabba prestanda ger en unik och kraftfull lösning för tidsserieanalys.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [avvikelseidentifiering och prognostisering av tidsserier](/azure/data-explorer/anomaly-detection) i Azure Data Explorer.
* Lär dig mer om [machine learning-funktioner](/azure/data-explorer/machine-learning-clustering) i Azure Data Explorer.