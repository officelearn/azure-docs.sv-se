---
title: Indexering tabeller i SQL Data Warehouse | Microsoft Azure
description: "Komma igång med tabellen indexering i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: 3e617674-7b62-43ab-9ca2-3f40c41d5a88
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2016
ms.author: shigu;barbkess
ms.openlocfilehash: b205ed47833f675286539705e2754d2ea3821b8e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexering tabeller i SQL Data Warehouse
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

SQL Data Warehouse erbjuder flera indexeringsalternativ inklusive [grupperat kolumnlagringsindex][clustered columnstore indexes], [grupperat index och icke-grupperade index] [ clustered indexes and nonclustered indexes].  Dessutom det ger också en inga indexalternativet kallas även [heap][heap].  Den här artikeln beskriver fördelarna med varje Indextypen samt tips på hur du hämtar de flesta prestanda från ditt index. Se [skapa table-syntax] [ create table syntax] för mer information om hur du skapar en tabell i SQL Data Warehouse.

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

* Columnstore-tabeller stöder inte varchar(max), nvarchar(max) och varbinary(max).  Överväg att heap eller grupperat index i stället.
* Columnstore-tabeller kan vara mindre effektiv för tillfälliga data.  Överväg heap och eventuellt även temporära tabeller.
* Små tabeller med mindre än 100 miljoner rader.  Överväg att heap-tabeller.

## <a name="heap-tables"></a>Heap-tabeller
När du tillfälligt hanterar data i SQL Data Warehouse kan processen gå snabbare om du använder en heap-tabell.  Det beror på att belastningen till heap är snabbare än index tabeller och i vissa fall kan du göra efterföljande Läs från cache.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare om du läser in tabellen till en heap-tabell än om du läser in data till en grupperad columnstore-tabell. Dessutom data läses in i en [tillfällig tabell] [ Temporary] läses också mycket snabbare än att läsa in en tabell i permanent lagringsutrymme.  

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
Grupperade index kan överträffar grupperade columnstore-tabeller när en rad måste hämtas snabbt.  Frågor där en enda eller mycket få raden sökning krävs att prestanda med extrema hastighet, Överväg i ett klusterindex eller sekundärt icke-grupperat index.  Nackdelen med att använda ett grupperat index är att får endast frågor som använder ett starkt selektiv filter på kolumnen grupperat index.  En icke-grupperat index kan läggas till andra kolumner för att förbättra filter på andra kolumner.  Dock varje index som har lagts till i en tabell lägger till både utrymme och bearbetningstid belastning.

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
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimera grupperade columnstore-index
Grupperade columnstore-tabeller är uppdelade i data i segment.  Det är viktigt att uppnå optimal prestanda för en tabell som columnstore har segment av hög kvalitet.  Segment kvalitet kan mätas med antalet rader i en komprimerad grupp.  Segment kvalitet är mest optimala där det finns minst 100K rader per komprimerade rad gruppen och få prestanda som antalet rader per rad grupp metod 1 048 576 rader som är en grupp kan innehålla de flesta rader.

Den nedan vyn kan skapas och används på datorn för att beräkna de genomsnittliga raderna per rad gruppen och identifiera eventuella optimalt klustret columnstore-index.  Den sista kolumnen i den här vyn genererar som SQL-uttryck som kan användas för att återskapa ditt index.

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

Nu när du har skapat vyn kan du köra den här frågan för att identifiera tabeller med radgrupper med färre än 100K rader.  Naturligtvis kan du vill öka tröskelvärdet för 100K om du behöver mer optimala segment kvalitet. 

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
| [CLOSED_rowgroup_count] |Antal avslutade radgrupper ska låg om någon ses alls. Stängd radgrupper kan konverteras till komprimerade rowg roups med hjälp av ALTER INDEX... REKONSTRUERA kommando. Detta är dock inte normalt krävs. Stängd grupper konverteras automatiskt till columnstore radgrupper av ”tuppel till tuppelflyttar” bakgrunden. |
| [CLOSED_rowgroup_rows_MIN] |Stängd radgrupper ska ha en mycket hög fill hastighet. Om fill för en stängd grupp är låg, krävs ytterligare analys av columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Som ovan |
| [CLOSED_rowgroup_rows_AVG] |Som ovan |
| [Rebuild_Index_SQL] |SQL att återskapa columnstore-index för en tabell |

