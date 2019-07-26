---
title: Temporära tabeller i SQL Data Warehouse | Microsoft Docs
description: Grundläggande rikt linjer för att använda temporära tabeller och visar principerna för temporära tabeller på sidnivå.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e43e52e56ec7abbf5d8eb879defef54bd7d50658
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479832"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Temporära tabeller i SQL Data Warehouse
Den här artikeln innehåller grundläggande information om hur du använder temporära tabeller och visar principerna för temporära tabeller på sidnivå. Med hjälp av informationen i den här artikeln kan du hjälpa dig att modularize din kod, förbättra både åter användning och enklare underhåll av din kod.

## <a name="what-are-temporary-tables"></a>Vad är temporära tabeller?
Temporära tabeller är användbara när du bearbetar data, särskilt under omvandling där de mellanliggande resultaten är tillfälliga. I SQL Data Warehouse finns temporära tabeller på den tillfälliga nivån.  De visas bara för den session där de skapades och tas bort automatiskt när sessionen loggar ut.  Temporära tabeller ger en prestanda förmån eftersom deras resultat skrivs till lokala platser i stället för Fjärrlagring.

## <a name="create-a-temporary-table"></a>Skapa en tillfällig tabell
Temporära tabeller skapas genom att prefixet för `#`ditt tabell namn används.  Exempel:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Temporära tabeller kan också skapas med en `CTAS` med exakt samma metod:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS`är ett kraftfullt kommando och har den extra fördelen att vara effektiv i användningen av transaktions logg utrymme. 
> 
> 

## <a name="dropping-temporary-tables"></a>Släpper temporära tabeller
Inga temporära tabeller bör finnas när en ny session skapas.  Men om du anropar samma lagrade procedur, vilket skapar en tillfällig med samma namn, så att du kan se till att `CREATE TABLE` dina instruktioner lyckas med en enkel för hands kontroll med en `DROP` kan användas som i följande exempel:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

För att koda konsekvens är det en bra idé att använda det här mönstret för både tabeller och temporära tabeller.  Det är också en bra idé att använda `DROP TABLE` för att ta bort temporära tabeller när du är klar med dem i din kod.  Vid utveckling av lagrade procedurer är det vanligt att se Drop-kommandon som samlats ihop i slutet av en procedur för att se till att dessa objekt rensas.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modulär kod
Eftersom temporära tabeller kan ses var som helst i en användarsession, kan detta utnyttjas för att hjälpa dig att modularize program koden.  Följande lagrade procedur genererar till exempel DDL för att uppdatera all statistik i databasen efter statistik namn.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

I det här skedet är den enda åtgärd som har inträffat skapandet av en lagrad procedur som genererar en temporär tabell, #stats_ddl, med DDL-instruktioner.  Den här lagrade proceduren tar #stats_ddl om den redan finns för att säkerställa att den inte fungerar om den körs mer än en gång i en session.  Men eftersom det inte finns något `DROP TABLE` i slutet av den lagrade proceduren när den lagrade proceduren har slutförts, så lämnar den den skapade tabellen så att den kan läsas utanför den lagrade proceduren.  I SQL Data Warehouse, till skillnad från andra SQL Server databaser, är det möjligt att använda den temporära tabellen utanför proceduren som skapade den.  SQL Data Warehouse temporära tabeller kan användas **var som helst** i sessionen. Detta kan leda till mer modulär och hanterbar kod som i följande exempel:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Tillfälligt tabell begränsningar
SQL Data Warehouse har ett par begränsningar vid implementering av temporära tabeller.  För närvarande stöds endast temporära tabeller för session definitions område.  Globala temporära tabeller stöds inte.  Dessutom kan inte vyer skapas i temporära tabeller.  Temporära tabeller kan bara skapas med hash-eller resursallokering-distribution.  Replikerad temporär tabell distribution stöds inte. 

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [tabell översikten](sql-data-warehouse-tables-overview.md).

