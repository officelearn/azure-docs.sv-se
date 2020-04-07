---
title: Partitionering tabeller
description: Rekommendationer och exempel för att använda tabellpartitioner i Synapse SQL-pool
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
ms.openlocfilehash: 368276f75128c80b8df326a26acf26c841e9f68a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742685"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Partitionering av tabeller i Synapse SQL-pool

Rekommendationer och exempel för att använda tabellpartitioner i Synapse SQL-pool.

## <a name="what-are-table-partitions"></a>Vad är tabellpartitioner?

Med tabellpartitioner kan du dela upp data i mindre datagrupper. I de flesta fall skapas tabellpartitioner i en datumkolumn. Partitionering stöds på alla Synapse SQL-pooltabelltyper. inklusive klustrade columnstore, klustrade index och heap. Partitionering stöds också på alla distributionstyper, inklusive både hash- eller round robin-distribuerad.  

Partitionering kan gynna dataunderhåll och frågeprestanda. Om det gynnar både eller bara en är beroende av hur data läses in och om samma kolumn kan användas för båda ändamålen, eftersom partitionering endast kan göras på en kolumn.

### <a name="benefits-to-loads"></a>Fördelar med laster

Den främsta fördelen med partitionering i Synapse SQL-pool är att förbättra effektiviteten och prestandan för att läsa in data med hjälp av partitionsborttagning, växling och sammanslagning. I de flesta fall delas data in på en datumkolumn som är nära knuten till den ordning i vilken data läses in i databasen. En av de största fördelarna med att använda partitioner för att underhålla data som undviker transaktionsloggning. Även om du helt enkelt infogar, uppdaterar eller tar bort data kan det vara den enklaste metoden, med lite eftertanke och ansträngning, kan det avsevärt förbättra prestanda genom att använda partitionering under inläsningsprocessen.

Partitionsväxling kan användas för att snabbt ta bort eller ersätta en del av en tabell.  En faktatabell för försäljning kan till exempel innehålla bara data för de senaste 36 månaderna. I slutet av varje månad tas den äldsta månaden med försäljningsdata bort från tabellen.  Dessa data kan tas bort med hjälp av en borttagningssats för att ta bort data för den äldsta månaden. Det kan ta för lång tid att ta bort en stor mängd data rad för rad med en borttagningssats, samt skapa risk för stora transaktioner som tar lång tid att återställa om något går fel. En mer optimal metod är att släppa den äldsta partitionen med data. Var det kan ta timmar att ta bort de enskilda raderna kan det ta några sekunder att ta bort en hel partition.

### <a name="benefits-to-queries"></a>Fördelar med frågor

Partitionering kan också användas för att förbättra frågeprestanda. En fråga som tillämpar ett filter på partitionerade data kan begränsa genomsökningen till endast kvalificerande partitioner. Den här filtreringsmetoden kan undvika en fullständig tabellsökning och bara skanna en mindre delmängd data. Med införandet av klustrade columnstore-index är prestandafördelarna för predikateliminering mindre fördelaktiga, men i vissa fall kan det finnas en fördel för frågor. Om till exempel tabellen försäljning faktum är uppdelad i 36 månader med hjälp av fältet försäljningsdatum, kan frågor som filtrerar på försäljningsdatumet hoppa över sökning i partitioner som inte matchar filtret.

## <a name="sizing-partitions"></a>Ändra storlek på partitioner

Partitionering kan användas för att förbättra prestanda vissa scenarier, skapa en tabell med **för många** partitioner kan skada prestanda under vissa omständigheter.  Dessa problem gäller särskilt för grupperade columnstore-tabeller. För att partitionering ska vara till hjälp är det viktigt att förstå när du ska använda partitionering och antalet partitioner som ska skapas. Det finns ingen hård snabb regel om hur många partitioner som är för många, det beror på dina data och hur många partitioner du läser in samtidigt. Ett framgångsrikt partitioneringsschema har vanligtvis tiotals till hundratals partitioner, inte tusentals.

När du skapar partitioner på **klustrade columnstore-tabeller** är det viktigt att tänka på hur många rader som tillhör varje partition. För optimal komprimering och prestanda för klustrade columnstore-tabeller behövs minst 1 miljon rader per distribution och partition. Innan partitioner skapas delar Synapse SQL-poolen redan upp varje tabell i 60 distribuerade databaser. All partitionering som läggs till i en tabell är ett tillägg till de distributioner som skapas bakom kulisserna. Om tabellen försäljningsfakta innehåller 36 månatliga partitioner och med tanke på att en Synaps-SQL-pool har 60 distributioner, ska tabellen för försäljningsfakta innehålla 60 miljoner rader per månad eller 2,1 miljarder rader när alla månader fylls i. Om en tabell innehåller färre än det rekommenderade minsta antalet rader per partition bör du överväga att använda färre partitioner för att öka antalet rader per partition. Mer information finns i [indexeringsartikeln,](sql-data-warehouse-tables-index.md) som innehåller frågor som kan bedöma kvaliteten på klusterkolonhandelsindex.

