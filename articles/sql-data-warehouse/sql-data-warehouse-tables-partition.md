---
title: Partitionering tabeller i SQL Data Warehouse | Microsoft Docs
description: "Komma igång med Tabellpartitionering i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a28cb1f8a2e48332b344566620dc49b29d9d3c99
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partitionering tabeller i SQL Data Warehouse
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

Partitionering stöds på alla SQL Data Warehouse tabelltyper; inklusive grupperade columnstore, grupperat index och heap.  Partitionering stöds även på alla typer av distribution, inklusive hash- eller resursallokering distribueras.  Partitionering aktiverar görs du vill dela upp dina data i mindre grupper av data och i de flesta fall partitionering på ett datum.

## <a name="benefits-of-partitioning"></a>Fördelarna med partitionering
Partitionering kan dra prestandadata för underhåll och fråga.  Om den fördelar både eller bara en beror på hur data har lästs in och om samma kolumn kan användas för båda, eftersom partitionering kan bara utföras på en kolumn.

### <a name="benefits-to-loads"></a>Fördelar med att belastning
Den största fördelen med partitionering i SQL Data Warehouse är att förbättra effektiviteten och prestanda för inläsning av data med hjälp av partition borttagning, växlar och koppla.  I de flesta fall partitionerat data på ett datum som är beroende av den ordning i vilken data läses in i databasen.  En av de största fördelarna med att använda partitioner för att upprätthålla data den undvikande av transaktionsloggning.  Även om bara infoga, uppdatera eller ta bort data kan vara den enklaste metoden med lite tankar och prestanda, kan med partitionering under din inläsningen avsevärt förbättra prestanda.

Växla partition kan användas för att snabbt ta bort eller ersätta ett avsnitt i en tabell.  En försäljning faktatabellen kan innehålla bara data för de senaste 36 månaderna.  I slutet av varje månad raderas den äldsta månaden av försäljningsinformation från tabellen.  Dessa data kan tas bort genom att använda en delete-instruktion för att ta bort data för den äldsta månaden.  Men kan om du tar bort en stor mängd data rad för rad med en delete-instruktion ta för lång tid, samt skapa risken för stora transaktioner som tar lång tid att återställa om något går fel.  En mer optimala metoden är att ta bort den äldsta partitionen i data.  När du tar bort enskilda rader kan ta timmar, ta ta bort en hel partition sekunder.

### <a name="benefits-to-queries"></a>Fördelar för frågor
Partitionering kan också användas för att förbättra frågeprestanda.  En fråga som filtrerar partitionerade data kan begränsa sökningen till kvalificerande partitionerna. Den här metoden för filtrering kan undvika att en fullständig tabellgenomsökning och bara skanna en mindre deluppsättning av data. Predikat eliminering prestandafördelarna är mindre användbara med introduktionen av grupperade columnstore-index, men i vissa fall det kan vara en fördel med att frågorna.  Till exempel om försäljning faktatabellen delas upp i 36 månader med fältet Försäljning och sedan frågar filtret på Försäljningsdatum kan hoppa över sökning i partitioner som inte matchar filtret.

## <a name="partition-sizing-guidance"></a>Vägledning för partition storlek
Medan partitionering kan användas för att förbättra prestandan för vissa scenarier, skapar en tabell med **för många** partitioner kan försämra prestanda under vissa omständigheter.  Dessa problem är särskilt för grupperade columnstore-tabeller.  Partitionering för att vara användbara, är det viktigt att förstå när du ska använda partitionering och antalet partitioner för att skapa.  Ingen hårda snabb regel om hur många partitioner är för många, det beror på dina data och hur många partitioner du läser in till samtidigt.  En lyckad partitioneringsschema har vanligtvis flera hundratals partitioner, inte tusentalsavgränsare.

När du skapar partitioner på **grupperade columnstore** tabeller, är det viktigt att tänka på hur många rader som hör till varje partition.  För optimala komprimering och prestanda för grupperade columnstore-tabeller krävs minst 1 miljon rader per distribution och partition.  Innan partitioner skapas, delas SQL Data Warehouse redan varje tabell i 60 distribuerade databaser.  Partitionering lagts till i en tabell är utöver de distributioner som skapats i bakgrunden.  Med det här exemplet, om försäljning faktatabellen innehåller 36 månatliga partitioner och med hänsyn till att SQL Data Warehouse har 60 distributioner, sedan försäljning faktatabellen ska innehålla 60 miljoner rader per månad eller 2.1 miljarder rader när alla månader fylls i.  Om en tabell innehåller avsevärt färre än det rekommenderade minsta antalet rader per partition, Överväg att använda färre partitioner för att öka antalet rader per partition.  Se även den [indexering] [ Index] artikel som innehåller de förfrågningar som kan köras på SQL-datalagret för att bedöma kvaliteten på klustret columnstore-index.

## <a name="syntax-difference-from-sql-server"></a>Syntaxen skillnaden från SQL Server
SQL Data Warehouse introducerar ett förenklat sätt att definiera partitioner, som skiljer sig något från SQL Server.  Partitionering funktioner och scheman används inte i SQL Data Warehouse som i SQL Server.  I stället är allt du behöver göra identifiera partitionerad kolumn och gräns punkter.  Syntaxen för partitionering kan skilja sig något från SQL Server, är grundbegreppen samma.  SQL Server och SQL Data Warehouse stöder en partition kolumn per tabell, som kan vara låg.  Mer information om partitionering finns [partitionerade tabeller och index][Partitioned Tables and Indexes].

