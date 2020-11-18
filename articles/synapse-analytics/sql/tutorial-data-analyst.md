---
title: 'Självstudie: Använd Server lös SQL-pool (för hands version) för att analysera Azure Open-datauppsättningar i Azure Synapse Studio (för hands version)'
description: Den här självstudien visar hur du enkelt kan utföra analys av analys data som kombinerar olika Azure Open-datauppsättningar med hjälp av SQL-poolen utan server (för hands version) och visualisera resultatet i Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 84fc49df2838a66969b449dee5b416c2a0f86f86
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685927"
---
# <a name="tutorial-use-serverless-sql-pool-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Självstudie: Använd Server lös SQL-pool för att analysera Azure Open-datauppsättningar och visualisera resultaten i Azure Synapse Studio

I den här självstudien får du lära dig hur du utför analys av exempel data genom att kombinera olika Azure Open-datauppsättningar med hjälp av SQL-poolen utan server och visualisera resultaten i Azure Synapse Studio.

I synnerhet analyseras den [nya taxi-datauppsättningen Göteborg (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) som innehåller:

- Upphämtnings-och utgångs datum och tidpunkter.
- Hämta och släppa platser. 
- Rese avstånd.
- Specificerade priser.
- Pris typer.
- Betalnings typer. 
- Driv rutin – rapporterade person antal.

## <a name="automatic-schema-inference"></a>Automatisk schema härledning

Eftersom data lagras i fil formatet Parquet, är automatisk schema härledning tillgängligt. Du kan enkelt fråga data utan att lista data typerna för alla kolumner i filerna. Du kan också använda den virtuella kolumnens mekanism och funktionen Sök väg för att filtrera bort en del av filerna.

Låt oss först bekanta dig med NYC taxi-data genom att köra följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Följande fragment visar resultatet för NYC taxi-data:

![NYC taxi data resultat-kodfragment](./media/tutorial-data-analyst/1.png)

På samma sätt kan du fråga den offentliga helgdags data uppsättningen med hjälp av följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Följande fragment visar resultatet för den offentliga helgdags data uppsättningen:

![Resultat avsnitt för data uppsättning för offentliga helgdagar](./media/tutorial-data-analyst/2.png)

Slutligen kan du även fråga data uppsättningen för väder data med hjälp av följande fråga:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Följande kodfragment visar resultatet för data uppsättningen väder data:

![Resultat avsnitt för data uppsättning för väder data](./media/tutorial-data-analyst/3.png)

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

Följande kodfragment visar resultatet för det årliga antalet taxi-åsidosättningar:

![Årligt antal taxi-åsidosättningar resultat-kodfragment](./media/tutorial-data-analyst/4.png)

Data kan visualiseras i Synapse Studio genom att växla från **tabellen** till **diagramvyn** . Du kan välja bland olika diagram typer, till exempel **yt**-, **stapel**-, **kolumn**-, **linje**-, **cirkel**-och **punkt** diagram. I det här fallet ska du rita **stapeldiagrammet** med **kategori** kolumnen inställt på **current_year**:

![Stapeldiagram med val per år](./media/tutorial-data-analyst/5.png)

I den här visualiseringen kan en tendens av ett minskande antal över år visas tydligt. Den här minskningen beror förmodligen på den senaste ökande populariteten av delnings företag.

> [!NOTE]
> Vid tidpunkten för den här självstudien är data för 2019 ofullständiga. Det innebär att det är mycket viktigt att ta hänsyn till antalet åsidosättningar för det året.

Nu ska vi fokusera på att analysera ett enda år, till exempel 2016. Följande fråga returnerar det dagliga antalet åsidosättningar under det året:

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

Följande fragment visar resultatet för den här frågan:

![Dagligt antal åsidosättningar för 2016 resultat-kodfragment](./media/tutorial-data-analyst/6.png)

Återigen kan du enkelt visualisera data genom att rita **stapeldiagrammet** med **kategori** kolumn uppsättningen till **current_day** och kolumnen **förklaring (serie)** är inställd på **rides_per_day**.

![Stapeldiagram som visar det dagliga antalet åsidosättningar för 2016](./media/tutorial-data-analyst/7.png)

I diagrammet kan du se att det finns ett vecko mönster, med lördagar som topp dag. Under sommaren månader finns det färre taxi-åsidosättningar på grund av semester. Lägg också märke till några betydande droppar i antalet taxi-åsidosättningar utan tydliga mönster för när och varför de inträffar.

Nu ska vi se om det går att korrelera med offentliga helgdagar genom att ansluta till NYC taxi-uppsättningen med offentliga helgdagar:

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

![NYC taxi-uppsättningar och offentliga helgdagar data uppsättningar resultat visualisering](./media/tutorial-data-analyst/8.png)

Den här gången vill vi belysa antalet taxi-ändringar under offentliga helgdagar. För det syftet väljer vi **ingen** för kolumnen **kategori** och **rides_per_day** och **helgdag** som **förklarings kolumner (serie)** .

![Antal taxi-undantag under offentliga helgdagar diagram](./media/tutorial-data-analyst/9.png)

I diagrammet kan du se att antalet taxi-åsidosättningar är lägre under offentliga helgdagar. Det finns fortfarande en icke-förklarad stor minskning den 23 januari. Nu ska vi titta på väder i NYC den dagen genom att fråga data uppsättningen för väder data:

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

![Resultat visualisering för data uppsättning för väder data](./media/tutorial-data-analyst/10.png)

Resultatet av frågan tyder på att minskningen av antalet taxi-åsidosättningar inträffade på grund av följande:

- Det fanns en Blizzard på den dagen i NYC med tunga snö (~ 30 cm).
- Den var kall (temperatur var lägre än noll grader Celsius).
- Den var lindning (~ 10 m/s).

Den här självstudien visar hur en dataanalytiker snabbt kan utföra analys av analys av data, enkelt kombinera olika data uppsättningar med hjälp av SQL-poolen utan server och visualisera resultaten med hjälp av Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

Information om hur du ansluter en server lös SQL-pool för att Power BI Desktop och skapa rapporter finns i [ansluta Server lös SQL-pool till Power BI Desktop och skapa rapporter](tutorial-connect-power-bi-desktop.md).
 
