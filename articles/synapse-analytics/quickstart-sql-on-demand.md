---
title: Använda SQL på begäran (förhandsgranskning)
description: I den här snabbstarten kommer du att se och lära dig hur enkelt är att fråga olika typer av filer med SQL on-demand (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0d543abc88c1e45f2c1f5503473d8e92566fc582
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457390"
---
# <a name="quickstart-using-sql-on-demand"></a>Snabbstart: Använda SQL on-demand

Synapse SQL on-demand (preview) är en serverlös frågetjänst som gör att du kan köra SQL-frågor på dina filer som placeras i Azure Storage. I den här snabbstarten får du lära dig att fråga olika typer av filer med SQL on-demand.

Följande filtyper stöds: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Krav

Välj en SQL-klient för att utfärda frågor:

- [Azure Synapse Studio](quickstart-synapse-studio.md) är ett webbverktyg som du kan använda för att bläddra bland filer i lagring och skapa SQL-fråga.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) är ett klientverktyg som gör att du kan köra SQL-frågor och anteckningsböcker på din on-demand-databas.
- [SQL Server Management Studio](sql/get-started-ssms.md) är ett klientverktyg som gör att du kan köra SQL-frågor på din on-demand-databas.

Parametrar för snabbstart:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slutpunktsadress för SQL on-demand-tjänst    | Används som servernamn                                   |
| Slutpunktsregion för SQL-tjänst på begäran     | Används för att avgöra vilken lagring vi ska använda i prover |
| Användarnamn och lösenord för åtkomst till slutpunkter | Används för att komma åt slutpunkt                               |
| Databasen som används för att skapa vyer         | Databas som används som utgångspunkt i exempel       |

## <a name="first-time-setup"></a>Första gången setup

Innan prover med hjälp av prover:

- Skapa databas för dina vyer (om du vill använda vyer)
- Skapa autentiseringsuppgifter som ska användas av SQL på begäran för att komma åt filer i lagring

### <a name="create-database"></a>Skapa databas

Skapa din egen databas för demoändamål. Det här är databasen där du skapar dina vyer. Använd den här databasen i exempelfrågorna i den här artikeln.

> [!NOTE]
> Databaserna används endast för visningsmetadata, inte för faktiska data.
>
> Skriv ned databasnamn som du använder för att användas senare i snabbstarten.

Använd följande fråga `mydbname` och ändra till ett namn som du väljer:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Om du vill köra frågor med SQL på begäran skapar du autentiseringsuppgifter för SQL on-demand som ska användas för att komma åt filer i lagring.

> [!NOTE]
> Observera att du måste skapa autentiseringsuppgifter för åtkomst till lagringskontot. Även om SQL on-demand kan komma åt lagringar från olika regioner, kommer lagring och Azure Synapse-arbetsyta i samma region att ge bättre prestandaupplevelse.

Ändra följande kodavsnitt för att skapa autentiseringsuppgifter för CSV-, JSON- och Parkettbehållare:

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

Följande bild är en förhandsgranskning av filen som ska efterfrågas:

![Första 10 raderna i CSV-filen utan rubrik, Windows stil ny rad.](./sql/media/query-single-csv-file/population.png)

Följande fråga visar hur du läser en CSV-fil som inte innehåller en rubrikrad, med ny rad i Windows-format och kommaavgränsade kolumner:

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

Du kan ange schema vid frågekompileringstid.
Fler exempel finns i hur du [frågar CSV-filen](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Fråga parkettfiler

Följande exempel visar de automatiska schemaavferensfunktionerna för att fråga parquet-filer. Antalet rader returneras i september 2017 utan att schema anges.

> [!NOTE]
> Du behöver inte ange `OPENROWSET WITH` kolumner i sats när du läser Parquet filer. I så fall använder SQL on-demand metadata i parquet-filen och binder kolumner efter namn.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Mer information om [hur du frågar parkettfiler](sql/query-parquet-files.md)finns i du.

## <a name="querying-json-files"></a>Fråga JSON-filer

### <a name="json-sample-file"></a>JSON-exempelfil

Filer lagras i *json* container, *mappböcker*, och innehåller en bok post med följande struktur:

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

Följande fråga visar hur du använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta skalärvärden (titel, utgivare) från en bok med titeln *Probabilistic och Statistiska metoder i kryptologi, en introduktion av utvalda artiklar:*

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
> Vi läser hela JSON filen som enda rad / kolumn så FIELDTERMINATOR, FIELDQUOTE och ROWTERMINATOR är inställda på 0x0b eftersom vi inte förväntar oss att hitta den i filen.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja med följande Snabbstartsartiklar:

- [Fråga en enda CSV-fil](sql/query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](sql/query-folders-multiple-csv-files.md)
- [Fråga specifika filer](sql/query-specific-files.md)
- [Fråga parquet-filer](sql/query-parquet-files.md)
- [Frågor Parkett kapslade typer](sql/query-parquet-nested-types.md)
- [Fråga JSON-filer](sql/query-json-files.md)
- [Skapa och använda vyer](sql/create-use-views.md)
- [Skapa och använda externa tabeller](sql/create-use-external-tables.md)
- [Beständigt frågeresultat i Azure-lagring](sql/create-external-table-as-select.md)

Gå vidare till nästa artikel om du vill lära dig hur du frågar en enda CSV-fil.
> [!div class="nextstepaction"]
> [Fråga en enda CSV-fil](sql/query-single-csv-file.md)
