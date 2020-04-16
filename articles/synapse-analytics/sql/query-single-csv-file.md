---
title: Fråga CSV-filer med SQL på begäran (förhandsgranskning)
description: I den här artikeln får du lära dig att fråga enstaka CSV-filer med olika filformat med SQL on-demand (preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431597"
---
# <a name="query-csv-files"></a>Fråga CSV-filer

I den här artikeln får du lära dig att fråga en enda CSV-fil med SQL on-demand (preview) i Azure Synapse Analytics. CSV-filer kan ha olika format: 

- Med och utan rubrikrad
- Komma- och flikavgränsade värden
- Radslut i Windows- och Unix-format
- Icke-citerade och citerade värden och utrymda tecken

Alla ovanstående variationer kommer att täckas nedan.

## <a name="prerequisites"></a>Krav

Innan du läser resten av den här artikeln, granska följande artiklar:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Ny linje i Windows-format

Följande fråga visar hur du läser en CSV-fil utan rubrikrad, med en ny rad i Windows-stil och kommaavgränsade kolumner.

Förhandsgranskning av filer:

![Första 10 raderna i CSV-filen utan rubrik, Windows stil ny rad.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
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

## <a name="unix-style-new-line"></a>Ny linje i Unix-stil

Följande fråga visar hur du läser en fil utan rubrikrad, med en ny linje med Unix-format och kommaavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Förhandsgranskning av filer:

![Första 10 raderna i CSV-filen utan rubrikrad och med ny rad i Unix-format.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
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

## <a name="header-row"></a>Rubrikraden

Följande fråga visar hur du gör en läst fil med en rubrikrad, med en ny linje i Unix-format och kommaavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Förhandsgranskning av filer:

![Första 10 raderna i CSV-filen med rubrikrad och med Ny rad i Unix-format.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
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

## <a name="custom-quote-character"></a>Anpassat citattecken

Följande fråga visar hur du läser en fil med en rubrikrad, med en ny rad i Unix-format, kommaavgränsade kolumner och citerade värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Förhandsgranskning av filer:

![De första 10 raderna i CSV-filen med rubrikrad och med ny rad i Unix-stil och citerade värden.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
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
> Den här frågan returnerar samma resultat om du utelämnade PARAMETERN FIELDQUOTE eftersom standardvärdet för FIELDQUOTE är en dubbeloffert.

## <a name="escaping-characters"></a>Fly tecken

Följande fråga visar hur du läser en fil med en rubrikrad, med en ny rad av Unix-format, kommaavgränsade kolumner och en escape-tecken som används för fältavgränsaren (komma) i värdena. Observera den olika platsen för filen jämfört med de andra exemplen.

Förhandsgranskning av filer:

![Första 10 raderna i CSV-filen med rubrikrad och med Unix-Style ny rad och escape-tecken som används för fält avgränsare.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
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
    country_name = 'Slov,enia';
```

> [!NOTE]
> Den här frågan skulle misslyckas om ESCAPECHAR inte anges eftersom kommatecknet i "Slov,enia" skulle behandlas som fält avgränsare i stället för en del av landsnamnet. "Slov,enia" skulle behandlas som två kolumner. Därför skulle den aktuella raden ha en kolumn mer än de andra raderna och en kolumn mer än du definierade i WITH-satsen.

## <a name="tab-delimited-files"></a>Tabbavgränsade filer

Följande fråga visar hur du läser en fil med en rubrikrad, med en ny linje med Unix-format och flikavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Förhandsgranskning av filer:

![Första 10 raderna i CSV-filen med rubrikrad och med Unix-style ny rad och flik avgränsare.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
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

## <a name="returning-subset-of-columns"></a>Returnera delmängd av kolumner

Hittills har du angett CSV-filschemat med MED och lista alla kolumner. Du kan bara ange kolumner som du faktiskt behöver i frågan med hjälp av ett ordningstal för varje kolumn som behövs. Du utelämnar också kolumner utan intresse.

Följande fråga returnerar antalet olika landsnamn i en fil och anger bara de kolumner som behövs:

> [!NOTE]
> Ta en titt på WITH-satsen i frågan nedan och notera att det finns "2" (utan citattecken) i slutet av raden där du definierar kolumnen *[country_name].* Det betyder att kolumnen *[country_name]* är den andra kolumnen i filen. Frågan ignorerar alla kolumner i filen utom den andra.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Nästa steg

Nästa artiklar visar hur du:

- [Fråga parettfiler](query-parquet-files.md)
- [Fråga mappar och flera filer](query-folders-multiple-csv-files.md)
