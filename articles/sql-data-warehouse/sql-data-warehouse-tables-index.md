---
title: Indexering tabeller i Azure SQL Data Warehouse | Microsoft Azure
description: Rekommendationer och exempel för indexering tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: fe19510d9b4c6311923b4b2ea15f133249e6cbd5
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190047"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexering tabeller i SQL Data Warehouse
Rekommendationer och exempel för indexering tabeller i Azure SQL Data Warehouse.

## <a name="what-are-index-choices"></a>Vad är val av index?

SQL Data Warehouse erbjuder flera indexeringsalternativ inklusive [klustrade kolumnlagringsindex](/sql/relational-databases/indexes/columnstore-indexes-overview), [klustrade index och icke-grupperade index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), och icke-index så kallade [heap ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Om du vill skapa en tabell med ett index i [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentation.

## <a name="clustered-columnstore-indexes"></a>Grupperade columnstore-index
Som standard skapar SQL Data Warehouse ett grupperat kolumnlagringsindex om inga indexalternativ anges för en tabell. Grupperade columnstore-tabeller har både den högsta nivån av datakomprimering och den bästa övergripande frågeprestanda.  Grupperade columnstore-tabeller kommer Allmänt överträffar klustrade index eller heap-tabeller och har vanligtvis det bästa valet för stora tabeller.  Därmed behöver är grupperade den bästa platsen för att starta när du är osäker på hur du indexera tabellen.  

För att skapa ett grupperat columnstore-tabell, helt enkelt ange KLUSTRADE COLUMNSTORE-INDEX i WITH-satsen eller lämna WITH-satsen:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Det finns några scenarier där grupperade inte kan vara ett bra alternativ:

- Columnstore-tabeller stöder inte varchar(max), nvarchar(max) och varbinary(max). Överväg att heap eller grupperat index i stället.
- Columnstore-tabeller kan vara mindre effektivt för tillfälliga data. Överväg att heap och till och med temporära tabeller.
- Små tabeller med färre än 60 miljoner rader. Överväg att heap-tabeller.

## <a name="heap-tables"></a>Heap-tabeller
När du tillfälligt hanterar data i SQL Data Warehouse, kanske som använder en heap-tabell gör processen gå snabbare. Det beror på att inläsningar till heaps är snabbare än att indextabeller och i vissa fall kan du göra efterföljande Läs från cache.  Om du läser in data endast för att mellanlagra dem innan du kör fler transformationer, är läser in tabellen till heap-tabell mycket snabbare än att läsa in data till ett grupperat columnstore-tabell. Dessutom läser in data till en [temporära tabellen](sql-data-warehouse-tables-temporary.md) läses in snabbare än läser in en tabell till permanent lagring.  

För små uppslagstabeller, mindre än 60 miljoner rader, meningslösa ofta heap-tabeller.  Klustret columnstore-tabeller börjar att uppnå optimal komprimering när det finns fler än 60 miljoner rader.

Om du vill skapa en heap-tabell, anger du bara HEAP i WITH-satsen:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Klustrade och icke-grupperat index
Grupperade index kan överträffar grupperade columnstore-tabeller när en enskild rad behöver hämtas snabbt. Överväg att ett klusterindex eller sekundärt icke-grupperat index för frågor där en enda eller mycket få rad sökning krävs att prestanda med extrem hastighet. Nackdelen med att använda ett grupperat index är att endast de frågor som har nytta är de som använder ett mycket selektiv filter på kolumnen grupperat index. Icke-grupperat index kan läggas till andra kolumner för att förbättra filter på andra kolumner. Men varje index som har lagts till i en tabell lägger till både utrymme och bearbetningstid belastning.

Om du vill skapa ett grupperat index-tabell, anger du bara GRUPPERAT INDEX i WITH-satsen:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Om du vill lägga till en icke-grupperat index för en tabell, använder du följande syntax:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimera grupperade columnstore-index
Grupperade columnstore-tabeller är uppdelade i data i segment.  Med hög segmentkvaliteten är viktigt att uppnå optimala frågeprestanda för en columnstore-tabell.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Segmentkvaliteten är mest optimala där det finns minst 100K rader per komprimerad rad gruppen och få prestanda när antalet rader per rad grupp metod 1 048 576 rader som är de flesta rader kan innehålla en radgrupp.

Den nedan visa kan skapas och används på datorn för att beräkna de genomsnittliga raderna per rad gruppera och identifiera eventuella icke-optimala cluster columnstore-index.  Den sista kolumnen i den här vyn genererar ett SQL-uttryck som kan användas för att återskapa ditt index.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Nu när du har skapat vyn kan du köra den här frågan för att identifiera tabeller med radgrupper med mindre än 100K rader. Naturligtvis kan du öka tröskelvärdet på 100 kB om du letar efter mer optimala segmentkvaliteten. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

När du har kört frågan kan du titta på data och analysera dina resultat. Den här tabellen beskriver vad som ska sökas efter i din grupp rad-analys.

| Kolumn | Hur du använder dessa data |
| --- | --- |
| [table_partition_count] |Om tabellen är partitionerad sedan du förväntar dig att se högre öppna radgrupp räknas. Varje partition i distributionen kan i teorin ha en öppen radgrupp som är kopplade till den. Ta hänsyn till det i din analys. En liten tabell som har partitionerats kan optimeras genom att ta bort partitionering helt och hållet eftersom detta skulle förbättra komprimering. |
| [row_count_total] |Totalt antal postantalet för tabellen. Du kan till exempel använda det här värdet för att beräkna procentandelen rader i komprimerat tillstånd. |
| [row_count_per_distribution_MAX] |Om alla rader är jämnt fördelade det här värdet är antalet rader per distribution. Jämför det här värdet med compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totalt antal rader i kolumnlagringsformatet för tabellen. |
| [COMPRESSED_rowgroup_rows_AVG] |Om det genomsnittliga antalet rader är betydligt mindre än maximalt antal rader för en grupp kan du överväga att använda CTAS eller ALTER INDEX REBUILD för att komprimera data |
| [COMPRESSED_rowgroup_count] |Antal radgrupper i kolumnlagringsformatet. Det här värdet är mycket hög i förhållande till tabellen är en indikator att columnstore-densitet är lågt. |
| [COMPRESSED_rowgroup_rows_DELETED] |Rader raderas logiskt i kolumnlagringsformatet. Om talet är hög i förhållande till tabellstorlek, Överväg att återskapa partitionen eller återskapa indexet eftersom det tar bort dem fysiskt. |
| [COMPRESSED_rowgroup_rows_MIN] |Använd detta tillsammans med AVG och maximalt antal kolumner för att förstå värdeintervallet för radgrupper i din columnstore. Ett lågt värde över belastningen tröskelvärdet (102,400 per partition justerad distribution) tyder på att optimeringar är tillgängliga i Datainläsningen |
| [COMPRESSED_rowgroup_rows_MAX] |Precis som ovan |
| [OPEN_rowgroup_count] |Öppna radgrupper är normalt. En förväntar rimligen en öppen radgrupp per tabelldistribution (60). Överdrivna föreslår över partitioner för inläsning av data. Dubbelkolla partitioneringsstrategin och kontrollera att det är bra |
| [OPEN_rowgroup_rows] |Varje radgrupp kan ha 1 048 576 rader i den högst. Använd det här värdet och se hur full öppna radgrupper är för närvarande |
| [OPEN_rowgroup_rows_MIN] |Öppna grupper anger att data är antingen som takt läses in i tabellen eller att den tidigare belastningen ut återstående rader i den här radgrupp. Använd MIN, MAX, genomsn kolumner för att se hur mycket data är satt i OPEN rad grupper. Det kan vara 100% av alla data för små tabeller! I så fall ALTER INDEX REBUILD för att göra data till columnstore. |
| [OPEN_rowgroup_rows_MAX] |Precis som ovan |
| [OPEN_rowgroup_rows_AVG] |Precis som ovan |
| [CLOSED_rowgroup_rows] |Titta på stängd rad gruppera rader som förstånd kontroll. |
| [CLOSED_rowgroup_count] |Antal stängda radgrupper bör vara lite om någon ses alls. Stängda radgrupper kan konverteras till komprimerade radgrupper med hjälp av ALTER INDEX... Ordna om kommandot. Detta är dock inte normalt krävs. Stängda grupper konverteras automatiskt till columnstore radgrupper av ”tuppel kolumnlagringens” bakgrundsprocess. |
| [CLOSED_rowgroup_rows_MIN] |Stängda radgrupper bör ha en mycket hög fyllning taxa. Om fyllning avgiften för en stängd radgrupp är låg, krävs ytterligare analys av columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Precis som ovan |
| [CLOSED_rowgroup_rows_AVG] |Precis som ovan |
| [Rebuild_Index_SQL] |SQL för att återskapa columnstore-index för en tabell |

## <a name="causes-of-poor-columnstore-index-quality"></a>Orsaker till låg kolumnlagringsindex index kvalitet
Om du har identifierat tabeller med dålig segmentkvaliteten kan du identifiera den bakomliggande orsaken.  Nedan visas några andra vanliga orsaker till dåliga segmentkvaliteten:

1. Minnesbelastning när indexet skapades
2. Stort antal DML-åtgärder
3. Små eller sipprar belastningen åtgärder
4. För många partitioner

Dessa faktorer kan orsaka ett columnstore-index har betydligt mindre än optimala 1 miljon rader per radgrupp. De kan även orsaka rader att gå till radgrupp delta i stället för en komprimerad radgrupp. 

### <a name="memory-pressure-when-index-was-built"></a>Minnesbelastning när indexet skapades
Antal rader per komprimerad radgrupp är direkt relaterade till bredden på raden och mängden minne som är tillgängliga för att bearbeta radgrupp.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Därför är det bästa sättet att ge den session som skriver till din columnstore-index tabeller åtkomst till så mycket minne som möjligt.  Eftersom det inte finns en kompromiss mellan minne och samtidighet, information om rätt minnesallokering beror på data i varje rad i tabellen, informationslagerenheter som allokerats till datorn och hur många samtidighetsfack som du kan ge till sessionen som skriver data till din tabell.

### <a name="high-volume-of-dml-operations"></a>Stort antal DML-åtgärder
Ett stort antal DML-åtgärder som uppdaterar och ta bort rader kan introducera funktionen till columnstore. Detta gäller särskilt om flesta av rader i en radgrupp ändras.

- Tar bort en rad från en komprimerad radgrupp endast logiskt markerar raden som tagits bort. Raden finns kvar i komprimerad radgrupp tills partitionen eller tabellen har återskapats.
- Infoga en rad läggs rad till en intern rowstore-tabell som kallas en delta-radgrupp. Den infoga raden omvandlas inte till columnstore tills radgrupp delta är full och har markerats som stängda. Radgrupper stängs när de når maximal kapacitet för 1 048 576 rader. 
- Uppdatera en rad i kolumnlagringsformatet behandlas som en logisk delete och sedan en infogning. Den infoga raden kan lagras i arkivet delta.

Satsvis update och infogningsåtgärder som överskrider tröskelvärdet bulk på 102 400 rader per partition-justerad distribution gå direkt till columnstore-format. Dock skulle under förutsättning att en jämn fördelning, du behöva ändra över 6.144 miljoner rader i en enda åtgärd görs för. Om antalet rader för en viss partitionsjusterade distribution är mindre än 102,400 går raderna du till andstay delta store det tills tillräckligt med rader har infogats eller ändras för att stänga gruppens raden eller indexet har återskapats.

### <a name="small-or-trickle-load-operations"></a>Små eller sipprar belastningen åtgärder
Små läser in flödet i SQL Data Warehouse kallas ibland också som sipprar belastning. De representerar vanligen en nästan konstant ström av data som matas in i systemet. Antal rader är dock inte särskilt stor eftersom den här strömmen är nära kontinuerlig. Data är ofta avsevärt under tröskelvärdet som krävs för en direkt till columnstore-format.

I sådana fall är det ofta bättre att hamnar först data i Azure blob storage och låt den ackumuleras innan de läses in. Den här metoden kallas ofta *mikrobatchbearbetning*.

### <a name="too-many-partitions"></a>För många partitioner
En annan sak att tänka på är effekten av partitionering på grupperade columnstore-tabeller.  Om du redan innan partitionering, SQL Data Warehouse delar du in dina data i 60 databaser.  Partitionering ytterligare delar in dina data.  Om du partitionerar data bör du överväga att som **varje** partition måste minst 1 miljon rader för att dra nytta av ett grupperat kolumnlagringsindex.  Om du partitionera tabellen i 100 partitioner, så att din tabell måste minst 6 miljarder rader för att dra nytta av ett grupperat columnstore-index (60 distributioner * 100 partitioner * 1 miljoner rader). Om din tabell med 100 partitioner inte har 6 miljarder rader, antingen minska antalet partitioner eller överväger att använda en heap-tabell i stället.

När dina tabeller har lästs in med vissa data, följer du de stegen nedan för att identifiera och återskapa tabeller med icke-optimala klustrade columnstore-index.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Bygga om index för att förbättra segmentkvaliteten
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Steg 1: Identifiera eller skapa användare som använder rätt resurs-klass
Ett snabbt sätt att förbättra omedelbart segmentkvaliteten är att återskapa indexet.  SQL som returneras av vyn ovan returnerar en ALTER INDEX REBUILD-instruktion som kan användas för att återskapa ditt index. När du återskapar dina index, är det viktigt att du allokerar tillräckligt med minne för att sessionen som återskapar ditt index.  Gör detta genom att öka resursklass för en användare som har behörighet att återskapa indexet på den här tabellen till minimum som rekommenderade. 

Nedan visas ett exempel på hur du allokera mer minne till en användare genom att öka deras resursklass. Om du vill arbeta med resursklasser Se [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Steg 2: Återskapa grupperade columnstore-index med högre resource klass användare
Logga in som användare i steg 1 (t.ex. LoadUser) som är med hjälp av en högre resursklass och kör ALTER INDEX-instruktioner. Var noga med att den här användaren har behörigheten ALTER till tabellerna där indexet återskapas. De här exemplen visar hur du återskapa hela columnstore-indexet eller återskapa en enda partition. För stora tabeller är det mer praktiska att återskapa indexerar en partition i taget.

Alternativt kan du i stället för att återskapa indexet, du kan kopiera tabellen till en ny tabell [med hjälp av CTAS](sql-data-warehouse-develop-ctas.md). Hur är bäst? För stora mängder data, CTAS är normalt snabbare än [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). För mindre mängder data, ALTER INDEX är enklare att använda och kräver inte att du byta ut tabellen. 

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

När ett index i SQL Data Warehouse är en offline-åtgärd.  Mer information om att bygga om index finns i avsnittet ALTER INDEX REBUILD i [Columnstore-index defragmentering](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), och [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Steg 3: Kontrollera klustrade columnstore-segmentens kvalitet har förbättrats
Kör frågan vilka identifierade tabell med dåligt segmentera kvalitet och verifiera segmentkvaliteten har förbättrats.  Om inte att förbättra segmentkvaliteten kan bero det på att raderna i tabellen är extra breda.  Överväg att använda en högre resursklass eller DWU när dina index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Bygga om index med CTAS och växla partition
Det här exemplet används den [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) -instruktionen och partitionsväxling för att återskapa en partition table. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Mer information om hur du skapar partitioner som använder CTAS igen finns i [använder partitioner i SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [utveckla tabeller](sql-data-warehouse-tables-overview.md).