## <a name="syntax-differences-from-sql-server"></a>Syntaxskillnader från SQL Server

Synapse SQL-pool introducerar ett sätt att definiera partitioner som är enklare än SQL Server. Partitioneringsfunktioner och scheman används inte i Synapse SQL-pool som de är i SQL Server. I stället behöver du bara identifiera partitionerad kolumn och gränspunkter. Syntaxen för partitionering kan skilja sig något från SQL Server, men de grundläggande begreppen är desamma. SQL Server och Synapse SQL pool stöder en partitionskolumn per tabell, som kan vara områdespartition. Mer information om partitionering finns i [Partitionerade tabeller och index](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

I följande exempel används [CREATE TABLE-satsen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för att partitionera tabellen FactInternetSales i kolumnen OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrera partitionering från SQL Server

Så här migrerar du SQL Server-partitionsdefinitioner till Synapse SQL-pool helt enkelt:

- Eliminera SQL [Server-partitionsschemat](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Lägg till [partitionsfunktionsdefinitionen](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i CREATE-tabellen.

Om du migrerar en partitionerad tabell från en SQL Server-instans kan följande SQL hjälpa dig att räkna ut antalet rader som finns i varje partition. Tänk på att om samma partitioneringsgranularitet används i Synapse SQL-pool, minskar antalet rader per partition med en faktor på 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partitionsväxling

Synapse SQL-pool stöder partitionsdelning, sammanslagning och växling. Var och en av dessa funktioner körs med hjälp av [ALTER TABLE-satsen.](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Om du vill växla partitioner mellan två tabeller måste du se till att partitionerna justeras på sina respektive gränser och att tabelldefinitionerna matchar. Eftersom kontrollbegränsningar inte är tillgängliga för att framtvinga värdeintervallet i en tabell måste källtabellen innehålla samma partitionsgränser som måltabellen. Om partitionsgränserna inte är desamma misslyckas partitionsväxeln eftersom partitionsmetadata inte synkroniseras.

### <a name="how-to-split-a-partition-that-contains-data"></a>Så här delar du en partition som innehåller data

Den mest effektiva metoden för att dela en partition `CTAS` som redan innehåller data är att använda en sats. Om den partitionerade tabellen är en klustrad columnstore måste tabellpartitionen vara tom innan den kan delas.

I följande exempel skapas en partitionerad columnstore-tabell. Den infogar en rad i varje partition:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

Följande fråga hittar radantalet `sys.partitions` med hjälp av katalogvyn:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Följande delade kommando får ett felmeddelande:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Nivå 15, Stat 1, Rad 44 SPLIT-sats för ALTER PARTITION-satsen misslyckades eftersom partitionen inte är tom. Endast tomma partitioner kan delas in när det finns ett columnstore-index i tabellen. Överväg att inaktivera columnstore-indexet innan du utfärdar ALTER PARTITION-satsen och sedan återskapa columnstore-indexet när ALTER PARTITION är klar.

Du kan dock `CTAS` använda för att skapa en ny tabell för att lagra data.

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

När partitionsgränserna är justerade tillåts en växel. Detta lämnar källtabellen med en tom partition som du sedan kan dela.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Allt som finns kvar är att justera data `CTAS`till de nya partitionsgränserna med hjälp av och sedan växla tillbaka data till huvudtabellen.

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

När du har slutfört förflyttningen av data är det en bra idé att uppdatera statistiken i måltabellen. Genom att uppdatera statistiken säkerställs att statistiken korrekt återspeglar den nya fördelningen av uppgifterna i deras respektive partitioner.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Läsa in nya data i partitioner som innehåller data i ett steg

Att läsa in data i partitioner med partitionsväxling är ett bekvämt sätt att iscensätta nya data i en tabell som inte är synlig för användarna växeln i de nya data.  Det kan vara utmanande på upptagna system för att hantera låsning påstående i samband med partitionsväxling.  För att rensa ut befintliga data `ALTER TABLE` i en partition måste en som används för att växla ut data.  Sedan `ALTER TABLE` krävdes en annan för att växla in de nya data.  I Synapse SQL-pool stöds `TRUNCATE_TARGET` alternativet `ALTER TABLE` i kommandot.  Med `TRUNCATE_TARGET` `ALTER TABLE` kommandot skriver befintliga data i partitionen med nya data.  Nedan finns ett `CTAS` exempel som använder för att skapa en ny tabell med befintliga data, infogar nya data och växlar sedan tillbaka alla data till måltabellen och skriver över befintliga data.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
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

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Källkontroll för tabellpartitionering

Om du vill undvika att **tabelldefinitionen rostar** i källstyrningssystemet kan du överväga följande metod:

1. Skapa tabellen som en partitionerad tabell men utan partitionsvärden

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT`tabellen som en del av distributionsprocessen:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Med den här metoden förblir koden i källkontrollen statisk och partitioneringsgränsvärdena tillåts vara dynamiska. utvecklas med databasen över tiden.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i artiklarna i [Tabellöversikt](sql-data-warehouse-tables-overview.md).
