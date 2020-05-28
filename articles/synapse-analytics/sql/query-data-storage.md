---
title: Översikt – fråga efter data i lagring med SQL på begäran (för hands version)
description: Det här avsnittet innehåller exempel frågor som du kan använda för att testa resursen SQL on-demand (för hands version) i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118272"
---
# <a name="overview-query-data-in-storage"></a>Översikt: fråga efter data i lagring

Det här avsnittet innehåller exempel frågor som du kan använda för att testa resursen SQL on-demand (för hands version) i Azure Synapse Analytics.
Fil format som stöds för närvarande:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Förutsättningar

De verktyg du behöver för att utfärda frågor:

- Valfri SQL-klient:
    - Azure Synapse Studio (för hands version)
    - Azure Data Studio
    - SQL Server Management Studio

Dessutom är parametrarna följande:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slut punkts adress för SQL-tjänst på begäran    | Kommer att användas som server namn.                                   |
| Tjänstens slut punkts region för SQL på begäran     | Kommer att användas för att avgöra vilken lagring som används i exemplen. |
| Användar namn och lösen ord för slut punkts åtkomst | Kommer att användas för att få åtkomst till slut punkten.                               |
| Den databas som du ska använda för att skapa vyer     | Den här databasen kommer att användas som utgångs punkt för exemplen.       |

## <a name="first-time-setup"></a>Installation vid första tiden

Ditt första steg är att **skapa en databas** där du ska köra frågorna. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används för att läsa data i dessa exempel.

> [!NOTE]
> Databaser används bara för att visa metadata, inte för faktiska data.  Skriv ned det databas namn du använder, men du behöver det senare.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Tillhandahållna demonstrations data

Demonstrations data innehåller följande data uppsättningar:

- NYC taxi – gul taxi resa-en del av en offentlig NYC-data uppsättning
  - CSV-format
  - Parquet-format
- Populations data uppsättning
  - CSV-format
- Testa Parquet-filer med kapslade kolumner
  - Parquet-format
- Books-JSON
  - JSON-format

| Mappsökväg                                                  | Beskrivning                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| SKV                                                        | Överordnad mapp för data i CSV-format                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Mappar med populations data filer i olika CSV-format. |
| /csv/taxi/                                                   | Mapp med NYC offentliga datafiler i CSV-format              |
| Parquet                                                    | Överordnad mapp för data i Parquet-format                     |
| /parquet/taxi                                                | NYC offentliga datafiler i Parquet-format, partitionerade efter år och månad med Hive/Hadoop-partitionerings schema. |
| /parquet/nested/                                             | Testa Parquet-filer med kapslade kolumner                     |
| utgör                                                       | Överordnad mapp för data i JSON-format                        |
| /json/books/                                                 | JSON-filer med data från böcker                                   |

### <a name="sample-query"></a>Exempelfråga

Det sista steget i verifieringen är att köra följande fråga:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

Ovanstående fråga ska returnera det här talet: **8945574**.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att fortsätta med följande artiklar:

- [Fråga en enkel CSV-fil](query-single-csv-file.md)

- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)

- [Fråga efter vissa filer](query-specific-files.md)

- [Efterfråga Parquet-filer](query-parquet-files.md)

- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)

- [Efterfråga JSON-filer](query-json-files.md)

- [Skapa och använda vyer](create-use-views.md)
