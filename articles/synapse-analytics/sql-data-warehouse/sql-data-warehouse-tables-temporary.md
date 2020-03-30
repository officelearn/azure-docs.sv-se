---
title: Temporära tabeller
description: Grundläggande vägledning för att använda temporära tabeller i Azure SQL Data Warehouse, som belyser principerna för temporära tabeller på sessionsnivå.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3a8772550e67c250b1a84dbae17d1d3fe6c5c90e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351160"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Temporära tabeller i SQL Data Warehouse
Den här artikeln innehåller viktiga riktlinjer för hur du använder temporära tabeller och belyser principerna för temporära tabeller på sessionsnivå. Med hjälp av informationen i den här artikeln kan hjälpa dig att modularisera din kod, förbättra både återanvändning och enkelt underhåll av din kod.

## <a name="what-are-temporary-tables"></a>Vad är temporära tabeller?
Temporära tabeller är användbara vid bearbetning av data - särskilt under omvandling där de mellanliggande resultaten är övergående. I SQL Data Warehouse finns temporära tabeller på sessionsnivå.  De är bara synliga för den session där de skapades och automatiskt tas bort när sessionen loggas ut.  Temporära tabeller erbjuder en prestandafördel eftersom deras resultat skrivs till lokal lagring i stället för fjärrlagring.

## <a name="create-a-temporary-table"></a>Skapa en tillfällig tabell
Temporära tabeller skapas genom att `#`du förebeskar tabellnamnet med en .  Ett exempel:

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

Temporära tabeller kan `CTAS` också skapas med exakt samma metod:

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
> `CTAS`är ett kraftfullt kommando och har den extra fördelen av att vara effektiv i sin användning av transaktionsloggutrymme. 
> 
> 

## <a name="dropping-temporary-tables"></a>Släppa temporära tabeller
När en ny session skapas bör det inte finnas några temporära tabeller.  Men om du anropar samma lagrade procedur, som skapar en tillfällig `CREATE TABLE` med samma namn, för att `DROP` säkerställa att dina satser lyckas en enkel förexistens kontroll med en kan användas som i följande exempel:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

För kodning konsekvens, är det en god idé att använda detta mönster för både tabeller och temporära tabeller.  Det är också en `DROP TABLE` bra idé att använda för att ta bort temporära tabeller när du är klar med dem i koden.  I den lagrade procedurens utveckling är det vanligt att se släppkommandona som buntas ihop i slutet av en procedur för att säkerställa att dessa objekt rensas.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modulariseringskod
Eftersom temporära tabeller kan ses var som helst i en användarsession kan du utnyttja detta för att hjälpa dig att modularisera programkoden.  Följande lagrade procedur genererar till exempel DDL för att uppdatera all statistik i databasen efter statistiknamn.

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

I det här skedet är den enda åtgärd som har inträffat skapandet av en lagrad procedur som genererar en tillfällig tabell, #stats_ddl, med DDL-satser.  Den här lagrade proceduren #stats_ddl om den redan finns för att säkerställa att den inte misslyckas om den körs mer än en gång i en session.  Men eftersom det `DROP TABLE` inte finns någon i slutet av den lagrade proceduren, när den lagrade proceduren är klar, lämnar den den skapade tabellen så att den kan läsas utanför den lagrade proceduren.  I SQL Data Warehouse, till skillnad från andra SQL Server-databaser, är det möjligt att använda den temporära tabellen utanför proceduren som skapade den.  Sql Data Warehouse tillfälliga tabeller kan användas **var som helst** i sessionen. Detta kan leda till mer modulär och hanterbar kod som i följande exempel:

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

## <a name="temporary-table-limitations"></a>Tillfälliga tabellbegränsningar
SQL Data Warehouse medför ett par begränsningar när tillfälliga tabeller implementeras.  För närvarande stöds endast temporära sessionsomfattade tabeller.  Globala temporära tabeller stöds inte.  Dessutom kan vyer inte skapas på temporära tabeller.  Temporära tabeller kan bara skapas med hash- eller round robin-distribution.  Replikerad temporär tabelldistribution stöds inte. 

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [tabellöversikten](sql-data-warehouse-tables-overview.md).

