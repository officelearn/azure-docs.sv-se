---
title: Skapa och Använd vyer i SQL på begäran (för hands version)
description: I det här avsnittet får du lära dig hur du skapar och använder vyer för att packa upp SQL on-demand-frågor (för hands version). Med vyer kan du återanvända dessa frågor. Vyer behövs också om du vill använda verktyg, till exempel Power BI, tillsammans med SQL på begäran.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 508c12d89d5b0476e4cd4cbd8dc43b8bda8fbd90
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647471"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Skapa och Använd vyer i SQL på begäran (för hands version) med Azure Synapse Analytics

I det här avsnittet får du lära dig hur du skapar och använder vyer för att packa upp SQL on-demand-frågor (för hands version). Med vyer kan du återanvända dessa frågor. Vyer behövs också om du vill använda verktyg, till exempel Power BI, tillsammans med SQL på begäran.

## <a name="prerequisites"></a>Krav

Ditt första steg är att skapa en databas där vyn kommer att skapas och initiera de objekt som behövs för att autentisera i Azure Storage genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) på den databasen. Alla frågor i den här artikeln utförs i exempel databasen.

## <a name="create-a-view"></a>Skapa en vy

Du kan skapa vyer på samma sätt som du skapar vanliga SQL Server vyer. I följande fråga skapas en vy som läser filen med *ifyllning. csv* .

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
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
) AS [r];
```

Vyn i det här exemplet använder `OPENROWSET` funktionen som använder absoluta sökvägar till de underliggande filerna. Om du har `EXTERNAL DATA SOURCE` en rot-URL för lagringen kan du använda `OPENROWSET` med `DATA_SOURCE` och relativ fil Sök väg:

```
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemandstorage',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Använda en vy

Du kan använda vyer i dina frågor på samma sätt som du använder vyer i SQL Server frågor.

Följande fråga visar hur du använder *population_csv* vy som vi skapade i [skapa en vy](#create-a-view). Den returnerar lands namn med sin population i 2019 i fallande ordning.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Nästa steg

Information om hur du frågar olika filtyper finns i artikeln [fråga en enkel CSV-fil](query-single-csv-file.md), [fråga Parquet-filer](query-parquet-files.md)och [fråga JSON-filer](query-json-files.md) .
