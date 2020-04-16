---
title: Använda externa tabeller med Synapse SQL
description: Läsa eller skriva datafiler med Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423980"
---
# <a name="use-external-tables-with-synapse-sql"></a>Använda externa tabeller med Synapse SQL

En extern tabell pekar på data som finns i Hadoop, Azure Storage blob eller Azure Data Lake Storage. Externa tabeller används för att läsa data från filer eller skriva data till filer i Azure Storage. Med Synapse SQL kan du använda externa tabeller för att läsa och skriva data till SQL-pool eller SQL on-demand (förhandsversion).

## <a name="external-tables-in-sql-pool"></a>Externa tabeller i SQL-poolen

I SQL-poolen kan du använda en extern tabell för att:

- Fråga Azure Blob Storage och Azure Data Lake Gen2 med Transact-SQL-uttryck.
- Importera och lagra data från Azure Blob Storage och Azure Data Lake Storage till SQL-pool.

När du används tillsammans med [CREATE TABLE AS SELECT-satsen](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) importerar om du väljer från en extern tabell data till en tabell i SQL-poolen. Utöver [COPY-satsen](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)är externa tabeller användbara för inläsning av data. En inläsningsdator självstudie finns i [Använda PolyBase för att läsa in data från Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="external-tables-in-sql-on-demand-preview"></a>Externa tabeller i SQL på begäran (förhandsgranskning)

För SQL on-demand använder du en extern tabell för att:

- Frågedata i Azure Blob Storage eller Azure Data Lake Storage med Transact-SQL-uttryck
- Lagra SQL on-demand-frågeresultat till filer i Azure Blob Storage eller Azure Data Lake Storage med [CETAS](develop-tables-cetas.md).

Du kan skapa externa tabeller med SQL on-demand via följande steg:

1. SKAPA EXTERN DATAKÄLLA
2. CREATE EXTERNAL FILE FORMAT
3. SKAPA EXTERN TABELL

## <a name="create-external-data-source"></a>SKAPA EXTERN DATAKÄLLA

Externa datakällor används för att ansluta till lagringskonton. Den fullständiga dokumentationen beskrivs [här](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="syntax-for-create-external-data-source"></a>Syntax för SKAPA EXTERN DATAKÄLLA

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Argument för SKAPA EXTERN DATAKÄLLA

data_source_name -Anger det användardefinierade namnet för datakällan. Namnet måste vara unikt i databasen.

LOCATION `'<prefix>://<path>'` = - Tillhandahåller anslutningsprotokollet och sökvägen till den externa datakällan. Sökvägen kan innehålla en behållare `'<prefix>://<path>/container'`i form av och `'<prefix>://<path>/container/folder'`en mapp i form av .

| Extern datakälla        | Platsprefix | Platssökväg                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Data Lake Store Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Store Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Exempel för SKAPA EXTERN DATAKÄLLA

I följande exempel skapas en extern datakälla för Azure Data Lake Gen2 som pekar på New York-datauppsättningen:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Skapar ett externt filformatsobjekt som definierar externa data som lagras i Azure Blob Storage eller Azure Data Lake Storage. Att skapa ett externt filformat är en förutsättning för att skapa en extern tabell. Den fullständiga dokumentationen är [här](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Genom att skapa ett externt filformat anger du den faktiska layouten för de data som refereras av en extern tabell.

## <a name="syntax-for-create-external-file-format"></a>Syntax för SKAPA EXTERNT FILFORMAT

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

## <a name="arguments-for-create-external-file-format"></a>Argument för SKAPA EXTERNT FILFORMAT

file_format_name- Anger ett namn för det externa filformatet.

FORMAT_TYPE = [ PARKETT | AVGRÄNSADTEXT]- Anger formatet för de externa data.

- PARKETT - Anger ett parkettformat.
- AVIMITEDTEXT - Anger ett textformat med kolumnavgränsare, även kallade fältavslutningar.

FIELD_TERMINATOR = *field_terminator* - Gäller endast avgränsade textfiler. Fältsluttaren anger ett eller flera tecken som markerar slutet på varje fält (kolumn) i den textavgränsade filen. Standard är pipe-tecknet (|1).

Exempel:

- FIELD_TERMINATOR = |
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR =\t

STRING_DELIMITER = *string_delimiter* - Anger fältslutaren för data av typen sträng i den textavgränsade filen. Strängavgränsaren är ett eller flera tecken i längd och omges av enstaka citattecken. Standard är den tomma strängen ("").

Exempel:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER =

FIRST_ROW = *First_row_int* - Anger det radnummer som läss först och gäller för alla filer. Om du ställer in värdet på två får den första raden i varje fil (rubrikrad) att hoppas över när data läses in. Rader hoppas över baserat på förekomsten av radavslutningar (/r/n, /r, /n).

USE_TYPE_DEFAULT = { SANT | **FALSKT** } - Anger hur värden som saknas i avgränsade textfiler ska hanteras när data hämtas från textfilen.

SANT - Om du hämtar data från textfilen lagrar du varje värde som saknas med hjälp av standardvärdets datatyp för motsvarande kolumn i den externa tabelldefinitionen. Ersätt till exempel ett värde som saknas med:

- 0 om kolumnen definieras som en numerisk kolumn. Decimalkolumner stöds inte och orsakar ett fel.
- Tom sträng ("") om kolumnen är en strängkolumn.
- 1900-01-01 om kolumnen är en datumkolumn.

FALSKT - Lagra alla värden som saknas som NULL. Alla NULL-värden som lagras med ordet NULL i den avgränsade textfilen importeras som strängen NULL.

Kodning = {'UTF8' | 'UTF16'} - SQL on-demand kan läsa UTF8 och UTF16 kodade avgränsade textfiler.

DATA_COMPRESSION = *data_compression_method* - Det här argumentet anger datakomprimeringsmetoden för externa data. När du läser från externa tabeller ignoreras den. Det används bara när du skriver till externa tabeller med [CETAS](develop-tables-cetas.md).

Filformattypen PARQUET stöder följande komprimeringsmetoder:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

## <a name="example-for-create-external-file-format"></a>Exempel för SKAPA EXTERNT FILFORMAT

I följande exempel skapas ett externt filformat för folkräkningsfiler:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>SKAPA EXTERN TABELL

Kommandot SKAPA EXTERN TABELL skapar en extern tabell för Synapse SQL för att komma åt data som lagras i Azure Blob Storage eller Azure Data Lake Storage. 

## <a name="syntax-for-create-external-table"></a>Syntax för SKAPA EXTERN TABELL

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

## <a name="arguments-create-external-table"></a>Argument SKAPA EXTERN TABELL

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Det namn på en till tre delar i tabellen som ska skapas. För en extern tabell lagrar SQL on-demand endast tabellmetadata. Inga faktiska data flyttas eller lagras i SQL på begäran.

<column_definition>... *n* ]

SKAPA EXTERN TABELL stöder möjligheten att konfigurera kolumnnamn, datatyp, nullability och sortering. Du kan inte använda STANDARDBEGRÄNSNINGEN FÖR externa tabeller.

>[!IMPORTANT]
>Kolumndefinitionerna, inklusive datatyp och antalet kolumner, måste matcha data i de externa filerna. Vid felmatchning avvisas filraderna när du kör frågor mot faktiska data.

När du läser från Parquet filer, kan du ange bara de kolumner du vill läsa och hoppa över resten.

PLATS = '*folder_or_filepath*'

Anger mappen eller filsökvägen och filnamnet för faktiska data i Azure Blob Storage. Platsen börjar från rotmappen. Rotmappen är den dataplats som anges i den externa datakällan.

Om du anger en mappplats kommer en SQL-fråga på begäran att väljas från den externa tabellen och hämta filer från mappen.

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar SQL on-demand inte undermappar. Den returnerar filer för vilka filnamnet börjar med en understrykning (_) eller en punkt (.).

I det här exemplet, om LOCATION='/webdata/', en SQL on-demand-fråga, returnerar rader från mydata.txt och _hidden.txt. Det kommer inte tillbaka mydata2.txt och mydata3.txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* - Anger namnet på den externa datakälla som innehåller platsen för de externa data. Om du vill skapa en extern datakälla använder du [SKAPA EXTERN DATAKÄLLA](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* - Anger namnet på det externa filformatsobjektet som lagrar filtypen och komprimeringsmetoden för externa data. Om du vill skapa ett externt filformat använder du [SKAPA EXTERNT FILFORMAT](#create-external-file-format).

## <a name="permissions-create-external-table"></a>Behörigheter SKAPA EXTERN TABELL

Om du vill välja från en extern tabell behöver du rätt autentiseringsuppgifter med list- och läsbehörigheter.

## <a name="example-create-external-table"></a>Exempel SKAPA EXTERN TABELL

I följande exempel skapas en extern tabell. Den returnerar den första raden:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Skapa och fråga externa tabeller från en fil i Azure Data Lake

Med hjälp av datasjöutforskningsfunktioner kan du nu skapa och fråga en extern tabell med SQL-pool eller SQL på begäran med ett enkelt högerklicka på filen.

## <a name="prerequisites"></a>Krav

- Du måste ha tillgång till arbetsytan med rollen armåtkomst till ADLS Gen2-kontot med minst ARM Access för storage blob Data Contributor till ADLS Gen2-kontot

- Du måste ha minst [behörighet för att skapa](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) och fråga externa tabeller i SQL-poolen eller SQL OD

- Den länkade tjänsten som är associerad med ADLS Gen2-kontot **måste ha åtkomst till filen**. Om till exempel den länkade tjänstautentiseringsmekanismen är Hanterad identitet måste den hanterade arbetsyteidentiteten ha minst behörigheten Lagringsblobbläsare på lagringskontot

På datapanelen väljer du den fil som du vill skapa den externa tabellen från:
> [!div class="mx-imgBorder"]
>![externt tabell1](./media/develop-tables-external-tables/external-table-1.png)

Ett dialogfönster öppnas. Välj SQL-pool eller SQL på begäran, ge tabellen ett namn och välj öppna skript:

> [!div class="mx-imgBorder"]
>![externt tabell2](./media/develop-tables-external-tables/external-table-2.png)

SQL Script genereras automatiskt som drar slutsatsen att schemat från filen kommer in:
> [!div class="mx-imgBorder"]
>![externt tabell3](./media/develop-tables-external-tables/external-table-3.png)

Kör skriptet. Skriptet kör automatiskt en Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externt tabell4](./media/develop-tables-external-tables/external-table-4.png)

Den externa tabellen skapas nu för framtida utforskning av innehållet i den här externa tabellen kan användaren fråga den direkt från datafönstret:
> [!div class="mx-imgBorder"]
>![externt tabell5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Nästa steg

I [CETAS-artikeln](develop-tables-cetas.md) finns i CETAS-artikeln om du vill spara frågeresultaten i en extern tabell i Azure Storage. Du kan också börja fråga [Spark-tabeller](develop-storage-files-spark-tables.md).
