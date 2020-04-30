---
title: Använda SQL på begäran (för hands version)
description: I den här snabb starten får du se och lära dig hur enkelt det är att fråga olika typer av filer med hjälp av SQL på begäran (för hands version).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d49918fc67a45419e5c7ca123642c48e689a1496
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113790"
---
# <a name="quickstart-using-sql-on-demand"></a>Snabb start: använda SQL på begäran

Synapse SQL on-demand (för hands version) är en server lös fråga som gör att du kan köra SQL-frågorna på dina filer som placerats i Azure Storage. I den här snabb starten får du lära dig hur du frågar olika typer av filer med SQL på begäran.

Följande filtyper stöds: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Krav

Välj en SQL-klient för att utfärda frågor:

- [Azure Synapse Studio](quickstart-synapse-studio.md) är ett webb verktyg som du kan använda för att söka efter filer i lagring och skapa SQL-frågor.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) är ett klient verktyg som gör att du kan köra SQL-frågor och antecknings böcker på din databas på begäran.
- [SQL Server Management Studio](sql/get-started-ssms.md) är ett klient verktyg som gör att du kan köra SQL-frågor på din databas på begäran.

Parametrar för snabb start:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slut punkts adress för SQL-tjänst på begäran    | Används som server namn                                   |
| Tjänstens slut punkts region för SQL på begäran     | Används för att avgöra vilken lagrings enhet som ska användas i exempel |
| Användar namn och lösen ord för slut punkts åtkomst | Används för att komma åt slut punkten                               |
| Databasen som används för att skapa vyer         | Databas som används som start punkt i exempel       |

## <a name="first-time-setup"></a>Installation vid första tiden

Innan du använder exempel:

- Skapa databas för dina vyer (om du vill använda vyer)
- Skapa autentiseringsuppgifter som ska användas av SQL på begäran för att komma åt filer i lagringen

### <a name="create-database"></a>Skapa databas

Skapa en egen databas för demonstrations syfte. Det här är databasen där du skapar dina vyer. Använd den här databasen i exempel frågorna i den här artikeln.

> [!NOTE]
> Databaserna används bara för att visa metadata, inte för faktiska data.
>
> Skriv ned databas namn som du använder för senare användning i snabb starten.

Använd följande fråga och ändra `mydbname` till önskat namn:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Om du vill köra frågor med SQL på begäran skapar du autentiseringsuppgifter för SQL på begäran för att komma åt filer i lagringen.

> [!NOTE]
> För att kunna köra exempel i det här avsnittet måste du använda SAS-token.
>
> Om du vill börja använda SAS-tokens måste du släppa UserIdentity som beskrivs i följande [artikel](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL på begäran som standard använder alltid AAD-vidarekoppling.

Mer information om hur du hanterar åtkomst kontroll för lagring, finns i den här [länken](sql/develop-storage-files-storage-access-control.md).

Kör följande kodfragment för att skapa autentiseringsuppgifter som används i exempel i det här avsnittet:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Fråga CSV-filer

Följande bild är en förhands granskning av den fil som ska frågas:

![De första 10 raderna i CSV-filen utan sidhuvud, ny rad i Windows-format.](./sql/media/query-single-csv-file/population.png)

Följande fråga visar hur du läser en CSV-fil som inte innehåller någon rubrik rad, med ny rad med Windows-typ och kommaavgränsade kolumner:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Du kan ange schema vid tid för kompilering av frågor.
Fler exempel finns i [fråga CSV-fil](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Fråga Parquet-filer

I följande exempel visas de automatiska schema härlednings funktionerna för att fråga Parquet-filer. Det returnerar antalet rader i september 2017 utan att ange schema.

> [!NOTE]
> Du behöver inte ange columns i `OPENROWSET WITH` -satsen när du läser Parquet-filer. I så fall använder SQL på begäran metadata i Parquet-filen och bind kolumner efter namn.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Hitta mer information om att [fråga Parquet-filer](sql/query-parquet-files.md)].

## <a name="querying-json-files"></a>Fråga JSON-filer

### <a name="json-sample-file"></a>JSON-exempel fil

Filer lagras i *JSON* *-behållare, mappträdet och*innehåller en post i en bok med följande struktur:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="querying-json-files"></a>Fråga JSON-filer

Följande fråga visar hur du använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta skalära värden (title, Publisher) från en bok med rubriken *Probabilistic och statistiska metoder i Cryptology, en introduktion av valda artiklar*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Vi läser hela JSON-filen som enskild rad/kolumn så att FIELDTERMINATOR, FIELDQUOTE och ROWTERMINATOR är inställda på 0x0B eftersom vi inte förväntar sig att hitta den i filen.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja med följande snabb starts artiklar:

- [Fråga en enkel CSV-fil](sql/query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](sql/query-folders-multiple-csv-files.md)
- [Fråga efter vissa filer](sql/query-specific-files.md)
- [Efterfråga Parquet-filer](sql/query-parquet-files.md)
- [Efterfråga kapslade Parquet-typer](sql/query-parquet-nested-types.md)
- [Efterfråga JSON-filer](sql/query-json-files.md)
- [Skapa och använda vyer](sql/create-use-views.md)
- [Skapa och använda externa tabeller](sql/create-use-external-tables.md)
- [Behåll frågeresultat till Azure Storage](sql/create-external-table-as-select.md)

Gå vidare till nästa artikel om du vill lära dig att fråga en enskild CSV-fil.
> [!div class="nextstepaction"]
> [Fråga en enkel CSV-fil](sql/query-single-csv-file.md)
