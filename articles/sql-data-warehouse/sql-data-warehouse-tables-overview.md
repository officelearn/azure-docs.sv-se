---
title: Utforma tabeller – Azure SQL Data Warehouse | Microsoft Docs
description: Introduktion till utformningen av tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 290230237a68730a908c6fd0fb0df1d63035b93b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247348"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Utforma tabeller i Azure SQL Data Warehouse

Lär dig viktiga begrepp för att utforma tabeller i Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Fastställa tabellkategori 

En [star-schema](https://en.wikipedia.org/wiki/Star_schema) ordnar data i fakta-och dimensionstabeller. Vissa tabeller som används för integration eller mellanlagring data innan den går vidare till en tabell med fakta eller dimension. När du utformar en tabell, kan du bestämma om tabelldata tillhör ett faktum, dimension eller integrering tabell. Det här beslutet informerar lämplig tabellstrukturen och distribution. 

- **Faktatabeller** innehåller kvantitativa data som ofta genereras i ett transaktionella system, och sedan läses in i datalagret. Till exempel en retail-verksamhet och genererar försäljningen varje dag och sedan läser in data i en faktatabell för data warehouse för analys.

- **Dimensionen tabeller** innehåller attributdata som ändras, men vanligtvis ändras sällan. Till exempel en kunds namn och adress lagras i en dimensionstabell och uppdateras bara när kundens profilen ändras. För att minimera storleken på en stor faktatabell behöver kundens namn och adress inte finnas i varje rad i en faktatabell. I stället kan faktatabellen och dimensionstabellen dela ett kund-ID. En fråga kan ansluta till de två tabellerna om du vill associera en kunds profil och transaktioner. 

- **Integrering tabeller** ange en plats för integrering av eller organiserar data. Du kan skapa en integration-tabell som en vanlig tabell, en extern tabell eller en temporär tabell. Du kan till exempel läsa in data i en mellanlagringstabell, utföra omvandlingar på data i mellanlagring och infogar data i en produktionstabell.

## <a name="schema-and-table-names"></a>Schema och tabellnamn
Ett informationslager är en typ av databas i SQL Data Warehouse. Alla tabeller i datalagret ingår i samma databas.  Du kan inte ansluta till tabeller i flera datalager. Det här beteendet skiljer sig från SQL Server som har stöd för kopplingar mellan databaser. 

I en SQL Server-databas, kan du använda fakta, dimension, eller integrera för schemanamnen. Om du migrerar en SQL Server-databas till SQL Data Warehouse, fungerar det bäst för att migrera alla tabeller fakta, dimension och integrering med ett schema i SQL Data Warehouse. Du kan till exempel lagra alla tabeller i den [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) exempelinformationslagret inom ett schema som kallas wwi. Följande kod skapar en [användardefinierat schema](/sql/t-sql/statements/create-schema-transact-sql) kallas wwi.

```sql
CREATE SCHEMA wwi;
```

För att visa hur tabellerna i SQL Data Warehouse, kan du använda fakta och dimension int som prefix tabellnamnen. I följande tabell visar några av namnen på schema och tabellen för WideWorldImportersDW. Den jämför namnen i SQL Server med namn i SQL Data Warehouse. 

| WideWorldImportersDW tabell  | Registertyp | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Ort | Dimension | Dimension.City | wwi.DimCity |
| Beställa | Fakta för | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Tabellen persistence 

Tabeller lagra data antingen permanent i Azure Storage, tillfälligt i Azure Storage eller i ett datalager som är externa för datalagret.

### <a name="regular-table"></a>Vanlig tabell

En vanlig tabell lagrar data i Azure Storage som en del av datalagret. Tabellen och data du bevara oavsett om en session är öppen.  Det här exemplet skapar en vanlig tabell med två kolumner. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Temporär tabell
Det finns bara en tillfällig tabell för hela sessionen. Du kan använda en tillfällig tabell för att förhindra att andra användningsområden tillfälliga resultat och att minska behovet av rensning.  Eftersom temporära tabeller kan också använda lokal lagring, kan de ger bättre prestanda för vissa åtgärder.  Mer information finns i [temporära tabeller](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Extern tabell
En extern tabell pekar på data som finns i Azure Storage blob eller Azure Data Lake Store. När den används tillsammans med CREATE TABLE AS SELECT-instruktionen, importerar att välja från en extern tabell data till SQL Data Warehouse. Externa tabeller är därför användbart för inläsning av data. En självstudiekurs om inläsning finns i [använda PolyBase för att läsa in data från Azure blob storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Datatyper
SQL Data Warehouse stöder vanligaste de datatyper. En lista över datatyperna som stöds finns i [datatyper i CREATE TABLE-referens](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-instruktionen. Genom att minimera storleken på datatyper hjälper till att förbättra frågeprestanda. Anvisningar om hur du använder datatyper finns i [datatyper](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuerade tabeller
En grundläggande funktion i SQL Data Warehouse är som den kan lagra och fungerar på tabeller över 60 [distributioner](massively-parallel-processing-mpp-architecture.md#distributions).  Tabeller distribueras med en annan metod (round-robin), hash eller replikering.

### <a name="hash-distributed-tables"></a>Hash-distribuerad tabeller
Hash-distributionen distribuerar rader baserat på värdet i kolumnen distribution. I hash-distribuerad tabell har utformats för att uppnå höga prestanda för frågan kopplar för stora tabeller. Det finns flera faktorer som påverkar valet av kolumnen distribution. 

Mer information finns i [designriktlinjer för distribuerade tabeller](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell har en fullständig kopia av tabellen tillgänglig på varje beräkningsnod. Frågor som körs snabbt på replikerade tabeller eftersom kopplingar i replikerade tabeller inte kräver dataförflyttning. Replikering kräver extra lagring men och är inte praktiskt för stora tabeller. 

Mer information finns i [designriktlinjer för replikerade tabeller](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Resursallokering tabeller
En resursallokeringstabell fördelar tabellrader jämnt över alla distributioner. Raderna distribueras slumpmässigt. Det går snabbt att läsa in data i en tabell med resursallokering.  Frågor kan dock kräva mer dataförflyttning än de andra metoderna för distribution. 

Mer information finns i [designriktlinjer för distribuerade tabeller](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Vanliga distributionsmetoder för tabeller
Kategorin tabell avgör ofta vilket alternativ du väljer för att distribuera i tabellen. 

| Tabellkategori | Rekommenderade distributionsalternativ |
|:---------------|:--------------------|
| Fakta för           | Använd hash-distribution med grupperat columnstore-index. Prestanda förbättras när två hash-tabeller som är anslutna på samma distributionskolumn. |
| Dimension      | Använd replikeras för mindre tabeller. Om du för stor för att lagra på varje beräkningsnod, Använd hash-distribuerad. |
| Mellanlagring        | Använd resursallokering för mellanlagringstabellen. Belastningen med CTAS går snabbt. När data är i mellanlagringstabellen kan du använda INSERT... Välj det här alternativet om du vill flytta data till en produktionstabellerna. |

## <a name="table-partitions"></a>Tabellpartitioner
En partitionerad tabell lagrar och utför åtgärder på tabellrader enligt dataområden. Till exempel kan en tabell partitioneras efter dag, månad eller år. Du kan förbättra frågeprestanda genom partitionseliminering, vilket begränsar en fråga sökning till data inom en partition. Du kan också ha data genom att växla partition. Eftersom data i SQL Data Warehouse redan har distribuerats kan för många partitioner sakta frågeprestanda. Mer information finns i [partitionering vägledning](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Columnstore-index
Som standard lagrar SQL Data Warehouse en tabell som ett grupperat kolumnlagringsindex. Den här typen av datalagring uppnår hög datakomprimering och frågeprestanda för stora tabeller.  Grupperat columnstore-index är vanligtvis det bästa valet, men i vissa fall ett grupperat index eller en heap är lämplig lagringsstrukturen.

En lista över columnstore funktioner finns i [vad är nytt för kolumnlagringsindex](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). För att förbättra prestanda för columnstore-index, se [maximera radgrupps kvalitet för kolumnlagringsindex](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistik
Frågeoptimeringen använder statistik på kolumnnivå när den skapar planen för att köra en fråga. För att förbättra frågeprestanda är det viktigt att skapa statistik på enskilda kolumner, särskilt de kolumner som används i frågan kopplar. Skapar och uppdaterar statistik sker inte automatiskt. [Skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql) när du har skapat en tabell. Uppdatera statistik när ett stort antal rader läggs till eller ändras. Till exempel uppdatera statistiken efter att en belastning. Mer information finns i [statistik vägledning](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Kommandon för att skapa tabeller
Du kan skapa en tabell som en ny tom tabell. Du kan också skapa och fylla i en tabell med resultatet av en select-instruktion. Här följer T-SQL-kommandon för att skapa en tabell.

| T-SQL-uttryck | Beskrivning |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Skapar en tom tabell genom att definiera de tabellkolumner och alternativ. |
| [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql) | Skapar en extern tabell. Definitionen av tabellen lagras i SQL Data Warehouse. Tabelldata lagras i Azure Blob storage eller Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Fyller på en ny tabell med resultatet av en select-instruktion. Tabelltyper kolumner och data baserat på resultaten select-instruktion. Den här instruktionen kan välja från en extern tabell för att importera data. |
| [SKAPA EXTERN TABELL SOM VÄLJ](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Skapar en ny extern tabell genom att exportera resultaten av en select-instruktion till en extern plats.  Platsen är antingen Azure Blob storage eller Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Justera källdata med datalagret

Informationslagertabeller fylls genom att läsa in data från en annan datakälla. Om du vill utföra en lyckad belastning, måste antal och data typer av kolumner i datakällan överensstämma med tabelldefinition i datalagret. Hämta data för att anpassas kanske den svåraste delen för att utveckla dina tabeller. 

Om data kommer från flera datalager, du fört dina data till datalagret och lagra den i en tabell för integrering. När data är i integrationstabellen kan använda du kraften i SQL Data Warehouse för att utföra transformeringsåtgärder. När data är förberedd kan du infoga dem i produktionstabellerna.

## <a name="unsupported-table-features"></a>Funktioner som inte stöds tabell
SQL Data Warehouse stöder många, men inte alla, tabell-funktioner erbjuds av andra databaser.  I följande lista visar några av de table-funktioner som inte stöds i SQL Data Warehouse.

- Primär nyckel, sekundärnycklar unik, kontrollera [Tabellbegränsningar](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Beräknade kolumner](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indexerade vyer](/sql/relational-databases/views/create-indexed-views)
- [Sekvens](/sql/t-sql/statements/create-sequence-transact-sql)
- [Glesa kolumner](/sql/relational-databases/tables/use-sparse-columns)
- Surrogate nycklar. Implementera med [identitet](sql-data-warehouse-tables-identity.md).
- [Synonyms](/sql/t-sql/statements/create-synonym-transact-sql)
- [Utlösare](/sql/t-sql/statements/create-trigger-transact-sql)
- [Unika index](/sql/t-sql/statements/create-index-transact-sql)
- [Användardefinierade typer](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Tabellen storlek frågor
Ett enkelt sätt att identifiera utrymme och rader som används av en tabell i var och en av 60 distributioner är att använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Men kan med hjälp av DBCC-kommandon vara ganska begränsande.  Dynamiska hanteringsvyer (DMV) visar mer information än DBCC-kommandon. Börja med att skapa den här vyn.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Tabellutrymme sammanfattning

Den här frågan returnerar rader och utrymme genom att tabellen.  Det kan du se vilka tabeller som är dina största tabeller och om de är resursallokering, replikeras, eller hash - distribueras.  Frågan visar kolumnen distribution för hash-distribuerad tabeller.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabellutrymme efter typ av distribution

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabellutrymme per Indextyp

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Distribution utrymme sammanfattning

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Nästa steg
När du har skapat tabeller för ditt informationslager, är nästa steg att läsa in data i tabellen.  En självstudiekurs om inläsning finns i [läser in data till SQL Data Warehouse](load-data-wideworldimportersdw.md).