Följande exempel på ett SQL Data Warehouse partitionerad [CREATE TABLE] [ CREATE TABLE] -instruktionen partitionerar tabellen FactInternetSales på kolumnen OrderDateKey:

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
Migrera bara definitioner för SQL Server-partitionen till SQL Data Warehouse:

* Ta bort SQL Server [partitionsschema][partition scheme].
* Lägg till den [partitionsfunktioner] [ partition function] definitionen för att skapa tabellen.

Om du migrerar en partitionerad tabell från en SQL Server-instans kan följande SQL kan hjälpa dig att ta reda på hur många rader som i varje partition.  Tänk på att om samma partitionering detaljnivå används i SQL Data Warehouse, minskar antalet rader per partition med en faktor på 60.  

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

## <a name="workload-management"></a>Arbetsbelastningshantering
En sista att ta hänsyn till tabellen partition beslut är [hantering av arbetsbelastning][workload management].  Hantering av arbetsbelastning i SQL Data Warehouse är främst hanteringen av minne och samtidighet.  Den maximala mängd minne som allokerats till varje distributionsplats vid körning av fråga styrs av resursklasser i SQL Data Warehouse.  Partitioner är det bästa storlek med hänsyn till andra faktorer som de minne som behövs för att skapa grupperade columnstore-index.  Grupperade columnstore-index förmånen avsevärt när de tilldelas mer minne.  Därför vill du se till att bygga upp index en partition inte är för lite minne. Öka mängden tillgängligt minne för att din fråga kan uppnås genom att växla från standardroll smallrc, till en av rollerna som largerc.

Genom att fråga resurs resursstyrningen dynamiska hanteringsvyer finns information om fördelningen av minne per distribution. I verkligheten kan understiger din minnestilldelningen följande bilder. Detta ger dock en nivå som du kan använda vid bedömning av partitionerna för hanteringsåtgärder för data.  Försök undvika att ändra storlek på dina partitioner efter minnestilldelningen som tillhandahålls av den extra stor resursklassen. Om din partitioner växer utöver denna bild kör risken för minnesbelastning vilket i sin tur leder till mindre optimal komprimering.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Växla partition
SQL Data Warehouse stöder partition dela sammanslagning och växlar. Dessa funktioner är excuted med hjälp av den [ALTER TABLE] [ ALTER TABLE] instruktionen.

Om du vill växla partitioner mellan två tabeller måste du kontrollera att justera partitionerna på deras respektive gränser och att tabelldefinitionerna matchar. Eftersom de inte kunna använda olika värden i en tabell Kontrollbegränsningar måste källtabellen innehålla samma partitionsgränserna som måltabellen. Om detta inte är fallet misslyckas partitionsväxeln som partition metadata inte synkroniseras.

### <a name="how-to-split-a-partition-that-contains-data"></a>Så här delar du en partition som innehåller data
Det effektivaste sättet att dela en partition som redan innehåller data är att använda en `CTAS` instruktion. Om partitionerade tabellen är ett grupperat columnstore måste sedan partition table vara tom innan du kan dela.

Nedan visas partitionerade columnstore exempeltabell som innehåller en rad i varje partition:

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


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Genom att skapa statistik-objektet, kontrollera att tabellmetadata är mer exakt. Om vi utelämnar statistik skapas sedan använder SQL Data Warehouse standardvärden. För information om statistik hittar [statistik][statistics].
> 
> 

Vi kan sedan fråga efter antal raden med det `sys.partitions` vyn katalog:

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

Om vi försöker att dela den här tabellen, kommer vi får ett felmeddelande:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Ignorerad 35346 nivå 15, tillstånd 1, rad 44 SPLIT-satsen i ALTER PARTITION-instruktionen misslyckades eftersom partitionen inte är tom.  Endast tomma partitioner kan delas i när ett columnstore-index finns i tabellen. Överväg att inaktivera columnstore-indexet innan ALTER PARTITION-instruktionen utfärdas och återskapa sedan columnstore-indexet när ALTER PARTITION har slutförts.

Vi kan dock använda `CTAS` att skapa en ny tabell för våra data.

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

En växel tillåts som partitionsgränserna är justerade. Detta lämnar källtabellen med en tom partition som senare kan vi dela.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Allt som återstår för att göra är att justera våra data till de nya partitionsgränser med hjälp av `CTAS` och växel våra data tillbaka till huvudtabellen

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

När du har slutfört flytt av data är en bra idé att uppdatera statistik i måltabellen så de motsvarar den nya fördelningen av data i deras respektive partitioner:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabell partitionering källkontroll
Att undvika att dina tabelldefinitionen från **rostangripet** i ditt källkontrollsystem som du bör du överväga följande metod:

1. Skapa tabellen som en partitionerad tabell men saknar värden för partition

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
                    ()
                )
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

Med den här metoden koden i källkontroll är statisk och partitionering gränsvärden tillåts vara dynamiska; under utveckling för datalagret över tid.

## <a name="next-steps"></a>Nästa steg
Mer information finns i artiklar på [tabell översikt][Overview], [Data tabelltyper][Data Types], [distribuerar en tabell][Distribute], [indexering av en tabell][Index], [underhålla tabellstatistik] [ Statistics] och [temporära tabeller][Temporary].  Mer information om metodtips finns [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
