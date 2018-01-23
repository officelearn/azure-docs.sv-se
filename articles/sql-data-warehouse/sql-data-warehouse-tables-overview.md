---
title: Tabellen design introduktion - Azure SQL Data Warehouse | Microsoft Docs
description: Introduktion till skapar tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 01/18/2018
ms.author: barbkess
ms.openlocfilehash: 5c163880a7508d69bce0019cc5379bca8c704d59
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="introduction-to-designing-tables-in-azure-sql-data-warehouse"></a>Introduktion till skapar tabeller i Azure SQL Data Warehouse

Läs om viktiga begrepp för att utforma tabeller i Azure SQL Data Warehouse. 

## <a name="determining-table-category"></a>Bestämma tabellkategori 

En [stjärnschema](https://en.wikipedia.org/wiki/Star_schema) organiserar data i fakta-och dimensionstabeller. Vissa tabeller som används för integrering eller mellanlagring data innan den flyttas till en tabell med fakta eller dimension. När du utformar en tabell kan du bestämma om tabelldata tillhör i en faktum, dimension eller integration tabell. Det här beslutet informerar lämpliga tabellstrukturen och distribution. 

- **Faktatabeller** innehåller kvantitativa data som ofta genereras i ett system med transaktionella, och sedan läses in i datalagret. En detaljhandeln genererar försäljning transaktioner varje dag och sedan läser in data i en faktatabell data warehouse för analys.

- **Dimensionen tabeller** innehåller attributdata som kan ändra men vanligtvis ändras sällan. Till exempel namn och adress för en kund lagras i en dimensionstabell och uppdateras bara när kundens profil ändras. För att minimera storleken på en stor faktatabell, behöver kundens namn och adress inte vara varje rad i en faktatabell. I stället kan faktatabellen och dimensionstabellen dela ett kund-ID. En fråga kan ansluta till de två tabellerna om du vill associera en kund profil och transaktioner. 

- **Integration tabeller** tillhandahålla en plats för integrering eller mellanlagring av data. Du kan skapa en integration tabell som en vanlig tabell, en extern tabell eller en tillfällig tabell. Till exempel läsa in data till en mellanlagringstabellen, utföra omformningar på data i Förproduktion och infoga data i en tabell i produktion.

## <a name="schema-and-table-names"></a>Schema och tabellnamn
I SQL Data Warehouse är ett informationslager en typ av databas. Alla tabeller i datalagret ingår i samma databas.  Du kan inte ansluta till tabeller över flera datalager. Det här beteendet skiljer sig från SQL Server som stöder flera databaser kopplingar. 

Du kan använda fakta, dim, eller integrera för schemanamn i en SQL Server-databas. Om du migrerar en SQL Server-databas till SQL Data Warehouse, fungerar det bäst för att migrera alla fakta, dimensionen och integrering tabeller till ett schema i SQL Data Warehouse. Du kan till exempel lagra alla tabeller i den [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) exempel datalager i ett schema som kallas wwi. Följande kod skapar en [användardefinierade schemat](/sql/t-sql/statements/create-schema-transact-sql) kallas wwi.

```sql
CREATE SCHEMA wwi;
```

Om du vill visa organisationen för tabellerna i SQL Data Warehouse, kan du använda fakta och dimension int som prefix tabellnamnen. Följande tabell visar några av namnen på schema och tabellen för WideWorldImportersDW. Den jämför namnen i SQL Server med namnen i SQL Data Warehouse. 

| WideWorldImportersDW tabell  | Tabelltypen | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|
| Ort | Dimensionen | Dimension.City | wwi.DimCity |
| Ordning | Fakta | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Tabell beständiga 

Tabeller lagra data permanent i Azure Storage, tillfälligt i Azure Storage, eller i ett dataarkiv som är externa för datalagret.

### <a name="regular-table"></a>Vanlig tabell

En vanlig tabell lagrar data i Azure Storage som en del av datalagret. Tabellen och data som kvarstår oavsett om en session är öppen.  Det här exemplet skapar en vanlig tabell med två kolumner. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tillfällig tabell
Det finns endast en tillfällig tabell för hela sessionen. Du kan använda en tillfällig tabell för att förhindra att andra använder tillfälliga resultat och att minska behovet av att rensa.  Eftersom temporära tabeller kan du också använda lokal lagring, ger de snabbare prestanda för vissa åtgärder.  Mer information finns i [temporära tabeller](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Extern tabell
En extern tabell pekar på data som finns i Azure Storage blob eller Azure Data Lake Store. När den används tillsammans med instruktionen CREATE TABLE AS SELECT, importerar välja från en extern tabell data till SQL Data Warehouse. Externa tabeller kan därför användbart för att läsa in data. En självstudiekurs inläsning finns [Använd PolyBase för att läsa in data från Azure blob storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Datatyper
SQL Data Warehouse stöder vanligaste de datatyper. En lista över datatyperna som stöds, se [datatyper i CREATE TABLE-referens](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-instruktion. Minimera storlek på datatyper för hjälper till att förbättra frågeprestanda. Anvisningar om hur du använder datatyper finns [datatyper](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuerade tabeller
En grundläggande funktion i SQL Data Warehouse är som den kan lagra och fungerar på tabeller över 60 [distributioner](massively-parallel-processing-mpp-architecture.md#distributions).  Tabellerna distribueras med en metod för resursallokering, hash eller replikering.

### <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
Hash-distribution distribuerar raderna baserat på värdet i kolumnen distribution. Distribuerad hash-tabellen är utformat för att uppnå hög prestanda för frågan kopplar på stora tabeller. Det finns flera faktorer som påverkar valet av kolumnen distribution. 

Mer information finns i [utforma vägledning för distribuerade tabeller](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell har en fullständig kopia av tabellen tillgänglig på varje beräkningsnod. Frågor som körs på snabb replikerade tabeller eftersom kopplingar i replikerade tabeller inte kräver dataflyttning. Replikering kräver extra lagring, och är inte praktiskt för stora tabeller. 

Mer information finns i [utforma vägledning för replikerade tabeller](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Resursallokering tabeller
En tabell med resursallokering fördelar tabellraderna jämnt över alla distributioner. Raderna distribueras slumpmässigt. Läsa in data i en tabell med resursallokering är snabbt.  Frågor kräver dock mer dataflyttning än de andra metoderna för distribution. 

Mer information finns i [utforma vägledning för distribuerade tabeller](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Vanliga distributionsmetoder för register
Kategorin tabellen anger ofta vilket alternativ du väljer för att distribuera tabellen. 

| Tabellkategori | Rekommenderade distributionsalternativ |
|:---------------|:--------------------|
| Fakta           | Använd hash-distribution med grupperade columnstore-indexet. Prestanda förbättras när två hash-tabellerna till samma kolumn i distributionen. |
| Dimensionen      | Använd replikeras för mindre tabeller. Om du för stort för att lagra på varje beräkningsnod, Använd hash-distribueras. |
| Mellanlagring        | Använda resursallokering för mellanlagringstabellen. Belastningen med CTAS går snabbt. När data är i mellanlagringstabellen, använda INSERT... Välj det här alternativet om du vill flytta data till en produktions-tabeller. |

## <a name="table-partitions"></a>Tabellpartitioner
En partitionerad tabell lagrar och utför åtgärder på tabellraderna enligt dataområden. En tabell kan till exempel vara partitionerade per dag, månad eller år. Du kan förbättra frågeprestanda via partition eliminering som begränsar en fråga genomsökning data inom en partition. Du kan också behålla data genom att växla partition. Eftersom data i SQL Data Warehouse redan har distribuerats långsamma för många partitioner prestanda för frågor. Mer information finns i [partitionering vägledning](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Columnstore-index
Som standard lagrar en tabell i SQL Data Warehouse som ett grupperat columnstore-index. Det här formuläret för datalagring uppnår hög komprimering och frågeprestanda för stora tabeller.  Grupperade columnstore-indexet är vanligtvis det bästa valet, men i vissa fall ett grupperat index eller en heap är lämplig lagringsstruktur.

En lista över columnstore-funktioner, se [vad är nytt för columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). För att förbättra prestanda för columnstore-index, se [maximera radgrupps kvalitet för columnstore-index](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistik
Frågeoptimeringen använder statistik på kolumnnivå när den skapar plan för att köra en fråga. Om du vill förbättra frågeprestanda är det viktigt att skapa statistik på enskilda kolumner, särskilt de kolumner som används i frågan kopplar. Skapa och uppdatera statistik sker inte automatiskt. [Skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql) när du har skapat en tabell. Uppdatera statistiken efter att ett stort antal rader läggs till eller ändras. Till exempel uppdatera statistiken efter att en belastning. Mer information finns i [statistik vägledning](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Kommandon för att skapa tabeller
Du kan skapa en tabell som en ny tom tabell. Du kan också skapa och fylla i en tabell med resultatet av en select-instruktion. Följande är T-SQL-kommandon för att skapa en tabell.

| T-SQL-uttryck | Beskrivning |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Skapar en tom tabell genom att definiera de tabellkolumner och alternativ. |
| [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql) | Skapar en extern tabell. Definitionen av tabellen lagras i SQL Data Warehouse. Tabellens data lagras i Azure Blob storage eller Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Fyller i en ny tabell med resultatet av en select-instruktion. Tabelltyper kolumner och data baserat på resultatet select-instruktion. Den här instruktionen kan välja från en extern tabell för att importera data. |
| [SKAPA EXTERN TABELL SOM VÄLJER](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Skapar en ny extern tabell genom att exportera resultaten av en select-instruktion till en extern plats.  Platsen är antingen Azure Blob storage eller Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Justera källdata med datalagret

Data warehouse-tabeller fylls genom att läsa in data från en annan datakälla. Om du vill utföra en lyckad belastning måste numret och datatyperna i kolumner i källdata överensstämmer med tabelldefinitionen i datalagret. Hämtning av data ska justeras kanske den svåraste delen av skapar tabeller. 

Om data kommer från flera datalager du sätta data i datalagret och lagrar den i en tabell för integrering. När data i integrationstabellen kan använda du kraften i SQL Data Warehouse för att utföra transformeringsåtgärder. När data är förberedd kan du infoga den i produktion tabeller.

## <a name="unsupported-table-features"></a>Funktioner som inte stöds tabell
SQL Data Warehouse stöder många, men inte alla, tabell-funktioner som erbjuds av andra databaser.  I följande lista visar några av funktionerna för tabellen som inte stöds i SQL Data Warehouse.

- Primär nyckel, sekundärnycklar unik, kontrollera [tabell begränsningar](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Beräknade kolumner](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indexerade vyer](/sql/relational-databases/views/create-indexed-views)
- [Sekvens](/sql/t-sql/statements/create-sequence-transact-sql)
- [Glesa kolumner](/sql/relational-databases/tables/use-sparse-columns)
- [Surrogattecken nycklar](). Implementera med [identitet](sql-data-warehouse-tables-identity.md).
- [Synonymer](/sql/t-sql/statements/create-synonym-transact-sql)
- [Utlösare](/sql/t-sql/statements/create-trigger-transact-sql)
- [Unika index](/sql/t-sql/statements/create-index-transact-sql)
- [Användardefinierade typer](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Tabell storlek frågor
Ett enkelt sätt att identifiera utrymme och rader som används av en tabell i var och en av de 60-distributioner är att använda [DBCC PDW_SHOWSPACEUSED] [DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Men kan med hjälp av DBCC-kommandon ganska begränsa.  Dynamiska hanteringsvyer (av DMV: er) visar mer information än DBCC-kommandon. Börja med att skapa den här vyn.

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

### <a name="table-space-summary"></a>Översikt över tabellutrymme

Den här frågan returnerar rader och utrymme för tabellen.  Det kan du se vilka tabeller som är dina största tabeller och om de är resursallokering, replikeras, eller hash - distribueras.  Frågan visar kolumnen distribution för hash-distribuerade tabeller.  

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

### <a name="table-space-by-distribution-type"></a>Tabellutrymme av distributionstypen

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

### <a name="table-space-by-index-type"></a>Tabellutrymme av Indextypen

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
När du skapar tabeller för ditt data warehouse, är nästa steg att läsa in data i tabellen.  En självstudiekurs inläsning finns [läsa in data från Azure blob storage med PolyBase](load-data-from-azure-blob-storage-using-polybase.md).