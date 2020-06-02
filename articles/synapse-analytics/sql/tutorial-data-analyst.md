---
title: Självstudier om data analytiker – Använd SQL på begäran (för hands version) för att analysera Azure Open-datauppsättningar i Azure Synapse Studio (för hands version)
description: I den här självstudien får du lära dig hur du enkelt kan utföra analys av test data som kombinerar olika Azure Open-datauppsättningar med SQL på begäran (för hands version) och visualisera resultatet i Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5084867d3db2da6718935f2af85e6148f2adbff8
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258920"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Använd SQL på begäran (för hands version) för att analysera Azure Open-datauppsättningar och visualisera resultaten i Azure Synapse Studio (för hands version)

I den här självstudien får du lära dig hur du utför analys av exempel data genom att kombinera olika Azure Open-datauppsättningar med SQL på begäran och sedan visualisera resultaten i Azure Synapse Studio.

I synnerhet analyseras den [nya taxi-datauppsättningen för Göteborg (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) som innehåller plocknings-och utgångs datum/-tidpunkter, hämtnings-och list platser, rese avstånd, specificerade priser, pris typer, betalnings typer och driv rutins rapporter som rapporteras.

Fokus på analysen är att hitta trender i förändringar av antalet taxi-ändringar över tid. Du analyserar två andra Azure Open-datauppsättningar ([offentliga helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) och [väder data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) för att förstå avvikande värden i antal taxi-tillägg.


## <a name="automatic-schema-inference"></a>Automatisk schema härledning

Eftersom data lagras i Parquet-filformat är automatisk schema härledning tillgängligt, så det är enkelt att skicka frågor till data utan att behöva ange data typerna för alla kolumner i filerna. Dessutom kan en virtuell kolumn mekanism och funktionen filsökväg användas för att filtrera bort en del av filerna.

Vi börjar med att bekanta dig med NYC taxi-data genom att köra följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Nedan visas ett resultat-kodfragment för NYC taxi-data:

![resultat kod avsnitt](./media/tutorial-data-analyst/1.png)

På samma sätt kan vi skicka frågor till den offentliga helgdags data uppsättningen med följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Nedan visas ett resultat-kodfragment för den offentliga helgdags data uppsättningen:

![resultat avsnitt 2](./media/tutorial-data-analyst/2.png)

Slutligen kan vi även fråga väder data uppsättningen med följande fråga:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Nedan visas ett resultat-kodfragment för väder data uppsättningen:

![resultat avsnitt 3](./media/tutorial-data-analyst/3.png)

Du kan lära dig mer om innebörden av enskilda kolumner i beskrivningarna av [NYC taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [offentliga helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)och data uppsättningar för [väder data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) .

## <a name="time-series-seasonality-and-outlier-analysis"></a>Tids serier, säsongs beroende och avvikare analys

Du kan enkelt sammanfatta det årliga antalet taxi-åsidosättningar med hjälp av följande fråga:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Följande visar resultatet av resultatet för det årliga antalet taxi-åsidosättningar:

![resultat kod 4](./media/tutorial-data-analyst/4.png)

Data kan visualiseras i Synapse Studio genom att växla från tabell till diagramvy. Du kan välja bland olika diagram typer (Area, stapel, kolumn, linje, cirkel och punkt). I det här fallet ska vi rita upp stapeldiagram med kategori kolumn inställt på "current_year":

![resultat visualisering 5](./media/tutorial-data-analyst/5.png)

I den här visualiseringen kan en tendens av ett minskat antal ändringar i år visas tydligt, förmodligen på grund av en nyligen ökad popularitet av att dela företag.

> [!NOTE]
> Vid tidpunkten för den här självstudien är data för 2019 ofullständiga, så det finns ett stort antal olika åsidosättningar för det året.

Nu ska vi fokusera på vår analys på ett enda år, till exempel 2016. Följande fråga returnerar det dagliga antalet åsidosättningar under det året:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

I följande avsnitt visas resultatet av den här frågan:

![resultat kod 6](./media/tutorial-data-analyst/6.png)

Återigen kan vi enkelt visualisera data genom att rita kolumn diagram med kolumnen Kategori current_day och förklaring (serien) rides_per_day.

![resultat visualisering 7](./media/tutorial-data-analyst/7.png)

Från området kan det observeras att det finns ett vecko mönster, med lördagens topp. Under sommaren månader finns det färre taxi-åsidosättningar på grund av semester period. Det finns dock också några betydande droppar i antal Taxis ökningar utan tydliga mönster när och varför de inträffar.

Nu ska vi se om dessa droppar är potentiellt korrelerade med offentliga helgdagar genom att delta i NYC taxi-åsidosättningar med den offentliga helgdags data uppsättningen:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![resultat visualisering 8](./media/tutorial-data-analyst/8.png)

Den här gången vill vi belysa antalet taxi-ändringar under offentliga helgdagar. För detta ändamål väljer vi "ingen" för kolumnen Kategori "och" rides_per_day "och" semester "som förklarings kolumner (serier).

![resultat visualisering 9](./media/tutorial-data-analyst/9.png)

Från området kan det tydligt visas att ett antal taxi-åsidosättningar är lägre under offentliga helgdagar. Det finns dock fortfarande en icke-förklarad enorma minskning den 23 januari. Nu ska vi titta på väder i NYC den dagen genom att fråga väder data uppsättningen:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![resultat visualisering 10](./media/tutorial-data-analyst/10.png)

Resultatet av frågan indikerar att minskningen i ett antal taxi-åsidosättningar berodde på:

- Blizzard den dagen i NYC, eftersom det var tungt snö (~ 30 cm)
- Det var kall (temperatur under noll grader Celsius)
- och lindning (~ 10 miljoner/s)

I den här självstudien visas hur data analytiker snabbt kan utföra analys av analys av data, enkelt kombinera olika data uppsättningar med SQL på begäran och visualisera resultaten med Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

Granska den [ansluta SQL på begäran för att Power BI Desktop & Skapa rapport](tutorial-connect-power-bi-desktop.md) artikel och lär dig hur du ansluter SQL på begäran till Power BI Desktop och skapar rapporter.
 
