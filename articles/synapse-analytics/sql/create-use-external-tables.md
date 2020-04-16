---
title: Skapa och använda externa tabeller i SQL på begäran (förhandsgranskning)
description: I det här avsnittet får du lära dig hur du skapar och använder externa tabeller i SQL på begäran (förhandsgranskning). Externa tabeller är användbara när du vill styra åtkomsten till externa data i SQL On-demand och om du vill använda verktyg, till exempel Power BI, tillsammans med SQL on-demand.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424554"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Skapa och använda externa tabeller i SQL on-demand (förhandsversion) med Azure Synapse Analytics

I det här avsnittet får du lära dig hur du skapar och använder externa tabeller i SQL på begäran (förhandsgranskning). Externa tabeller är användbara när du vill styra åtkomsten till externa data i SQL On-demand och om du vill använda verktyg, till exempel Power BI, tillsammans med SQL on-demand.

## <a name="prerequisites"></a>Krav

Ditt första steg är att granska artiklarna nedan och se till att du har uppfyllt förutsättningarna för att skapa och använda sql on-demand externa tabeller:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Skapa en extern tabell

Du kan skapa externa tabeller på samma sätt som du skapar vanliga externa SQL Server-tabeller. Frågan nedan skapar en extern tabell som läser *population.csv-fil.*

> [!NOTE]
> Ändra den första raden i frågan, dvs [mydbname], så att du använder databasen du skapade. Om du inte har skapat en databas läser du [Första gången.](query-data-storage.md#first-time-setup)

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Använda en extern tabell

Du kan använda externa tabeller i dina frågor på samma sätt som du använder dem i SQL Server-frågor.

Följande fråga visas med hjälp av *den externa populationstabellen* som vi skapade i Skapa ett [externt tabellavsnitt.](#create-an-external-table) Landet returnerar landsnamn med sin befolkning 2019 i fallande ordning.

> [!NOTE]
> Ändra den första raden i frågan, dvs [mydbname], så att du använder databasen du skapade. Om du inte har skapat en databas läser du [Första gången.](query-data-storage.md#first-time-setup)

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Nästa steg

Information om hur du lagrar resultat av en fråga till lagringen finns i [store-frågeresultaten till lagringen](../sql/create-external-table-as-select.md).
