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
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116255"
---
# <a name="overview-query-data-in-storage"></a>Översikt: fråga efter data i lagring

Det här avsnittet innehåller exempel frågor som du kan använda för att testa resursen SQL on-demand (för hands version) i Azure Synapse Analytics.
Filer som stöds för närvarande är: 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Krav

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

Innan du använder exemplen som ingår längre fram i den här artikeln har du två steg:

- Skapa en databas för dina vyer (om du vill använda vyer)
- Skapa autentiseringsuppgifter som ska användas av SQL på begäran för att komma åt filerna i lagringen

### <a name="create-database"></a>Skapa databas

Du behöver en databas för att skapa vyer. Du använder den här databasen för några av exempel frågorna i den här dokumentationen.

> [!NOTE]
> Databaser används bara för att visa metadata, inte för faktiska data.  Skriv ned det databas namn du använder, men du behöver det senare.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Du måste skapa autentiseringsuppgifter innan du kan köra frågor. Den här autentiseringsuppgiften används av SQL-tjänsten på begäran för att komma åt filerna i lagrings utrymmet.

> [!NOTE]
> För att kunna köra det här avsnittet måste du använda SAS-token.
>
> Om du vill börja använda SAS-tokens måste du släppa UserIdentity som beskrivs i följande [artikel](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL på begäran som standard använder alltid AAD-vidarekoppling.

Mer information om hur du hanterar åtkomst kontroll för lagring, finns i den här [länken](develop-storage-files-storage-access-control.md).

Om du vill skapa autentiseringsuppgifter för CSV-, JSON-och Parquet-behållare kör du koden nedan:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>Validering

Kör följande tre frågor och kontrol lera om autentiseringsuppgifterna har skapats korrekt.

> [!NOTE]
> Alla URI: er i exempel frågorna använder ett lagrings konto som finns i regionen Nord Europa Azure. Se till att du har skapat rätt autentiseringsuppgifter. Kör frågan nedan och kontrol lera att lagrings kontot finns med i listan.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Om du inte kan hitta rätt autentiseringsuppgifter, kontrol lera [inställningen vid första tidpunkten](#first-time-setup).

### <a name="sample-query"></a>Exempelfråga

Det sista steget i verifieringen är att köra följande fråga:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
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
