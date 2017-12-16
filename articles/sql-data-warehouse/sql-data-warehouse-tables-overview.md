---
title: "Översikt över tabeller i SQL Data Warehouse | Microsoft Docs"
description: "Komma igång med Azure SQL Data Warehouse-tabeller."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/14/2017
ms.author: barbkess
ms.openlocfilehash: 46f7d2ea19a88e65b2d039fdf36d1619c4d74020
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>Översikt över tabeller i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Datatyper][Data Types]
> * [Distribuera][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistik][Statistics]
> * [Tillfällig][Temporary]
> 
> 

Det är enkelt att komma igång med att skapa tabeller i SQL Data Warehouse.  Grundläggande [CREATE TABLE] [ CREATE TABLE] syntaxen följer samma syntax som du är mest sannolika redan bekant med från att arbeta med andra databaser.  Om du vill skapa en tabell, behöver du bara namnge din tabell, namnge din kolumner och definiera datatyper för varje kolumn.  Om du har skapar tabeller i andra databaser, detta ska se ut mycket känner till.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

I exemplet ovan skapar en tabell med namnet kunder med två kolumner, Förnamn och efternamn.  Varje kolumn har definierats med datatypen VARCHAR(25) som begränsar data till 25 tecken.  Attributen grundläggande för en tabell, samt andra, är främst samma som andra databaser.  Datatyper definieras för varje kolumn och säkerställer integriteten hos dina data.  Index kan läggas till att förbättra prestanda genom att i/o.  Partitionering kan läggas till att förbättra prestanda när du behöver ändra data.

[Byta namn på] [ RENAME] en tabell i SQL Data Warehouse ser ut så här:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Distribuerade tabeller
Ett nytt grundläggande attribut som introducerades av distribuerade system som SQL Data Warehouse är den **distribution kolumnen**.  Kolumnen distribution är mycket vad det låter som.  Det är den kolumn som avgör hur du distribuerar eller dela dina data i bakgrunden.  När du skapar en tabell utan att ange kolumnen distribution tabellen distribueras automatiskt med hjälp av **resursallokering**.  Resursallokering tabeller kan vara tillräckligt i vissa scenarier, kan definiera distributionskolumner avsevärt minska dataflytt under frågor, vilket optimerar prestanda.  I situationer där det finns en liten mängd data i en tabell, välja att skapa tabellen med de **replikera** distributionstypen kopierar data till varje compute-nod och sparar flytt av data vid körning i frågan. Se [distribuerar en tabell] [ Distribute] att lära dig mer om hur du väljer en kolumn för distribution.

## <a name="indexing-and-partitioning-tables"></a>Indexering och partitionerar register
När du blir mer avancerade med SQL Data Warehouse och vill optimera prestanda vill du veta mer om tabelldesign.  Mer information finns i artiklar på [Data tabelltyper][Data Types], [distribuerar en tabell][Distribute], [indexering tabell] [ Index] och [partitionering en tabell][Partition].

## <a name="table-statistics"></a>Tabellstatistik
Statistik är ett mycket viktigt att få bästa möjliga prestanda utanför ditt SQL Data Warehouse.  Eftersom SQL Data Warehouse ännu inte automatiskt skapa och uppdatera statistik, som du har kanske du kan förvänta dig i Azure SQL Database, läsa vår artikel [statistik] [ Statistics] kan vara en av mest viktiga artiklar du läsa för att säkerställa att du får bästa möjliga prestanda från dina frågor.

## <a name="temporary-tables"></a>Temporära tabeller
Temporära tabeller är tabeller som endast finns under din inloggning och inte kan ses av andra användare.  Temporära tabeller kan vara ett bra sätt att förhindra att andra ser tillfälliga resultat och också minska behovet av att rensa.  Eftersom temporära tabeller kan du också använda lokal lagring, ger de snabbare prestanda för vissa åtgärder.  Finns det [tillfällig tabell] [ Temporary] artiklar för mer information om temporära tabeller.

## <a name="external-tables"></a>Externa tabeller
Externa tabeller, även kallat Polybase tabeller är tabeller som kan efterfrågas från SQL Data Warehouse men punkt till externa data från SQL Data Warehouse.  Du kan till exempel skapa en extern tabell som pekar till filer i Azure Blob Storage eller Azure Data Lake Store.  Mer information om hur du skapar och fråga en extern tabell, se [Läs in data med Polybase][Load data with Polybase].  

## <a name="unsupported-table-features"></a>Funktioner som inte stöds tabell
SQL Data Warehouse innehåller många av samma tabell funktionerna som erbjuds av andra databaser, finns men det vissa funktioner som ännu inte stöds.  Nedan visas en lista över några av de funktionerna i tabellen som ännu inte stöds.

| Funktioner som inte stöds |
| --- |
| Primär nyckel, sekundärnycklar unika och kontrollera [tabell begränsningar][Table Constraints] |
| [Unika index][Unique Indexes] |
| [Beräknade kolumner][Computed Columns] |
| [Glesa kolumner][Sparse Columns] |
| [Användardefinierade typer][User-Defined Types] |
| [Sekvens][Sequence] |
| [Utlösare][Triggers] |
| [Indexerade vyer][Indexed Views] |
| [Synonymer][Synonyms] |

## <a name="table-size-queries"></a>Tabell storlek frågor
Ett enkelt sätt att identifiera utrymme och rader som används av en tabell i var och en av de 60-distributioner är att använda [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Men kan med hjälp av DBCC-kommandon ganska begränsa.  Dynamiska hanteringsvyer (av DMV: er) kan du se mycket mer detaljer som ger dig mycket större kontroll över resultatet av frågan.  Börja med att skapa den här vyn, kommer att anges av många av våra exempel i den här och andra artiklar.

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
Den här frågan returnerar rader och utrymme för tabellen.  Det är en bra fråga för att se vilka tabeller som är dina största tabeller och om de är resursallokering replikerade eller distribuerade hash.  För distribuerade hash-tabeller visar även kolumnen distribution.  I de flesta fall ska största tabellerna hash som distribueras med ett grupperat columnstore-index.

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
Mer information finns i artiklar på [Data tabelltyper][Data Types], [distribuerar en tabell][Distribute], [indexering tabell] [ Index], [Partitionering en tabell][Partition], [underhålla tabellstatistik] [ Statistics] och [Temporära tabeller][Temporary].  Mer information om metodtips finns [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
