---
title: Fråga CSV-filer med SQL på begäran (för hands version)
description: I den här artikeln får du lära dig att fråga enkla CSV-filer med olika fil format med hjälp av SQL på begäran (för hands version).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 628631fb7fddbc07dcb865e3d3badbfb608ad097
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214459"
---
# <a name="query-csv-files"></a>Fråga CSV-filer

I den här artikeln får du lära dig hur du frågar en enkel CSV-fil med SQL på begäran (för hands version) i Azure Synapse Analytics. CSV-filer kan ha olika format: 

- Med och utan en rubrik rad
- Kommatecken och Tabbavgränsade värden
- Windows-och UNIX-format rads slut
- Värden som inte är citerade och citerade och som överlappar tecken

Alla ovanstående variationer kommer att täckas av nedan.

## <a name="prerequisites"></a>Krav

Ditt första steg är att **skapa en databas** där tabellerna ska skapas. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="windows-style-new-line"></a>Ny rad i Windows-format

Följande fråga visar hur du läser en CSV-fil utan en rubrik rad, med en ny rad med Windows-typ och kommaavgränsade kolumner.

Fil för hands version:

![De första 10 raderna i CSV-filen utan sidhuvud, ny rad i Windows-format.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Ny rad med UNIX-format

Följande fråga visar hur du läser en fil utan en rubrik rad, med en ny rad med UNIX-format och kommaavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen utan rubrik rad och med ny rad UNIX-typ.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Rubrik rad

Följande fråga visar hur du kan läsa en fil med en rubrik rad, med en ny rad med UNIX-typ och kommaavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med ny rad UNIX-typ.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Anpassat citat tecken

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-format, kommaavgränsade kolumner och offererade värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med nya rad-och offererade värden i UNIX-format.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Den här frågan returnerar samma resultat om du utelämnade parametern FIELDQUOTE eftersom standardvärdet för FIELDQUOTE är ett dubbelt citat tecken.

## <a name="escaping-characters"></a>Undantagna tecken

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-typ, kommaavgränsade kolumner och ett escape-tecken som används för fält avgränsare (komma) i värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med ny rad och escape-tecken i UNIX-format som används för fält avgränsare.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Den här frågan kan inte utföras om ESCAPECHAR inte anges eftersom kommatecknet i "slov, enia" behandlas som fält avgränsare i stället för en del av landets/regionens namn. "Slov, enia" behandlas som två kolumner. Därför skulle den aktuella raden ha en kolumn mer än de andra raderna, och en kolumn mer än du definierade i WITH-satsen.

### <a name="escaping-quoting-characters"></a>Hoppar över citat tecken

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-format, kommaavgränsade kolumner och ett escapeed Double citat tecken inom värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-format, kommaavgränsade kolumner och ett escapeed Double citat tecken inom värden.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Citat tecken måste föregås av ett annat citat tecken. Citat tecken får bara förekomma i kolumn värden om värdet är kapslat med citat tecken.

## <a name="tab-delimited-files"></a>Tabbavgränsade filer

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-typ och tabbavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med en ny rad med UNIX-typ och avgränsare.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Returnerar delmängd av kolumner

Hittills har du angett CSV-filschemat med med och visar alla kolumner. Du kan bara ange kolumner som du faktiskt behöver i frågan genom att använda ett ordnings tal för varje kolumn som behövs. Du kommer också att utelämna kolumner utan intresse.

Följande fråga returnerar antalet distinkta lands-/region namn i en fil och anger bara de kolumner som behövs:

> [!NOTE]
> Ta en titt på WITH-satsen i frågan nedan och Observera att det finns "2" (utan citat tecken) i slutet av raden där du definierar kolumnen *[country_name]* . Det innebär att kolumnen *[country_name]* är den andra kolumnen i filen. Frågan kommer att ignorera alla kolumner i filen förutom den andra.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Nästa steg

I nästa artiklar visas hur du:

- [Fråga Parquet-filer](query-parquet-files.md)
- [Fråga mappar och flera filer](query-folders-multiple-csv-files.md)
