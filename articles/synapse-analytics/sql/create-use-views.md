---
title: Skapa och använda vyer i SQL på begäran (förhandsgranskning)
description: I det här avsnittet får du lära dig hur du skapar och använder vyer för att radbrytas frågor om SQL on-demand (preview). Med vyer kan du återanvända dessa frågor. Vyer behövs också om du vill använda verktyg, till exempel Power BI, tillsammans med SQL on-demand.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424540"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Skapa och använda vyer i SQL on-demand (förhandsversion) med Azure Synapse Analytics

I det här avsnittet får du lära dig hur du skapar och använder vyer för att radbrytas frågor om SQL on-demand (preview). Med vyer kan du återanvända dessa frågor. Vyer behövs också om du vill använda verktyg, till exempel Power BI, tillsammans med SQL on-demand.

## <a name="prerequisites"></a>Krav

Ditt första steg är att granska artiklarna nedan och se till att du har uppfyllt förutsättningarna för att skapa och använda SQL on-demand-vyer:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Skapa en vy

Du kan skapa vyer på samma sätt som du skapar vanliga SQL Server-vyer. Frågan nedan skapar vy som läser *population.csv-filen.*

> [!NOTE]
> Ändra den första raden i frågan, dvs [mydbname], så att du använder databasen du skapade. Om du inte har skapat en databas läser du [Första gången.](query-data-storage.md#first-time-setup)

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

## <a name="use-a-view"></a>Använda en vy

Du kan använda vyer i dina frågor på samma sätt som du använder vyer i SQL Server-frågor.

Följande fråga visas med hjälp av *den population_csv* vy som vi skapade i Skapa [en vy](#create-a-view). Landet returnerar landsnamn med sin befolkning 2019 i fallande ordning.

> [!NOTE]
> Ändra den första raden i frågan, dvs [mydbname], så att du använder databasen du skapade. Om du inte har skapat en databas läser du [Första gången.](query-data-storage.md#first-time-setup)

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

Information om hur du frågar olika filtyper finns i artiklarna [Fråga en enda CSV-fil,](query-single-csv-file.md) [Frågeparkettfiler](query-parquet-files.md)och [Fråge-JSON-filer.](query-json-files.md)
