---
title: Analysera tids serie data med Azure Datautforskaren
description: Lär dig hur du analyserar Time Series-data i molnet med hjälp av Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765491"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analys av tids serier i Azure Datautforskaren

Azure Datautforskaren (ADX) utför pågående insamling av telemetridata från moln tjänster eller IoT-enheter. Dessa data kan analyseras för olika insikter, till exempel övervakning av tjänstens hälsa, fysiska produktions processer och användnings trender. Analys görs i tids serier med valda mått för att hitta en avvikelse i mönstret jämfört med dess typiska bas linje mönster.
ADX innehåller inbyggt stöd för att skapa, manipulera och analysera flera tids serier. I det här avsnittet får du lära dig hur ADX används för att skapa och analysera **tusentals tids serier på några sekunder**, vilket möjliggör övervakning av lösningar och arbets flöden i nära real tid.

## <a name="time-series-creation"></a>Skapa tids serier

I det här avsnittet ska vi skapa en stor uppsättning vanliga tids serier enkelt och intuitivt med hjälp av `make-series` operatören och fylla i saknade värden efter behov.
Det första steget i tids serie analysen är att partitionera och transformera den ursprungliga telemetri-tabellen till en uppsättning tids serier. Tabellen innehåller vanligt vis en tidsstämpelkolumn, sammanhangsbaserade dimensioner och valfria mått. Dimensionerna används för att partitionera data. Målet är att skapa tusentals tids serier per partition med regelbundna tidsintervall.

Tabellen *demo_make_series1* innehåller 600K poster av godtycklig webb tjänst trafik. Använd kommandot nedan för att sampla 10 poster:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

Den resulterande tabellen innehåller en tidsstämpelkolumn, tre sammanhangsbaserade dimensions kolumner och inga mått:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Tidsstämpel | BrowserVer | OsVer | Land/region |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51,0 | Windows 7 | Storbritannien |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52,0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52,0 | Windows 7 | Storbritannien |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52,0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52,0 | Windows 10 | Republiken Litauen |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52,0 | Windows 8.1 | Indien |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52,0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52,0 | Windows 7 | Nederländerna |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52,0 | Windows 10 | Storbritannien |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52,0 | Windows 10 | Indien |

Eftersom det inte finns några mått kan vi bara skapa en uppsättning tids serier som representerar själva antalet trafik, partitionerade efter operativ system med hjälp av följande fråga:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Använd operatorn [`make-series`](/azure/kusto/query/make-seriesoperator) för att skapa en uppsättning tre tids serier där:
    - `num=count()`: tids serie trafik
    - `range(min_t, max_t, 1h)`: tids serien skapas på 1-timmars lager platser i tidsintervallet (äldsta och senaste tidsstämplar för tabell poster)
    - `default=0`: Ange Fill-metoden för saknade lager platser för att skapa regelbundna tids serier. Du kan också använda [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) och [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) för ändringar
    - `byOsVer`: partition efter OS
- Den faktiska tids seriens data struktur är en numerisk matris med det sammanlagda värdet per tidpunkt. Vi använder `render timechart` för visualisering.

Vi har tre partitioner i tabellen ovan. Vi kan skapa en separat tids serie: Windows 10 (Red), 7 (blå) och 8,1 (grön) för varje operativ system version som visas i diagrammet:

![Tids serie partition](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Analys funktioner för tids serier

I det här avsnittet ska vi utföra typiska funktioner för serie bearbetning.
När en uppsättning tids serier har skapats stöder ADX en växande lista med funktioner för att bearbeta och analysera dem som finns i [Time Series-dokumentationen](/azure/kusto/query/machine-learning-and-tsa). Vi kommer att beskriva några av de representativa funktionerna för bearbetning och analys av tids serier.

### <a name="filtering"></a>Filtrering

Filtrering är en vanlig metod för signal bearbetning och användbart för bearbetnings uppgifter för tids serier (till exempel utjämna en brus signal, ändrings identifiering).
- Det finns två allmänna filtrerings funktioner:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): tillämpar FIR-filter. Används för enkel beräkning av glidande medelvärde och differentiering av tids serien för ändrings identifiering.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): tillämpar IIR-filter. Används för Exponentiell utjämning och ackumulerad summa.
- `Extend` tids serien som har angetts genom att lägga till en ny glidande genomsnitts serie med storlek 5 lager platser (med namnet *ma_num*) till frågan:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtrering av tids serier](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressions analys

ADX stöder segmenterad linjär Regressions analys för att uppskatta trenden för tids serien.
- Använd [series_fit_line ()](/azure/kusto/query/series-fit-linefunction) för att anpassa den bästa raden till en tids serie för allmän trend identifiering.
- Använd [series_fit_2lines ()](/azure/kusto/query/series-fit-2linesfunction) för att identifiera trend ändringar i förhållande till bas linjen, som är användbara i övervaknings scenarier.

Exempel på `series_fit_line()` och `series_fit_2lines()` funktioner i en tids serie fråga:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Time Series-regression](media/time-series-analysis/time-series-regression.png)

