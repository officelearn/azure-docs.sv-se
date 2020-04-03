---
title: Indexeringstabeller
description: Rekommendationer och exempel för indexering av tabeller i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d5acc2b69ed521af4fd4777dc9f3496290078379
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583277"
---
# <a name="indexing-tables-in-synapse-sql-pool"></a>Indexera tabeller i Synapse SQL-pool

Rekommendationer och exempel för indexering av tabeller i Synapse SQL-pool.

## <a name="index-types"></a>Indextyper

Synapse SQL pool erbjuder flera indexeringsalternativ, inklusive [klustrade columnstore index,](/sql/relational-databases/indexes/columnstore-indexes-overview) [klustrade index och icke-grupperade index,](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)och en icke-index alternativ även känd som [hög](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Om du vill skapa en tabell med ett index finns i dokumentationen [skapa tabell (Synapse SQL-pool).](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)

## <a name="clustered-columnstore-indexes"></a>Grupperade columnstore-index

Som standard skapar Synapse SQL-pool ett grupperat columnstore-index när inga indexalternativ anges i en tabell. Grupperade columnstore-tabeller erbjuder både den högsta nivån av datakomprimering samt den bästa övergripande frågeprestanda.  Grupperade columnstore-tabeller överträffar i allmänhet grupperade index- eller heaptabeller och är vanligtvis det bästa valet för stora tabeller.  Av dessa skäl är klustrade columnstore det bästa stället att börja när du är osäker på hur du indexerar tabellen.  

Om du vill skapa en grupperad kolumnbutikstabell anger du bara CLUSTERED COLUMNSTORE INDEX i WITH-satsen eller låter WITH-satsen vara inaktiverad:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Det finns några scenarier där klustrade columnstore kanske inte är ett bra alternativ:

- Columnstore-tabeller stöder inte varchar(max), nvarchar(max) och varbinary(max). Överväg att lägga eller gruppera index i stället.
- Columnstore-tabeller kan vara mindre effektiva för tillfälliga data. Tänk hög och kanske till och med temporära tabeller.
- Små bord med mindre än 60 miljoner rader. Överväg att lägga upp tabeller.

## <a name="heap-tables"></a>Heap-tabeller

När du tillfälligt landar data i Synapse SQL-pool kan det hända att det går snabbare att använda en heap-tabell. Detta beror på att belastningar till högar är snabbare än att indexera tabeller och i vissa fall kan den efterföljande läsningen göras från cacheminnet.  Om du läser in data bara för att iscensätta dem innan du kör fler omvandlingar är det mycket snabbare att läsa in tabellen till heap-tabellen än att läsa in data i en grupperad columnstore-tabell. Dessutom läses data in i en [tillfällig tabell](sql-data-warehouse-tables-temporary.md) snabbare än att läsa in en tabell till permanent lagring.  

För små uppslagstabeller, mindre än 60 miljoner rader, är ofta heap-tabeller meningsfulla.  Kluster columnstore tabeller börjar uppnå optimal komprimering när det finns mer än 60 miljoner rader.

Om du vill skapa en heap-tabell anger du bara HÖGEN i WITH-satsen:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Klustrade och icke-grupperade index

Klustrade index kan överträffa klustrade columnstore-tabeller när en enda rad måste hämtas snabbt. För frågor där en eller flera rader krävs för att utföra en hög hastighet bör du överväga ett klusterindex eller ett sekundärt index som inte är grupperat. Nackdelen med att använda ett klustrade index är att endast frågor som gynnar är de som använder ett mycket selektivt filter i den klustrade indexkolumnen. För att förbättra filtret på andra kolumner kan ett icke-grupperat index läggas till i andra kolumner. Varje index som läggs till i en tabell lägger dock till både utrymme och bearbetningstid i belastningar.

Om du vill skapa en grupperad indextabell anger du bara CLUSTERED INDEX i WITH-satsen:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Om du vill lägga till ett icke-grupperat index i en tabell använder du följande syntax:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimera klustrade columnstore-index

Grupperade columnstore-tabeller är ordnade i data i segment.  Att ha hög segmentkvalitet är avgörande för att uppnå optimal frågeprestanda i en columnstore-tabell.  Segmentkvaliteten kan mätas med antalet rader i en komprimerad radgrupp.  Segmentkvaliteten är mest optimal där det finns minst 100 000 rader per komprimerad radgrupp och resultatvinst när antalet rader per radgrupp närmar sig 1 048 576 rader, vilket är de mest rader en radgrupp kan innehålla.

Nedanstående vy kan skapas och användas på ditt system för att beräkna de genomsnittliga raderna per radgrupp och identifiera eventuella suboptimala klusterkolonhandelsindex.  Den sista kolumnen i den här vyn genererar ett SQL-uttryck som kan användas för att återskapa dina index.

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

Nu när du har skapat vyn kör du den här frågan för att identifiera tabeller med radgrupper med färre än 100 000 rader. Naturligtvis kanske du vill öka tröskeln på 100K om du letar efter mer optimal segmentkvalitet.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

När du har kört frågan kan du börja titta på data och analysera dina resultat. I den här tabellen förklaras vad du ska leta efter i radgruppsanalysen.

| Kolumn | Så här använder du dessa data |
| --- | --- |
| - Jag vet inte vad du table_partition_count. |Om tabellen är partitionerad kan du förvänta dig att se högre antal öppna radgrupper. Varje partition i distributionen kan i teorin ha en öppen radgrupp associerad med den. Räkna in detta i din analys. En liten tabell som har partitionerats kan optimeras genom att ta bort partitioneringen helt och hållet eftersom detta skulle förbättra komprimeringen. |
| - Jag vet row_count_total inte vad du går för. |Totalt antal rader för tabellen. Du kan till exempel använda det här värdet för att beräkna procentandelen rader i komprimerat tillstånd. |
| - Jag vet inte vad du row_count_per_distribution_MAX. |Om alla rader är jämnt fördelade skulle det här värdet vara målantalet rader per fördelning. Jämför det här värdet med compressed_rowgroup_count. |
| - Jag vet inte vad du COMPRESSED_rowgroup_rows. |Totalt antal rader i columnstore-format för tabellen. |
| - Jag vet inte vad du COMPRESSED_rowgroup_rows_AVG. |Om det genomsnittliga antalet rader är betydligt mindre än det maximala antalet rader för en radgrupp bör du överväga att använda CTAS eller ALTER INDEX REBUILD för att komprimera om data |
| - Jag vet inte vad du COMPRESSED_rowgroup_count. |Antal radgrupper i columnstore-format. Om detta tal är mycket högt i förhållande till tabellen är det en indikator på att columnstore-densiteten är låg. |
| - Jag vet inte vad du COMPRESSED_rowgroup_rows_DELETED. |Rader tas logiskt bort i columnstore-format. Om talet är högt i förhållande till tabellstorleken bör du överväga att återskapa partitionen eller återskapa indexet eftersom det tas bort fysiskt. |
| - Jag vet inte vad du COMPRESSED_rowgroup_rows_MIN. |Använd detta tillsammans med kolumnerna AVG och MAX för att förstå intervallet med värden för radgrupperna i din columnstore. Ett lågt tal över belastningströskeln (102 400 per partitionsjusterad distribution) tyder på att optimeringar är tillgängliga i datainläsningen |
| - Jag vet inte vad du COMPRESSED_rowgroup_rows_MAX. |Som ovan |
| - Jag vet inte vad du OPEN_rowgroup_count. |Öppna radgrupper är normala. Man skulle rimligen förvänta sig en OPEN radgrupp per tabellfördelning (60). För stora tal tyder på datainläsning över partitioner. Dubbelkolla partitioneringsstrategin för att se till att det är ljud |
| - Jag vet inte vad du OPEN_rowgroup_rows. |Varje radgrupp kan ha högst 1 048 576 rader. Använd det här värdet för att se hur fullständiga de öppna radgrupperna är för närvarande |
| - Jag vet OPEN_rowgroup_rows_MIN inte vad du går för. |Öppna grupper anger att data antingen laddas i tabellen eller att den tidigare belastningen spills över återstående rader i den här radgruppen. Använd kolumnerna MIN, MAX, AVG för att se hur mycket data som sitter i OPEN-radgrupper. För små tabeller kan det vara 100% av alla data! I vilket fall ALTER INDEX REBUILD för att tvinga data till columnstore. |
| - Jag vet inte vad du OPEN_rowgroup_rows_MAX. |Som ovan |
| - Jag vet inte vad du OPEN_rowgroup_rows_AVG. |Som ovan |
| - Jag vet inte vad du CLOSED_rowgroup_rows. |Titta på de stängda radraderna som en sanity check. |
| - Jag vet inte vad du CLOSED_rowgroup_count. |Antalet slutna radgrupper bör vara lågt om några ses alls. Stängda radgrupper kan konverteras till komprimerade radgrupper med hjälp av ALTER INDEX ... KOMMANDOT ORDNA OM. Detta är dock normalt inte nödvändigt. Stängda grupper konverteras automatiskt till columnstore-radgrupper med bakgrundsprocessen "tuple mover". |
| - Jag vet inte vad du CLOSED_rowgroup_rows_MIN. |Slutna radgrupper bör ha en mycket hög fyllningshastighet. Om fyllningshastigheten för en sluten radgrupp är låg krävs ytterligare analys av columnstore. |
| - Jag vet inte vad du CLOSED_rowgroup_rows_MAX. |Som ovan |
| - Jag vet CLOSED_rowgroup_rows_AVG inte vad du går för. |Som ovan |
| - Jag vet inte vad du Rebuild_Index_SQL. |SQL för att återskapa columnstore-index för en tabell |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causes of poor columnstore index quality (Orsaker till låg columnstore-indexkvalitet)

Om du har identifierat tabeller med dålig segmentkvalitet vill du identifiera grundorsaken.  Nedan följer några andra vanliga orsaker till dålig segmentkvalitet:

1. Minnestryck när index byggdes
2. Hög volym DML-åtgärder
3. Små eller sippra lastoperationer
4. För många partitioner

Dessa faktorer kan orsaka att ett columnstore-index har betydligt mindre än de optimala 1 miljon raderna per radgrupp. De kan också orsaka att rader går till deltaradgruppen i stället för en komprimerad radgrupp.

### <a name="memory-pressure-when-index-was-built"></a>Minnestryck när index byggdes

Antalet rader per komprimerad radgrupp är direkt relaterade till radens bredd och mängden minne som är tillgängligt för att bearbeta radgruppen.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Därför är det bästa sättet att ge sessionen som skriver till dina columnstore indextabeller tillgång till så mycket minne som möjligt.  Eftersom det finns en kompromiss mellan minne och samtidighet beror vägledningen om rätt minnesallokering på data i varje rad i tabellen, de informationslagerenheter som allokerats till ditt system och antalet samtidighetsplatser som du kan ge till sessionen som skriver data till tabellen.

### <a name="high-volume-of-dml-operations"></a>Hög volym DML-åtgärder

En stor mängd DML-åtgärder som uppdaterar och tar bort rader kan införa ineffektivitet i columnstore. Detta gäller särskilt när majoriteten av raderna i en radgrupp ändras.

- Om du tar bort en rad från en komprimerad radgrupp markeras bara raden som borttagen. Raden finns kvar i den komprimerade radgruppen tills partitionen eller tabellen återskapas.
- Om du infogar en rad läggs raden till i en intern radvåningstabell som kallas deltaradgrupp. Den infogade raden konverteras inte till columnstore förrän deltaradgruppen är full och markeras som stängd. Radgrupper stängs när de når den maximala kapaciteten på 1 048 576 rader.
- Uppdatering av en rad i columnstore-format bearbetas som en logisk borttagning och sedan en infoga. Den infogade raden kan lagras i deltaarkivet.

Batchade uppdaterings- och infogningar som överskrider masströskeln på 102 400 rader per partitionsjusterad distribution går direkt till columnstore-formatet. Men om du antar en jämn fördelning måste du ändra mer än 6,144 miljoner rader i en enda åtgärd för att detta ska inträffa. Om antalet rader för en viss partitionsjusterad fördelning är mindre än 102 400 går raderna till deltaarkivet och stannar där tills tillräckligt många rader har infogats eller ändrats för att stänga radgruppen eller indexet har byggts om.

### <a name="small-or-trickle-load-operations"></a>Små eller sippra lastoperationer

Små laster som flödar in i Synapse SQL-pool kallas ibland också trickle laster. De representerar vanligtvis en nästan konstant ström av data som intas av systemet. Men eftersom denna ström är nära kontinuerlig volymen av rader är inte särskilt stor. Oftast är data betydligt under det tröskelvärde som krävs för en direkt belastning till columnstore-format.

I dessa situationer är det ofta bättre att landa data först i Azure blob storage och låta den ackumuleras före inläsning. Denna teknik kallas ofta *mikro-batching*.

### <a name="too-many-partitions"></a>För många partitioner

En annan sak att tänka på är effekten av partitionering på dina klustrade columnstore-tabeller.  Innan partitioneringen delar Synapse SQL-poolen redan upp dina data i 60 databaser.  Partitionering ytterligare delar dina data.  Om du partitionerar dina data bör du tänka på att **varje** partition behöver minst 1 miljon rader för att dra nytta av ett grupperat columnstore-index.  Om du partitionerar tabellen i 100 partitioner behöver tabellen minst 6 miljarder rader för att dra nytta av ett grupperat columnstore-index (60 distributioner *100 partitioner* 1 miljon rader). Om tabellen med 100 partitioner inte har 6 miljarder rader minskar du antingen antalet partitioner eller överväger att använda en heap-tabell i stället.

När tabellerna har lästs in med vissa data följer du stegen nedan för att identifiera och återskapa tabeller med suboptimala klustrade columnstore-index.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Återskapa index för att förbättra segmentets kvalitet

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Steg 1: Identifiera eller skapa användare som använder rätt resursklass

Ett snabbt sätt att omedelbart förbättra segmentets kvalitet är att återskapa indexet.  SQL som returneras av ovanstående vy returnerar en ALTER INDEX REBUILD-sats som kan användas för att återskapa dina index. När du återskapar indexen måste du allokera tillräckligt med minne till sessionen som återskapar indexet.  Det gör du genom att öka resursklassen för en användare som har behörighet att återskapa indexet i den här tabellen till det rekommenderade minimumet.

Nedan följer ett exempel på hur du allokerar mer minne till en användare genom att öka deras resursklass. Om du vill arbeta med resursklasser finns i [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Steg 2: Återskapa klustrade columnstore-index med användare av högre resursklass

Logga in som användare från steg 1 (t.ex. LoadUser), som nu använder en högre resursklass, och kör ALTER INDEX-satserna. Se till att den här användaren har ALTER-behörighet till de tabeller där indexet byggs om. De här exemplen visar hur du återskapar hela columnstore-indexet eller hur du återskapar en enda partition. På stora tabeller är det mer praktiskt att återskapa index en enda partition i taget.

Alternativt kan du kopiera tabellen till en ny tabell med [CTAS](sql-data-warehouse-develop-ctas.md)i stället för att återskapa indexet. Vilken väg är bäst? För stora datavolymer är CTAS vanligtvis snabbare än [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). För mindre datavolymer är ALTER INDEX enklare att använda och kräver inte att du byter ut tabellen.

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

Att återskapa ett index i Synapse SQL-pool är en offlineåtgärd.  Mer information om hur du återskapar index finns i avsnittet ALTER INDEX REBUILD i [Defragmentering](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)för Kolumnstore Index och [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Steg 3: Kontrollera klustrade columnstore segment kvalitet har förbättrats

Kör frågan igen som identifierade tabellen med dålig segmentkvalitet och verifierar segmentkvaliteten har förbättrats.  Om segmentkvaliteten inte förbättrades kan det vara så att raderna i tabellen är extra breda.  Överväg att använda en högre resursklass eller DWU när du återskapar dina index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Återskapa index med CTAS och partitionsväxling

I det här exemplet används [UTTRYCKET SKAPA TABELL AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) och partitionsväxling för att återskapa en tabellpartition.

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

Mer information om hur du återskapar partitioner med CTAS finns [i Använda partitioner i Synapse SQL-pool](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i [Utveckla tabeller](sql-data-warehouse-tables-overview.md).
