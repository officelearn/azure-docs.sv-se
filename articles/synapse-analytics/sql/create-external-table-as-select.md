---
title: Lagra frågeresultat till lagring
description: I den här artikeln får du lära dig hur du lagrar frågeresultat till lagring med SQL on-demand (preview).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425149"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Lagra frågeresultat till lagring med SQL on-demand (preview) med Azure Synapse Analytics

I den här artikeln får du lära dig hur du lagrar frågeresultat till lagring med SQL On-demand (preview).

## <a name="prerequisites"></a>Krav

Ditt första steg är att granska artiklarna nedan och se till att du har uppfyllt förutsättningarna:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Skapa extern tabell som markering

Du kan använda CREATE EXTERNAL TABLE AS SELECT (CETAS) för att lagra frågeresultaten till lagringen.

> [!NOTE]
> Ändra den första raden i frågan, dvs [mydbname], så att du använder databasen du skapade. Om du inte har skapat en databas läser du [Första gången.](query-data-storage.md#first-time-setup)

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Använda en extern tabell som skapats

Du kan använda extern tabell som skapats via CETAS som en vanlig extern tabell.

> [!NOTE]
> Ändra den första raden i frågan, dvs [mydbname], så att du använder databasen du skapade. Om du inte har skapat en databas läser du [Första gången.](query-data-storage.md#first-time-setup)

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Nästa steg

Information om hur du frågar olika filtyper finns i artiklarna [Fråga en enda CSV-fil,](query-single-csv-file.md) [Frågeparkettfiler](query-parquet-files.md)och [Fråge-JSON-filer.](query-json-files.md)