- Blå: ursprunglig tids serie
- Grön: monterad linje
- Röd: två monterade linjer

> [!NOTE]
> Funktionen identifierade hopp punkten (nivå förändring) korrekt.

### <a name="seasonality-detection"></a>Säsongs beroende-identifiering

Många mått följer säsongs mönster (periodiska). Användar trafiken i Cloud Services innehåller vanligt vis dagliga och vecko Visa mönster som är högst mitten av arbets dagen och den lägsta på natten och över helgen. IoT-sensorer mäter i regelbundna intervall. Fysiska mått som temperatur, tryck eller fuktighet kan också Visa säsongs beteende.

I följande exempel används säsongs beroende-identifiering på en månads trafik i en webb tjänst (2 timmars fack):

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Säsongs beroende för Time Series](media/time-series-analysis/time-series-seasonality.png)

- Använd [series_periods_detect ()](/azure/kusto/query/series-periods-detectfunction) för att automatiskt identifiera perioderna i tids serien. 
- Använd [series_periods_validate ()](/azure/kusto/query/series-periods-validatefunction) om vi vet att ett mått ska ha särskilda distinkta perioder och vi vill verifiera att de finns.

> [!NOTE]
> Det är en avvikelse om specifika distinkta perioder inte finns

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | tidsfrist | resultatet | antalet |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funktionen identifierar dagliga och vecko Visa säsongs beroende. De dagliga resultaten är mindre än vecko dagar, eftersom veckosluts dagar skiljer sig från vardagar.

### <a name="element-wise-functions"></a>Element – klokt-funktioner

Aritmetiska och logiska åtgärder kan utföras i en tids serie. Med hjälp av [series_subtract ()](/azure/kusto/query/series-subtractfunction) kan vi beräkna en rest tids serie, det vill säga skillnaden mellan ursprungligt rå mått och en utjämnad och söka efter avvikelser i resten av signalen:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

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

![Tids serie åtgärder](media/time-series-analysis/time-series-operations.png)

- Blå: ursprunglig tids serie
- Röd: utjämnad tids serie
- Grönt: rest Time-serien

## <a name="time-series-workflow-at-scale"></a>Tids serie arbets flöde i skala

Exemplet nedan visar hur dessa funktioner kan köras i skala på tusentals tids serier i sekunder för avvikelse identifiering. Om du vill se några exempel på telemetri för en DB-tjänsts mått för Läs antal under fyra dagar kör du följande fråga:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIDSSTÄMPEL | Loc | anonOp | DATABASEN | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | – 865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

Och enkel statistik:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | NUM | minsta\_t | Max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Skapa en tids serie i 1 – timmes lager platser för Läs mått (totalt fyra dagar * 24 timmar = 96 punkter), vilket resulterar i normala mönster variationer:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Tids serier i skala](media/time-series-analysis/time-series-at-scale.png)

Ovanstående beteende är missvisande, eftersom den enkla normal tids serien sammanställs från tusentals olika instanser som kan ha onormala mönster. Därför skapar vi en tids serie per instans. En instans definieras av Loc (plats), anonOp (operation) och DB (viss dator).

Hur många tids serier kan vi skapa?

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Antal |
|   | 18339 |

Nu ska vi skapa en uppsättning 18339 tids serier av måttet för att läsa antalet. Vi lägger till `by`-satsen i instruktionen tillverka-serien, använder linjär regression och väljer de två främsta tids serier som hade den mest betydande minsknings trenden:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Två främsta tids serier](media/time-series-analysis/time-series-top-2.png)

Visa instanserna:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

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
|   | Loc | Öpp | DATABASEN | lutning |
|   | Loc 15 | 37 | 1151 | – 102743,910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

På mindre än två minuter analyserade ADX nära 20 000 tids serier och upptäckte två onormal tids serier där antalet läsningar plötsligt släpptes.

Dessa avancerade funktioner kombinerat med ADX snabb prestanda tillhandahåller en unik och kraftfull lösning för tids serie analys.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [avvikelse identifiering och prognoser för tids serier](/azure/data-explorer/anomaly-detection) i Azure datautforskaren.
* Lär dig mer om [Machine Learning-funktioner](/azure/data-explorer/machine-learning-clustering) i Azure datautforskaren.