## <a name="causes-of-poor-columnstore-index-quality"></a>Orsaker till dåliga columnstore-index kvalitet
Om du har identifierat tabeller med dålig segment kvalitet, kommer du vill identifiera den bakomliggande orsaken.  Nedan visas några andra vanliga orsaker till dåliga segment quaility:

1. Minnesbelastning när indexet har skapats
2. Stor volym med DML-operationer
3. Liten eller sipprar belastningen åtgärder
4. För många partitioner

Dessa faktorer kan orsaka ett columnstore-index har betydligt mindre än de optimala 1 miljon raderna per grupp.  De kan också medföra rader som ska gå att delta raden gruppen i stället för en komprimerad grupp. 

### <a name="memory-pressure-when-index-was-built"></a>Minnesbelastning när indexet har skapats
Antalet rader per komprimerade grupp är direkt relaterade till bredden på raden och mängden minne för att bearbeta gruppen raden.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Därför är det bästa sättet att ge session, som skriver till din columnstore-index tabeller åtkomst till så mycket minne som möjligt.  Eftersom det är en kompromiss mellan minne och samtidighet, riktlinjer om rätt minnesallokering beror på data i varje rad i tabellen, hur många DWU du har tilldelat till systemet och mängden samtidighet platser kan du ge till sessionen som är skriver data till tabellen.  Som bästa praxis rekommenderar vi börjar med xlargerc om du använder DW300 eller mindre largerc om du använder DW400 DW600 och mediumrc om du använder DW1000 och senare.

### <a name="high-volume-of-dml-operations"></a>Stor volym med DML-operationer
En stor volym med DML-operationer som uppdatera och ta bort rader kan installera funktionen i columnstore. Detta gäller särskilt när flesta av rader i en grupp ändras.

* Ta bort en rad från en komprimerad grupp bara logiskt markerar raden som tagits bort. Raden finns kvar i gruppen komprimerade raden tills partitionen eller tabellen har återskapats.
* Infoga en rad lägger till raden till en intern rowstore tabell som kallas en rad delta-grupp. Den infogade raden konverteras inte till columnstore tills gruppen delta raden är full och har markerats som stängda. Radgrupper stängs när de når maximal kapacitet för 1 048 576 rader. 
* Uppdatera en rad i columnstore format behandlas som en logisk delete och insert. Den infogade raden kan lagras i arkivet delta.

Batchar update och infoga åtgärder som överskrider tröskelvärdet bulk på 102 400 rader per partition justerade distribution ska skrivas direkt till columnstore-format. Dock skulle förutsatt en jämn fördelning du behöva ändra överstiger 6.144 miljoner rader i en enda åtgärd för att det ska ske. Om antalet rader i en given partition justerade är distribution mindre än 102,400 rader skickas till arkivet delta och förblir det tills tillräckligt rader har infogats eller ändras för att stänga gruppen eller indexet har återskapats.

### <a name="small-or-trickle-load-operations"></a>Liten eller sipprar belastningen åtgärder
Liten läser in flödet i SQL Data Warehouse kallas även ibland som sipprar belastning. De representerar vanligtvis en nära konstant ström av data som inhämtas av systemet. Antal rader är dock inte särskilt stor eftersom dataströmmen är nära kontinuerlig. Data är oftast avsevärt under tröskelvärdet som krävs för en direkt till columnstore-format.

I sådana fall är det ofta bättre att hamna först data i Azure blob storage och låt den ackumuleras innan de läses in. Den här metoden kallas ofta *micro batchbearbetning*.

### <a name="too-many-partitions"></a>För många partitioner
En annan sak att tänka på är effekten av partitionering på grupperade columnstore-tabeller.  Innan du partitionering delar SQL Data Warehouse redan dina data i 60 databaser.  Partitionering ytterligare delar upp dina data.  Om du partitionera data, så ska du anser att **varje** partitionen måste ha minst 1 miljoner rader att dra nytta av ett grupperat columnstore-index.  Om du partitionera tabellen till 100 partitioner kommer din tabell måste ha minst 6 miljarder rader att dra nytta av ett grupperat columnstore-index (60 distributioner * 100 partitioner * 1 miljoner rader). Minska antalet partitioner eller Överväg att använda en heap-tabellen i stället om 100 partitionstabellen saknar 6 miljarder rader.

