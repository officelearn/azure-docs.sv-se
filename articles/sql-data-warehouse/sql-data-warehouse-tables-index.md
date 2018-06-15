---
title: Indexering tabeller i Azure SQL Data Warehouse | Microsoft Azure
description: Rekommendationer och exempel för indexering tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 75d3638326bc1bf2f72997fa9d5d5feabc837a62
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527162"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexering tabeller i SQL Data Warehouse
Rekommendationer och exempel för indexering tabeller i Azure SQL Data Warehouse.

## <a name="what-are-index-choices"></a>Vad är index alternativ?

SQL Data Warehouse erbjuder flera indexeringsalternativ inklusive [grupperat kolumnlagringsindex](/sql/relational-databases/indexes/columnstore-indexes-overview), [grupperat index och icke-grupperade index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), och icke-index alternativet kallas även [heap ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Om du vill skapa en tabell med ett index som finns på [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentation.

## <a name="clustered-columnstore-indexes"></a>Grupperade columnstore-index
Som standard skapar SQL Data Warehouse grupperade columnstore-indexet när inga Indexalternativ för har angetts för en tabell. Grupperade columnstore-tabeller erbjuda både den högsta nivån av datakomprimering samt den bästa frågeprestandan.  Grupperade columnstore-tabeller kommer vanligtvis överträffar grupperade index eller heap-tabeller och är vanligtvis det bästa alternativet för stora tabeller.  Därför är grupperade columnstore enklast att starta när du är osäker på hur du index i tabellen.  

Om du vill skapa ett grupperat columnstore-tabellen bara ange GRUPPERAT COLUMNSTORE-INDEX i WITH-satsen eller lämna WITH-satsen:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Det finns några scenarier där det grupperade columnstore inte får vara ett bra alternativ:

- Columnstore-tabeller stöder inte varchar(max), nvarchar(max) och varbinary(max). Överväg att heap eller grupperat index i stället.
- Columnstore-tabeller kan vara mindre effektiv för tillfälliga data. Överväg heap och eventuellt även temporära tabeller.
- Små tabeller med mindre än 100 miljoner rader. Överväg att heap-tabeller.

## <a name="heap-tables"></a>Heap-tabeller
När du tillfälligt hamnar data i SQL Data Warehouse, kan vara att använda en heap-tabell gör den övergripande processen snabbare. Det beror på att belastningen till heap är snabbare än index tabeller och i vissa fall kan du göra efterföljande Läs från cache.  Om du läser in data endast för att mellanlagra innan du kör flera omvandlingar är läser in tabellen heap tabellen mycket snabbare än inläsning av data till ett grupperat columnstore-tabellen. Dessutom data läses in i en [tillfällig tabell](sql-data-warehouse-tables-temporary.md) laddas snabbare än att läsa in en tabell i permanent lagringsutrymme.  

Ofta heap tabeller vara klokt med små sökning tabeller, mindre än 100 miljoner rader.  Klustret columnstore-tabeller börjar att uppnå optimal komprimering när det finns fler än 100 miljoner rader.

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
Grupperade index kan överträffar grupperade columnstore-tabeller när en rad måste hämtas snabbt. Frågor där en enda eller mycket få raden sökning krävs att prestanda med extrema hastighet, Överväg i ett klusterindex eller sekundärt icke-grupperat index. Nackdelen med att använda ett grupperat index är att endast de frågor som har nytta är de som använder ett starkt selektiv filter på kolumnen grupperat index. En icke-grupperat index kan läggas till andra kolumner för att förbättra filter på andra kolumner. Dock varje index som har lagts till i en tabell lägger till både utrymme och bearbetningstid belastning.

Om du vill skapa ett grupperat index, bara ange GRUPPERAT INDEX i WITH-satsen:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Om du vill lägga till en icke-grupperat index för en tabell, Använd följande syntax:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimera grupperade columnstore-index
Grupperade columnstore-tabeller är uppdelade i data i segment.  Det är viktigt att uppnå optimal prestanda för en tabell som columnstore har segment av hög kvalitet.  Segment kvalitet kan mätas med antalet rader i en komprimerad grupp.  Segment kvalitet är mest optimala där det finns minst 100K rader per komprimerade rad gruppen och få prestanda som antalet rader per rad grupp metod 1 048 576 rader som är en grupp kan innehålla de flesta rader.

Den nedan vyn kan skapas och används på datorn för att beräkna de genomsnittliga raderna per rad gruppen och identifiera eventuella optimalt klustret columnstore-index.  Den sista kolumnen i den här vyn genererar ett SQL-uttryck som kan användas för att återskapa ditt index.

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

Nu när du har skapat vyn kan du köra den här frågan för att identifiera tabeller med radgrupper med färre än 100K rader. Naturligtvis kan du vill öka tröskelvärdet för 100K om du behöver mer optimala segment kvalitet. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

När du har kört frågan kan du titta på data och analysera dina resultat. Den här tabellen beskrivs vad som ska sökas efter i raden grupp analyser.

| Kolumn | Hur du använder dessa data |
| --- | --- |
| [table_partition_count] |Om tabellen är partitionerad kan du förvänta att se senare öppna grupp räknas. Varje partition i distributionen kan ha en öppen grupp som är kopplade till den i teorin. Ta hänsyn till det i dina analyser. En mindre tabell som har har partitionerats kan optimeras genom att ta bort partitionering helt eftersom detta skulle förbättra komprimering. |
| [row_count_total] |Totalt radantal för tabellen. Du kan till exempel använda det här värdet för att beräkna procentandelen rader i det komprimerade tillståndet. |
| [row_count_per_distribution_MAX] |Om alla rader fördelas jämnt skulle värdet vara mål för antalet rader per distribution. Jämför detta med compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totalt antal rader i tabellen columnstore-format. |
| [COMPRESSED_rowgroup_rows_AVG] |Om det genomsnittliga antalet rader är betydligt mindre än det maximala antal rader för en grupp, sedan Överväg att använda CTAS eller ALTER INDEX REBUILD för att komprimera data |
| [COMPRESSED_rowgroup_count] |Antal radgrupper i columnstore-format. Om värdet är mycket hög i förhållande till tabellen är en indikator att columnstore densitet är låg. |
| [COMPRESSED_rowgroup_rows_DELETED] |Logiskt ta bort rader i columnstore-format. Överväg att återskapa partitionen eller återskapa indexet eftersom det tar bort dem fysiskt om talet är hög i förhållande till tabellen. |
| [COMPRESSED_rowgroup_rows_MIN] |Använd detta tillsammans med kolumnerna AVG och MAX för att förstå värdeintervall för radgrupper i din columnstore. Ett lågt värde över belastningen tröskelvärdet (102,400 per partition justerad distribution) föreslår att optimeringar är tillgängliga i inläsningen |
| [COMPRESSED_rowgroup_rows_MAX] |Som ovan |
| [OPEN_rowgroup_count] |Öppna radgrupper är normalt. En förväntar rimligen en öppen grupp per tabell distribution (60). Alltför stora tal föreslår datainläsning över partitioner. Kontrollera partitioneringsstrategi så att den är bra |
| [OPEN_rowgroup_rows] |Varje grupp kan innehålla 1 048 576 rader som maximalt. Använd det här värdet och se hur full öppna radgrupper är för närvarande |
| [OPEN_rowgroup_rows_MIN] |Öppna grupper tyda på att data är antingen som takt läses in i tabellen eller att den tidigare belastningen ut återstående rader i den här raden-gruppen. Använd MIN, MAX, AVG kolumner för att se hur mycket data är satt i öppna rad grupper. Det kan vara 100% av alla data för små tabeller! I så fall ALTER INDEX REBUILD för att göra data till columnstore. |
| [OPEN_rowgroup_rows_MAX] |Som ovan |
| [OPEN_rowgroup_rows_AVG] |Som ovan |
| [CLOSED_rowgroup_rows] |Titta på stängd rad gruppera rader som förstånd kontroll. |
| [CLOSED_rowgroup_count] |Antal avslutade radgrupper ska låg om någon ses alls. Stängd radgrupper kan konverteras till komprimerade radgrupper med hjälp av ALTER INDEX... Organisera om kommandot. Detta är dock inte normalt krävs. Stängd grupper konverteras automatiskt till columnstore radgrupper av ”tuppel till tuppelflyttar” bakgrunden. |
| [CLOSED_rowgroup_rows_MIN] |Stängd radgrupper ska ha en mycket hög fill hastighet. Om fill för en stängd grupp är låg, krävs ytterligare analys av columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Som ovan |
| [CLOSED_rowgroup_rows_AVG] |Som ovan |
| [Rebuild_Index_SQL] |SQL att återskapa columnstore-index för en tabell |

## <a name="causes-of-poor-columnstore-index-quality"></a>Orsaker till dåliga columnstore-index kvalitet
Om du har identifierat tabeller med dålig segment kvalitet, som du vill identifiera den bakomliggande orsaken.  Nedan visas några andra vanliga orsaker till dåliga segment kvalitet:

1. Minnesbelastning när indexet har skapats
2. Stor volym med DML-operationer
3. Liten eller sipprar belastningen åtgärder
4. För många partitioner

Dessa faktorer kan orsaka ett columnstore-index har betydligt mindre än de optimala 1 miljon raderna per grupp. De kan också medföra rader som ska gå att delta raden gruppen i stället för en komprimerad grupp. 

### <a name="memory-pressure-when-index-was-built"></a>Minnesbelastning när indexet har skapats
Antalet rader per komprimerade grupp är direkt relaterade till bredden på raden och mängden minne för att bearbeta gruppen raden.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Därför är det bästa sättet att ge session, som skriver till din columnstore-index tabeller åtkomst till så mycket minne som möjligt.  Eftersom det är en kompromiss mellan minne och samtidighet, riktlinjer om rätt minnesallokering beror på data i varje rad i tabellen, informationslagerenheter som allokerats till ditt system och antal samtidiga fack som du kan ge till sessionen som skriver data till tabellen.  Som bästa praxis rekommenderar vi börjar med xlargerc om du använder DW300 eller mindre largerc om du använder DW400 DW600 och mediumrc om du använder DW1000 och senare.

### <a name="high-volume-of-dml-operations"></a>Stor volym med DML-operationer
En stor volym med DML-operationer som uppdatera och ta bort rader kan installera funktionen i columnstore. Detta gäller särskilt när flesta av rader i en grupp ändras.

- Ta bort en rad från en komprimerad grupp bara logiskt markerar raden som tagits bort. Raden finns kvar i gruppen komprimerade raden tills partitionen eller tabellen har återskapats.
- Infoga en rad lägger till raden till en intern rowstore-tabell som kallas en rad delta-grupp. Den infogade raden konverteras inte till columnstore tills gruppen delta raden är full och har markerats som stängda. Radgrupper stängs när de når maximal kapacitet för 1 048 576 rader. 
- Uppdatera en rad i columnstore format behandlas som en logisk delete och insert. Den infogade raden kan lagras i arkivet delta.

Batch update och infogningsåtgärder som överskrider tröskelvärdet bulk på 102 400 rader per partitionsjusterade distributionsplats gå direkt till columnstore-format. Dock skulle förutsatt en jämn fördelning du behöva ändra överstiger 6.144 miljoner rader i en enda åtgärd för att det ska ske. Om antalet rader för en viss partitionsjusterade distributionsplats är mindre än 102,400 går raderna du till andstay delta store det tills tillräckligt rader har infogats eller ändras för att stänga gruppen eller indexet har återskapats.

### <a name="small-or-trickle-load-operations"></a>Liten eller sipprar belastningen åtgärder
Liten läser in flödet i SQL Data Warehouse kallas även ibland som sipprar belastning. De representerar vanligtvis en nära konstant ström av data som inhämtas av systemet. Antal rader är dock inte särskilt stor eftersom dataströmmen är nära kontinuerlig. Data är oftast avsevärt under tröskelvärdet som krävs för en direkt till columnstore-format.

I sådana fall är det ofta bättre att hamna först data i Azure blob storage och låt den ackumuleras innan de läses in. Den här metoden kallas ofta *micro batchbearbetning*.

### <a name="too-many-partitions"></a>För många partitioner
En annan sak att tänka på är effekten av partitionering på grupperade columnstore-tabeller.  Innan du partitionering delar SQL Data Warehouse redan dina data i 60 databaser.  Partitionering ytterligare delar upp dina data.  Om du partitionera data bör du som **varje** partitionen måste minst 1 miljoner rader att dra nytta av ett grupperat columnstore-index.  Om du partitionera tabellen i 100-partitioner, så måste minst 6 miljarder rader att dra nytta av ett grupperat columnstore-index för tabellen (60 distributioner * 100 partitioner * 1 miljoner rader). Om din 100 partitionstabellen saknar 6 miljarder rader, minska antalet partitioner eller Överväg att använda en heap-tabellen i stället.

När dina tabeller har lästs in med vissa data, följer du de nedanstående steg för att identifiera och återskapa tabeller med optimalt grupperade columnstore-index.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Bygga om index för att förbättra kvaliteten för segmentet
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Steg 1: Identifiera eller skapa användare som använder rätt resurs-klass
Det är ett snabbt sätt att förbättra kvalitet segment omedelbart återskapa indexet.  SQL som returneras av vyn ovan returnerar en ALTER INDEX REBUILD-instruktion som kan användas för att återskapa ditt index. När ditt index, måste du allokera tillräckligt med minne för sessionen som återskapar ditt index.  Gör detta genom att öka resursklassen för en användare som har behörighet att återskapa indexet för den här tabellen med den rekommenderade minimala. Resursklassen för databasanvändare för ägare kan inte ändras om du inte har skapat en användare i systemet måste göra det nu. Den minsta rekommenderade resursklassen är xlargerc om du använder DW300 eller mindre largerc om du använder DW400 DW600 och mediumrc om du använder DW1000 och senare.

Nedan visas ett exempel på hur du allokera mer minne till en användare genom att öka sin resursklassen. Om du vill arbeta med resursklasser finns [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Steg 2: Återskapa grupperade columnstore-index med högre resurs klassen användare
Logga in som användare från steg 1 (t.ex. LoadUser) som är med hjälp av en högre resursklassen, och kör ALTER INDEX-satser. Var noga med att den här användaren har behörigheten ALTER tabeller där index återskapas. Följande exempel visar hur du återskapa hela columnstore-indexet eller återskapa en partition. För stora tabeller är det mer praktiska återskapa index en partition i taget.

Alternativt kan du i stället för att återskapa indexet du kopiera tabellen till en ny tabell [med CTAS](sql-data-warehouse-develop-ctas.md). Hur är bäst? För stora mängder data, CTAS är normalt snabbare än [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). För mindre datamängder ALTER INDEX är enklare att använda och kräver inte att du byta tabellen. Se **bygga om index med CTAS och växla partition** nedan för mer information om hur du återskapa index med CTAS.

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

När ett index i SQL Data Warehouse är en åtgärd som är offline.  Mer information om att bygga om index finns i avsnittet ALTER INDEX REBUILD i [Columnstore-index defragmentering](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), och [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Steg 3: Kontrollera grupperade columnstore segment kvalitet har förbättrats
Kör frågan vilka identifierade tabell med dålig segmentera kvalitet och verifiera segment kvalitet har förbättrats.  Om segment kvalitet inte förbättrar, bero det på att raderna i tabellen är extra breda.  Överväg att använda en högre resursklassen eller DWU när ditt index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Bygga om index med CTAS och växla partition
Det här exemplet används den [Skapa tabell AS Välj (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) -instruktionen och partitionsväxling för att återskapa en partition table. 

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

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Mer information om att återskapa partitioner som använder CTAS finns [med partitioner i SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns [utveckla tabeller](sql-data-warehouse-tables-overview.md).

