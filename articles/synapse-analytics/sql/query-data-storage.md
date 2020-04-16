---
title: Översikt - Frågedata i lagring med SQL on-demand (förhandsgranskning)
description: Det här avsnittet innehåller exempelfrågor som du kan använda för att prova SQL on-demand (preview) -resursen i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424904"
---
# <a name="overview-query-data-in-storage"></a>Översikt: Fråga data i lagring

Det här avsnittet innehåller exempelfrågor som du kan använda för att prova SQL on-demand (preview) -resursen i Azure Synapse Analytics.
Filer som stöds är för närvarande: 
- CSV
- Parkett
- JSON

## <a name="prerequisites"></a>Krav

De verktyg du behöver för att utfärda frågor:

- VALFRI SQL-klient:
    - Azure Synapse Studio (förhandsversion)
    - Azure Data Studio
    - SQL Server Management Studio

Dessutom är parametrarna följande:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slutpunktsadress för SQL on-demand-tjänst    | Kommer att användas som servernamn.                                   |
| Slutpunktsregion för SQL-tjänst på begäran     | Kommer att användas för att bestämma den lagring som används i proverna. |
| Användarnamn och lösenord för åtkomst till slutpunkter | Kommer att användas för att komma åt slutpunkten.                               |
| Databasen som du ska använda för att skapa vyer     | Databasen används som utgångspunkt för exemplen.       |

## <a name="first-time-setup"></a>Första gången setup

Innan du använder exemplen som ingår senare i den här artikeln har du två steg:

- Skapa en databas för dina vyer (om du vill använda vyer)
- Skapa autentiseringsuppgifter som ska användas av SQL på begäran för att komma åt filerna i lagring

### <a name="create-database"></a>Skapa databas

Du behöver en databas för att skapa vyer. Du ska använda den här databasen för några av exempelfrågorna i den här dokumentationen.

> [!NOTE]
> Databaser används endast för att visa metadata, inte för faktiska data.  Skriv ner databasnamnet som du använder, du behöver det senare.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Du måste skapa autentiseringsuppgifter innan du kan köra frågor. Den här autentiseringsuppgifterna används av SQL on-demand-tjänsten för att komma åt filerna i lagring.

> [!NOTE]
> För att kunna köra How To's i det här avsnittet måste du använda SAS-token.
>
> För att börja använda SAS-token måste du släppa UserIdentity som förklaras i följande [artikel](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL on-demand som standard använder alltid AAD-vidaregenomströmning.

Mer information om hur du hanterar lagringsåtkomstkontroll finns i den här [länken](develop-storage-files-storage-access-control.md).

> [!WARNING]
> Du måste skapa autentiseringsuppgifter för ett lagringskonto som finns i slutpunktsregionen. Även om SQL on-demand kan komma åt lagringar från olika regioner, kommer lagring och slutpunkt i samma region att ge en bättre prestandaupplevelse.

Om du vill skapa autentiseringsuppgifter för CSV-, JSON- och Parkettbehållare kör du koden nedan:

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

## <a name="provided-demo-data"></a>Förutsatt demodata

Demodata innehåller följande datauppsättningar:

- NYC Taxi - Yellow Taxi Trip Records - en del av offentliga NYC data som
  - CSV-format
  - Parquet-format
- Befolkningsdatauppsättning
  - CSV-format
- Exempel på parkettfiler med kapslade kolumner
  - Parquet-format
- Böcker JSON
  - JSON-format

| Mappsökväg                                                  | Beskrivning                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Överordnad mapp för data i CSV-format                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-citerad | Mappar med populationsdatafiler i olika CSV-format. |
| /csv/taxi/                                                   | Mapp med offentliga NYC-datafiler i CSV-format              |
| /parkett/                                                    | Överordnad mapp för data i parkettformat                     |
| /parkett/taxi                                                | NYC offentliga datafiler i parquet format, partitionerad efter år, och månad med Hive / Hadoop partitionering system. |
| /parkett/kapslade/                                             | Exempel på parkettfiler med kapslade kolumner                     |
| /json/                                                       | Överordnad mapp för data i JSON-format                        |
| /json/böcker/                                                 | JSON filer med böcker data                                   |

## <a name="validation"></a>Validering

Kör följande tre frågor och kontrollera om autentiseringsuppgifterna skapas korrekt.

> [!NOTE]
> Alla URI:er i exempelfrågorna använder ett lagringskonto i Azure-regionen i Norra Europa. Kontrollera att du har skapat rätt autentiseringsuppgifter. Kör frågan nedan och se till att lagringskontot visas.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Om du inte hittar rätt autentiseringsuppgifter kontrollerar du [första gången.](#first-time-setup)

### <a name="sample-query"></a>Exempelfråga

Det sista steget i valideringen är att köra följande fråga:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

Ovanstående fråga ska returnera detta nummer: **8945574**.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fortsätta med följande How To articles:

- [Fråga en enda CSV-fil](query-single-csv-file.md)

- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)

- [Fråga specifika filer](query-specific-files.md)

- [Fråga parquet-filer](query-parquet-files.md)

- [Frågor Parkett kapslade typer](query-parquet-nested-types.md)

- [Fråga JSON-filer](query-json-files.md)

- [Skapa och använda vyer](create-use-views.md)
