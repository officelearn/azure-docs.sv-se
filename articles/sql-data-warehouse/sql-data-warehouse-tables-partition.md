---
title: Partitionstabeller i Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och exempel för att använda tabellpartitioner i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d3557be2fd8fdb459571d2c792302963e17e4471
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189401"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partitionstabeller i SQL Data Warehouse
Rekommendationer och exempel för att använda tabellpartitioner i Azure SQL Data Warehouse.

## <a name="what-are-table-partitions"></a>Vad är tabellpartitioner?
Tabellpartitioner kan du dela upp dina data i mindre grupper av data. I de flesta fall skapas tabellpartitioner på en datumkolumn. Partitionering stöds på alla typer för SQL Data Warehouse-tabell. inklusive grupperade, grupperat index och heap. Partitionering stöds även på alla typer av distribution, inklusive hash- eller resursallokering som distribueras.  

Partitionering kan dra dataprestanda för underhåll och fråga. Om den förmåner båda eller bara en beror på hur data har lästs in och om samma kolumn kan användas för båda, eftersom partitionering kan bara utföras på en kolumn.

### <a name="benefits-to-loads"></a>Fördelar med att belastning
Den främsta fördelen med partitionering i SQL Data Warehouse är att förbättra effektivitet och prestanda för inläsning av data med hjälp av partitionen borttagning, växlar och koppla. I de flesta fall är data partitionerade på ett datum som är beroende av den ordning i vilken data läses in i databasen. En av de största fördelarna med att använda partitioner för att lagra data den undvikande av transaktionsloggning. Helt enkelt infogar, uppdaterar eller tar bort data kan vara den enklaste metoden, med lite ansedda och enkelt har kan partitioner under din inläsningen avsevärt förbättra prestanda.

Växla partition kan användas för att snabbt ta bort eller ersätta en del av en tabell.  En försäljning faktatabell kan exempelvis innehålla bara data för de senaste 36 månaderna. I slutet av varje månad raderas den äldsta månaden med försäljningsdata från tabellen.  Dessa data kan tas bort genom att använda en delete-instruktion för att ta bort data för den äldsta månaden. Men kan om du tar bort en stor mängd data rad för rad med en delete-instruktion ta för lång tid, samt skapa risk för stora transaktioner som tar lång tid att återställa om något går fel. En mer optimal metod är att släppa den äldsta partitionen i data. Tar bort en hel partition kan ta när bort enskilda rader kan ta några timmar som sekunder.

### <a name="benefits-to-queries"></a>Fördelar med att frågor
Partitionering kan också användas för att förbättra frågeprestanda. En fråga som filtrerar partitionerade data kan begränsa sökningen till endast partitionerna som är berättigade. Den här metoden för att filtrera kan undvika att en fullständig tabellsökning och bara skanna en mindre deluppsättning av data. Med introduktionen av grupperade columnstore-index, predikat utesluta prestandafördelarna är mindre bra, men i vissa fall det kan vara en fördel med att frågorna. Till exempel om försäljning faktatabellen är indelat i 36 månader med hjälp av fältet Försäljning och sedan frågar filtret på Försäljningsdatum kan hoppa över sökning i partitioner som inte matchar filtret.

## <a name="sizing-partitions"></a>Ändra storlek på partitioner
Partitionering kan användas för att förbättra prestanda vissa scenarier, skapar en tabell med **för många** partitioner kan försämra prestanda under vissa omständigheter.  Dessa problem är särskilt för grupperade columnstore-tabeller. Det är viktigt att veta när du använder partitionering och antalet partitioner som ska skapas för partitionering för att vara till hjälp. Det finns ingen hårda snabb regel om hur många partitioner är för stort, det beror på dina data och hur många partitioner du läser in samtidigt. En lyckad partitioneringsschemat har vanligtvis tiotals till hundratals partitioner, inte tusentals.

När du skapar partitioner på **grupperade** tabeller, är det viktigt att tänka på hur många rader som hör till varje partition. För optimala komprimering och prestanda för grupperade columnstore-tabeller krävs minst 1 miljon rader per distribution och partition. Innan partitioner skapas, delar SQL Data Warehouse redan varje tabell i 60 distribuerade databaser. Partitionering har lagts till i en tabell är ett tillägg till de distributioner som skapats i bakgrunden. Med det här exemplet om försäljning faktatabellen finns 36 månatliga partitioner och tanke på att SQL Data Warehouse har 60 distributioner, sedan försäljning faktatabellen ska innehålla 60 miljoner rader per månad eller 2.1 miljarder rader när alla månader har fyllts i. Om en tabell innehåller färre än det rekommenderade lägsta antalet rader per partition kan du använda färre partitioner för att öka antalet rader per partition. Mer information finns i den [indexering](sql-data-warehouse-tables-index.md) artikel som innehåller de förfrågningar som kan bedöma kvaliteten på cluster columnstore-index.

