---
title: CETAS i Synapse SQL
description: Använda CETAS med Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f3e53ac189e0d612b09c362e82ba5bc2fe5fec8d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696824"
---
# <a name="cetas-with-synapse-sql"></a>CETAS med Synapse SQL

I antingen SQL-poolen eller SQL på begäran (för hands version) kan du använda skapa extern tabell som SELECT (CETAS) för att utföra följande uppgifter:  

- Skapa en extern tabell
- Exportera, parallellt, resultatet av ett Transact-SQL SELECT-uttryck till

  - Hadoop
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS i SQL-pool

För SQL-pool, CETAS-användning och syntax, markerar du artikeln [skapa extern tabell som Välj](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . Mer information om CTAS med SQL-poolen finns i artikeln [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="cetas-in-sql-on-demand"></a>CETAS i SQL på begäran

När du använder SQL-resursen på begäran, används CETAS för att skapa en extern tabell och exportera frågeresultaten till Azure Storage Blob eller Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Syntax

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argument

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *table_name**

Namnet på en till tre delar av tabellen som ska skapas. För en extern tabell lagrar SQL on-demand bara tabellens metadata. Inga faktiska data flyttas eller lagras i SQL på begäran.

PLATS = *path_to_folder*

Anger var resultatet av SELECT-instruktionen ska skrivas på den externa data källan. Rotmappen är den dataplats som anges i den externa datakällan. PLATSEN måste peka på en mapp och ha ett avslutande/. Exempel: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Anger namnet på det externa data käll objekt som innehåller den plats där de externa data ska lagras. Om du vill skapa en extern data källa använder du [skapa en extern data källa (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Anger namnet på det externa fil formats objekt som innehåller formatet för den externa data filen. Om du vill skapa ett externt fil format använder du [Skapa externt fil format (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Endast externa fil format med formatet = ' PARQUET ' stöds för närvarande.

MED *<common_table_expression>*

Anger en tillfällig namngiven resultat uppsättning, kallat ett gemensamt tabell uttryck (common Table EXPRESSIONS). Mer information finns i [med common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Välj <select_criteria>

Fyller den nya tabellen med resultaten från en SELECT-instruktion. *select_criteria* är bröd texten i SELECT-instruktionen som avgör vilka data som ska kopieras till den nya tabellen. Information om SELECT-instruktioner finns i [Select (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> ORDER BY-satsen i SELECT-delen av CETAS stöds inte.

## <a name="permissions"></a>Behörigheter

Du måste ha behörighet att lista innehållet i mappen och skriva till plats-mappen för att CETAS ska fungera.

## <a name="examples"></a>Exempel

I de här exemplen används CETAS för att spara total population som sammanställs per år och stat till en aggregated_data mapp som finns i population_ds data källan.

Det här exemplet använder sig av autentiseringsuppgifter, data källa och externt fil format som skapats tidigare. Referera till det [externa tabell](develop-tables-external-tables.md) dokumentet. Om du vill spara frågeresultaten till en annan mapp i samma data källa ändrar du plats argumentet. 

Om du vill spara resultatet i ett annat lagrings konto skapar du och använder en annan data källa för DATA_SOURCE argument.

> [!NOTE]
> I exemplen som följer används ett offentligt Azure Open data Storage-konto. Den är skrivskyddad. Om du vill köra dessa frågor måste du ange data källan som du har Skriv behörighet för.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

I följande exempel används en extern tabell som källa för CETAS. Den förlitar sig på autentiseringsuppgifter, data källa, externt fil format och extern tabell som skapats tidigare. Referera till det [externa tabell](develop-tables-external-tables.md) dokumentet.

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Data typer som stöds

CETAS kan användas för att lagra resultat uppsättningar med följande SQL-data typer:

- binary
- varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- numeric
- float
- real
- bigint
- int
- smallint
- tinyint
- bit

> [!NOTE]
> LOBs kan inte användas med CETAS.

Följande data typer kan inte användas i SELECT-delen av CETAS:

- nchar
- nvarchar
- datetime
- smalldatetime
- datetimeoffset
- money
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>Nästa steg

Du kan prova att fråga [Apache Spark för externa Azure Synapse-tabeller](develop-storage-files-spark-tables.md).