När dina tabeller har lästs in med vissa data, följer du de nedanstående steg för att identifiera och återskapa tabeller med kolumnlagringsindex optimalt klustret.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Bygga om index för att förbättra kvaliteten för segmentet
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Steg 1: Identifiera eller skapa användare som använder rätt resurs-klass
Det är ett snabbt sätt att förbättra kvalitet segment omedelbart återskapa indexet.  SQL som returneras av vyn ovan returneras en ALTER INDEX REBUILD-instruktion som kan användas för att återskapa ditt index.  När ditt index, måste du allokera tillräckligt med minne för sessionen som kommer att återskapa index.  Gör detta genom att öka resursklassen för en användare som har behörighet att återskapa indexet för den här tabellen med den rekommenderade minimala.  Resursklass av ägare databasanvändare kan inte ändras så att om du inte har skapat en användare i systemet, behöver du göra det nu.  Den minsta rekommenderar vi är xlargerc om du använder DW300 eller mindre largerc om du använder DW400 DW600 och mediumrc om du använder DW1000 och senare.

Nedan visas ett exempel på hur du allokera mer minne till en användare genom att öka sin resursklassen.  Mer information om resurs klasser och hur du skapar en ny användare finns i den [samtidighet och arbetsbelastningen] [ Concurrency] artikel.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Steg 2: Återskapa grupperade columnstore-index med högre resurs klassen användare
Logga in som användaren från steg 1 (t.ex. LoadUser), som nu med en högre resursklassen och kör ALTER INDEX-satser.  Var noga med att den här användaren har behörigheten ALTER tabeller där index återskapas.  Följande exempel visar hur du återskapa hela columnstore-indexet eller återskapa en partition. För stora tabeller är det mer praktiska återskapa index en partition i taget.

Alternativt kan du i stället för att återskapa indexet du kopiera tabellen till en ny tabell med hjälp av [CTAS][CTAS].  Hur är bäst? För stora mängder data, [CTAS] [ CTAS] är normalt snabbare än [ALTER INDEX][ALTER INDEX]. För mindre datamängder [ALTER INDEX] [ ALTER INDEX] är enklare att använda och inte kräver att du vill byta ut tabellen.  Se **bygga om index med CTAS och växla partition** nedan för mer information om hur du återskapa index med CTAS.

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

När ett index i SQL Data Warehouse är en åtgärd som är offline.  Mer information om att bygga om index finns i avsnittet ALTER INDEX REBUILD i [Columnstore-index defragmentering] [ Columnstore Indexes Defragmentation] och avsnittet syntax [ALTER INDEX] [ ALTER INDEX].

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Steg 3: Kontrollera grupperade columnstore segment kvalitet har förbättrats
Kör frågan vilka identifierade tabell med dålig segmentera kvalitet och verifiera segment kvalitet har förbättrats.  Om segment kvalitet inte förbättrar, bero det på att raderna i tabellen är extra breda.  Överväg att använda en högre resursklassen eller DWU när ditt index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Bygga om index med CTAS och växla partition
Det här exemplet används [CTAS] [ CTAS] och partitionsväxling för att återskapa en partition table. 

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

Mer information om att återskapa partitioner som använder `CTAS`, finns det [Partition] [ Partition] artikel.

## <a name="next-steps"></a>Nästa steg
Mer information finns i artiklar på [tabell översikt][Overview], [Data tabelltyper][Data Types], [distribuerar en tabell] [ Distribute], [Partitionering en tabell][Partition], [underhålla tabellstatistik] [ Statistics] och [Temporära tabeller][Temporary].  Mer information om metodtips finns [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[clustered indexes and nonclustered indexes]: https://msdn.microsoft.com/library/ms190457.aspx
[create table syntax]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[clustered columnstore indexes]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