## <a name="syntax-differences-from-sql-server"></a>Skillnader i syntaxen från SQL Server
SQL Data Warehouse introducerar ett sätt att definiera partitioner som är enklare än SQL Server. Partitionering funktioner och scheman används inte i SQL Data Warehouse som i SQL Server. Allt du behöver göra är i stället identifiera partitionerade kolumn- och gräns-punkter. Syntaxen för partitionering kan vara skiljer sig från SQL Server, är de grundläggande begreppen desamma. SQL Server och SQL Data Warehouse stöder en partition kolumn per tabell, vilket kan vara intervallet partition. Mer information om partitionering finns [partitionerade tabeller och index](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

I följande exempel används den [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) -uttrycket för att partitionera tabellen FactInternetSales i kolumnen OrderDateKey:

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
Migrera bara definitioner för partition av SQL Server till SQL Data Warehouse:

- Ta bort SQL Server [partitionsschema](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Lägg till den [partitionsfunktioner](/sql/t-sql/statements/create-partition-function-transact-sql) definitionen för att skapa tabellen.

Om du migrerar en partitionerad tabell från en SQL Server-instans, följande SQL kan hjälpa dig att ta reda på hur många rader som i varje partition. Tänk på att om du använder samma partitionering detaljnivå i SQL Data Warehouse, minskar antalet rader per partition med en faktor på 60.  

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

## <a name="partition-switching"></a>Växla partition
SQL Data Warehouse stöder partition delning, sammanslagning och växlar. Var och en av dessa funktioner utförs med hjälp av den [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) instruktionen.

Om du vill växla partitioner mellan två tabeller, måste du kontrollera att partitionerna justera på sina respektive gränser och att tabelldefinitionerna stämmer. Som Kontrollera begränsningar inte är tillgängliga för att genomdriva intervallet för värden i en tabell, måste källtabellen innehålla samma partitionsgränserna som måltabellen. Om partitionsgränserna inte är samma sedan, misslyckas partitionsväxeln kommer inte att synkroniseras partitionsmetadata.

### <a name="how-to-split-a-partition-that-contains-data"></a>Att dela en partition som innehåller data
Den effektivaste metoden att dela upp en partition som redan innehåller data är att använda en `CTAS` instruktionen. Om partitionerade tabellen är en klustrad columnstore, måste sedan table-partition vara tom innan den kan delas.

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

Följande fråga söker efter antalet rader med hjälp av den `sys.partitions` vyn katalog:

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

Följande dela kommandot tar emot ett felmeddelande visas:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346 nivå 15, tillstånd 1, rad 44 SPLIT-satsen i ALTER PARTITION-instruktionen misslyckades eftersom partitionen inte är tom. Endast tomma partitioner kan delas i när ett columnstore-index finns i tabellen. Överväg att inaktivera columnstore-indexet innan ALTER PARTITION-instruktionen utfärdas och återskapa sedan columnstore-indexet när ALTER PARTITION har slutförts.

Du kan dock använda `CTAS` att skapa en ny tabell för att lagra data.

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

Eftersom partitionsgränserna justeras tillåts en växel. Detta lämnar källtabellen med en tom partition som du kan därefter delas.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Allt som återstår är att justera data som nya partitionsgränserna med hjälp av `CTAS`, och växla sedan data tillbaka till huvudtabell.

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

När du har slutfört flödet av data, är det en bra idé att uppdatera statistik i måltabellen. Uppdaterar statistik säkerställer statistik korrekt speglar den nya distributionen av data i sina respektive partitioner.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Läsa in nya data i partitioner som innehåller data i ett steg
Läsa in data i partitioner vid växling av partitionen är ett praktiskt sätt mellanlagra nya data i en tabell som inte är synliga för användare växeln på den nya informationen.  Det kan vara svårt på upptaget system behöver bry dig om låsning konkurrensen som är associerade med partitionen växlar.  Rensa befintliga data i en partition, en `ALTER TABLE` används för att bli ombedd att byta ut data.  Sedan en annan `ALTER TABLE` krävdes för att växla i nya data.  I SQL Data Warehouse den `TRUNCATE_TARGET` stöds i den `ALTER TABLE` kommando.  Med `TRUNCATE_TARGET` den `ALTER TABLE` kommando skriver över befintliga data på partitionen med nya data.  Nedan visas ett exempel som använder `CTAS` infogar nya data att skapa en ny tabell med befintliga data, sedan växlar tillbaka alla data i måltabellen, skriva över befintliga data.

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

### <a name="table-partitioning-source-control"></a>Tabellen partitionering källkontroll
Undvik din tabelldefinitionen från **rostangripet** i ditt källkontrollsystem kan du överväga följande metod:

1. Skapa tabellen som en partitionerad tabell men utan partition värden

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

1. `SPLIT` tabellen som en del av distributionsprocessen:

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

Med den här metoden koden i källkontroll är statiskt och partitionering gränsvärden ska kunna vara dynamisk; under utveckling med lagret över tid.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i artiklarna på [Tabellöversikt](sql-data-warehouse-tables-overview.md).

