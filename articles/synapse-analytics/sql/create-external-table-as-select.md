---
title: Spara frågeresultat till lagringsplats
description: I den här artikeln får du lära dig hur du lagrar frågeresultat till lagring med SQL på begäran (för hands version).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cbf6d42f3b1d130a6bf89f07bd3a7009ff0e8fa8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647518"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Lagra frågeresultat till lagring med SQL på begäran (för hands version) med Azure Synapse Analytics

I den här artikeln får du lära dig hur du lagrar frågeresultat till lagring med SQL på begäran (för hands version).

## <a name="prerequisites"></a>Krav

Ditt första steg är att granska artiklarna nedan och se till att du uppfyller kraven:

- [Installation vid första tiden](query-data-storage.md#first-time-setup)
- [Förutsättningar](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Skapa extern tabell som Välj

Du kan använda instruktionen skapa extern tabell som SELECT (CETAS) för att lagra frågeresultatet till Storage.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat. Om du inte har skapat en databas läser du [installations programmet för första gången](query-data-storage.md#first-time-setup). Du måste ändra plats för den externa data källan för data källan för att kunna peka på platsen som du har Skriv behörighet för. 

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>Använd den externa tabellen

Du kan använda den externa tabellen som skapats via CETAS som en vanlig extern tabell.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat. Om du inte har skapat en databas läser du [installations programmet för första gången](query-data-storage.md#first-time-setup).

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

Mer information om hur du frågar olika filtyper finns i artikeln [fråga en enkel CSV-fil](query-single-csv-file.md), [fråga Parquet-filer](query-parquet-files.md)och [fråga JSON-filer](query-json-files.md) .
