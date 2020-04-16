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
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424029"
---
# <a name="cetas-with-synapse-sql"></a>CETAS med Synapse SQL

I antingen SQL-poolen eller SQL on-demand (förhandsversion) kan du använda SKAPA EXTERN TABELL SOM SELECT (CETAS) för att slutföra följande uppgifter:  

- Skapa en extern tabell
- Exportera, parallellt, resultaten av ett Transact-SQL SELECT-uttryck till

  - Hadoop
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS i SQL-pool

För SQL-pool, CETAS-användning och syntax kontrollerar du artikeln [SKAPA EXTERN TABELL SOM SELECT.](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Mer information om CTAS med SQL-pool finns i artikeln [SKAPA TABELL SOM SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="cetas-in-sql-on-demand"></a>CETAS i SQL on-demand

När du använder SQL on-demand-resursen används CETAS för att skapa en extern tabell och exportera frågeresultat till Azure Storage Blob eller Azure Data Lake Storage Gen2.

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

**-* Database_name. - *Jag schema_name har* inte tid med det här. ] | *schema_name* . ] *table_name**

Det namn på en till tre delar i tabellen som ska skapas. För en extern tabell lagrar SQL on-demand endast tabellmetadata. Inga faktiska data flyttas eller lagras i SQL på begäran.

PLATS = *"path_to_folder"*

Anger var resultaten av SELECT-satsen ska skrivas på den externa datakällan. Rotmappen är den dataplats som anges i den externa datakällan. PLATS måste peka på en mapp och ha en avslutande /. Exempel: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Anger namnet på det externa datakällobjektet som innehåller platsen där de externa data ska lagras. Om du vill skapa en extern datakälla använder du [SKAPA EXTERN DATAKÄLLA (Transact-SQL).](develop-tables-external-tables.md#create-external-data-source)

FILE_FORMAT = *external_file_format_name*

Anger namnet på det externa filformatsobjektet som innehåller formatet för den externa datafilen. Om du vill skapa ett externt filformat använder du [SKAPA EXTERNT FILFORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Endast externa filformat med FORMAT='PARKQUET' stöds för närvarande.

MED *<common_table_expression>*

Anger en tillfällig namngiven resultatuppsättning, känd som ett gemensamt tabelluttryck (CTE). Mer information finns i [MED common_table_expression (Transact-SQL).](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

VÄLJ <select_criteria>

Fyller i den nya tabellen med resultaten från en SELECT-sats. *select_criteria* är brödtexten i SELECT-satsen som avgör vilka data som ska kopieras till den nya tabellen. Information om SELECT-satser finns i [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="permissions"></a>Behörigheter

Du måste ha behörighet att lista mappinnehåll och skriva till mappen PLATS för att CETAS ska fungera.

## <a name="examples"></a>Exempel

Dessa exempel använder CETAS för att spara den totala populationen som aggregeras efter år och tillstånd till en aggregated_data mapp som finns i population_ds datakällan.

Det här exemplet är beroende av det autentiseringsuppgifter, datakälla och externa filformat som skapats tidigare. Se dokumentet om [externa tabeller.](develop-tables-external-tables.md) Om du vill spara frågeresultat i en annan mapp i samma datakälla ändrar du argumentet PLATS. Om du vill spara resultat i ett annat lagringskonto skapar och använder du en annan datakälla för DATA_SOURCE argument.

> [!NOTE]
> Exemplen som följer använder ett offentligt Azure Open Data storage-konto. Det är skrivskyddat. Om du vill köra dessa frågor måste du ange den datakälla som du har skrivbehörighet för.

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

I exemplet nedan används en extern tabell som källa för CETAS. Den är beroende av autentiseringsuppgifter, datakälla, externt filformat och extern tabell som skapats tidigare. Se dokumentet om [externa tabeller.](develop-tables-external-tables.md)

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

## <a name="supported-data-types"></a>Datatyper som stöds

CETAS kan användas för att lagra resultatuppsättningar med följande SQL-datatyper:

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

LOBs kan inte användas med CETAS.

Följande datatyper kan inte användas i SELECT-delen av CETAS:

- nchar
- nvarchar
- datetime
- smalldatetime
- datetimeoffset
- money
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>Nästa steg

Du kan prova att fråga [Spark-tabeller](develop-storage-files-spark-tables.md).
