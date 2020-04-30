---
title: Skapa och Använd externa tabeller i SQL på begäran (för hands version)
description: I det här avsnittet får du lära dig hur du skapar och använder externa tabeller i SQL på begäran (för hands version). Externa tabeller är användbara när du vill kontrol lera åtkomsten till externa data i SQL på begäran och om du vill använda verktyg, till exempel Power BI, tillsammans med SQL på begäran.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424554"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Skapa och Använd externa tabeller i SQL på begäran (för hands version) med Azure Synapse Analytics

I det här avsnittet får du lära dig hur du skapar och använder externa tabeller i SQL på begäran (för hands version). Externa tabeller är användbara när du vill kontrol lera åtkomsten till externa data i SQL på begäran och om du vill använda verktyg, till exempel Power BI, tillsammans med SQL på begäran.

## <a name="prerequisites"></a>Krav

Ditt första steg är att granska artiklarna nedan och kontrol lera att du har uppfyllt kraven för att skapa och använda SQL på begäran-externa tabeller:

- [Installation vid första tiden](query-data-storage.md#first-time-setup)
- [Förutsättningar](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Skapa en extern tabell

Du kan skapa externa tabeller på samma sätt som du skapar vanliga SQL Server externa tabeller. Frågan nedan skapar en extern tabell som läser en *populations. csv* -fil.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat. Om du inte har skapat en databas läser du [installations programmet för första gången](query-data-storage.md#first-time-setup).

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

## <a name="use-a-external-table"></a>Använd en extern tabell

Du kan använda externa tabeller i dina frågor på samma sätt som du använder dem i SQL Server frågor.

Följande fråga visar hur du använder den externa *populations* tabellen som vi skapade i avsnittet [skapa en extern tabell](#create-an-external-table) . Den returnerar lands namn med sin population i 2019 i fallande ordning.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat. Om du inte har skapat en databas läser du [installations programmet för första gången](query-data-storage.md#first-time-setup).

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

Information om hur du lagrar resultat från en fråga till lagringen hittar du i [butiks frågeresultaten till lagringen](../sql/create-external-table-as-select.md).
