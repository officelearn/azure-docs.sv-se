---
title: Självstudiekurs för dataanalytiker – Använd SQL on-demand (förhandsversion) för att analysera Azure Open Datasets i Azure Synapse Studio (förhandsversion)
description: I den här självstudien får du lära dig hur du enkelt utför utforskande dataanalys som kombinerar olika Azure Open Dataset med SQL on-demand (preview) och visualiserar resultaten i Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423231"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Använd SQL on-demand (förhandsversion) för att analysera Azure Open Dataset och visualisera resultaten i Azure Synapse Studio (förhandsversion)

I den här självstudien får du lära dig hur du utför utforskande dataanalys genom att kombinera olika Azure Open Dataset med SQL on-demand och sedan visualisera resultaten i Azure Synapse Studio.

I synnerhet analyserar du [New York City (NYC) Taxi-datauppsättningen](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) som inkluderar hämtnings- och avlämningsdatum/avlämningsdatum/-tider, hämtnings- och avlämningsplatser, reseavstånd, specificerade biljettpriser, pristyper, betalningstyper och förarrapporterade passagerarantal.

Fokus för analysen är att hitta trender i förändringar av antalet taxiresor över tiden. Du analyserar två andra Azure Open Datasets ([Helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) och [väderdata](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) för att förstå avvikare i antalet taxiresor.

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Automatisk schemaavferening

Eftersom data lagras i parquet filformat, automatisk schema inferens är tillgänglig, så man kan enkelt fråga data utan att behöva lista datatyper för alla kolumner i filerna. Dessutom kan man använda virtuell kolumn mekanism och filpath funktion för att filtrera bort en viss delmängd av filer.

Låt oss först bekanta sig med NYC Taxi-data genom att köra följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Följande visar resultatet utdrag för NYC Taxi data:

![resultatutdrag](./media/tutorial-data-analyst/1.png)

På samma sätt kan vi fråga helgdagar datauppsättning med hjälp av följande fråga:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Följande visar resultatkodavsnittet för datauppsättningen för helgdagar:

![resultatutdrag 2](./media/tutorial-data-analyst/2.png)

Slutligen kan vi också fråga väderdatauppsättningen med hjälp av följande fråga:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Följande visar resultatkodavsnittet för väderdatauppsättningen:

![resultatutdrag 3](./media/tutorial-data-analyst/3.png)

Du kan läsa mer om innebörden av de enskilda kolumnerna i beskrivningarna av [NYC Taxi,](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) [Helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)och [Väderdatadatauppsättningar.](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Tidsserier, säsongsvariationer och avvikare analys

Du kan enkelt sammanfatta det årliga antalet taxiresor med följande fråga:

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

Följande visar resultatet utdrag för det årliga antalet taxiresor:

![resultatutdrag 4](./media/tutorial-data-analyst/4.png)

Data kan visualiseras i Synapse Studio genom att växla från tabell till diagramvy. Du kan välja mellan olika diagramtyper (Area, Bar, Column, Line, Pie och Scatter). I det här fallet ska vi rita stapeldiagram med kategorikolumn inställd på "current_year":

![resultatvisualisering 5](./media/tutorial-data-analyst/5.png)

Från denna visualisering, en trend av ett minskande antal rider under åren kan tydligt ses, förmodligen på grund av en nyligen ökad popularitet ride sharing företag.

> [!NOTE]
> I skrivande stund är data för 2019 ofullständiga, så det finns en enorm minskning av ett antal turer för det året.

Låt oss sedan fokusera vår analys på ett enda år, till exempel 2016. Följande fråga returnerar det dagliga antalet turer under det året:

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

Följande visar resultatkodavsnittet för den här frågan:

![resultatutdrag 6](./media/tutorial-data-analyst/6.png)

Återigen kan vi enkelt visualisera data genom att rita kolumndiagram med kategorikolumn "current_day" och Legend (serie) kolumn "rides_per_day".

![resultatvisualisering 7](./media/tutorial-data-analyst/7.png)

Från tomten kan det observeras att det finns ett veckomönster, med lördagens topp. Under sommarmånaderna är det färre taxiresor på grund av semesterperioden. Men det finns också några betydande minskningar i antalet taxiresor utan ett tydligt mönster när och varför de inträffar.

Därefter ska vi se om dessa droppar är potentiellt korrelerade med helgdagar genom att gå NYC taxiresor med helgdagar datauppsättning:

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

![resultatvisualisering 8](./media/tutorial-data-analyst/8.png)

Den här gången vill vi lyfta fram antalet taxiresor under helgdagar. För detta ändamål väljer vi "ingen" för kolumnkategori och "rides_per_day" och "semester" som kolumner i förklaring (serie).

![resultatvisualisering 9](./media/tutorial-data-analyst/9.png)

Från tomten kan man tydligt se att under helgdagar ett antal taxiresor är lägre. Det finns dock fortfarande en oförklarlig stor nedgång den 23 januari. Låt oss kontrollera vädret i NYC den dagen genom att fråga vädret datauppsättning:

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

![resultatvisualisering 10](./media/tutorial-data-analyst/10.png)

Resultatet av frågan visar att nedgången i ett antal taxiresor berodde på:

- snöstorm den dagen i NYC, eftersom det var tung snö (~ 30 cm)
- det var kallt (temperatur under noll grader Celsius)
- och blåsigt (~10m/s)

Den här självstudien har visat hur dataanalytiker snabbt kan utföra utforskande dataanalys, enkelt kombinera olika datauppsättningar med SQL on-demand och visualisera resultaten med Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

Läs [connect SQL on-demand till Power BI Desktop & skapa rapportartikel](tutorial-connect-power-bi-desktop.md) om du vill lära dig hur du ansluter SQL på begäran till Power BI Desktop och skapar rapporter.
 
