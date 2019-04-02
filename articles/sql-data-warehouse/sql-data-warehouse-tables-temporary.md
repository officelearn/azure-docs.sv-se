---
title: Temporära tabeller i SQL Data Warehouse | Microsoft Docs
description: Grundläggande riktlinjer för att använda temporära tabeller och visar principerna för sessionen på temporära tabeller.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/01/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 23a62e28700ad5fd733040c43ea0eec225fd286f
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793109"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Temporära tabeller i SQL Data Warehouse
Den här artikeln innehåller grundläggande information för att använda temporära tabeller och visar principerna för sessionen på temporära tabeller. Med hjälp av informationen i den här artikeln kan hjälpa dig modularize din kod, förbättra både återanvändning och enkelt underhåll av din kod.

## <a name="what-are-temporary-tables"></a>Vad är temporära tabeller?
Temporära tabeller är användbara vid bearbetning av data, särskilt under omvandlingen där mellanliggande resultatet är tillfälligt. Temporära tabeller finns på nivån sessionen i SQL Data Warehouse.  De är bara synliga för sessionen har skapats och släpps automatiskt när den aktuella sessionen loggar ut.  Temporära tabeller erbjuder en prestandafördelar eftersom resultaten skrivs till lokala i stället för Fjärrlagring.

## <a name="create-a-temporary-table"></a>Skapa en temporär tabell
Temporära tabeller som skapas genom din tabellnamn med en `#`.  Exempel:

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

Temporära tabeller kan även skapas med en `CTAS` med exakt samma metod:

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
``` 

> [!NOTE]
> `CTAS` är ett kraftfullt kommando och har fördelen att effektivt vid användningen av transaktionsloggutrymmet. 
> 
> 

## <a name="dropping-temporary-tables"></a>Tar bort temporära tabeller
När en ny session skapas ska inga temporära tabeller finnas.  Men om du anropar den samma lagrade proceduren, vilket skapar en tillfällig med samma namn, så se till att din `CREATE TABLE` instruktioner har lyckats en enkel före kontroll med en `DROP` kan användas som i följande exempel:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Det är en bra idé att använda det här mönstret för både tabeller och temporära tabeller för kodning konsekvens.  Det är också en bra idé att använda `DROP TABLE` att ta bort temporära tabeller när du är klar med dem i din kod.  I lagrad procedur utveckling är det vanligt att drop-kommandon buntas ihop i slutet av en procedur för att se till att dessa objekt rensas.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing kod
Eftersom temporära tabeller kan visas någonstans i en användarsession, kan detta utnyttjas för att hjälpa dig modularize programkoden.  Till exempel genererar följande lagrade procedur DDL för att uppdatera all statistik i databasen efter Statistiknamn.

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

I det här skedet är den enda åtgärden som har inträffat skapandet av en lagrad procedur som genererar en tillfällig tabell #stats_ddl med DDL-instruktionerna.  Den här lagrade proceduren utelämnar #stats_ddl om det redan finns för att säkerställa att den inte misslyckas om mer än en gång i en session.  Men eftersom det finns inga `DROP TABLE` i slutet av den lagrade proceduren när den lagrade proceduren har slutförts blir den skapade tabellen så att de kan läsas utanför den lagrade proceduren.  I SQL Data Warehouse är till skillnad från andra SQL Server-databaser, det möjligt att använda den temporära tabellen utanför den procedur som skapade den.  Temporära tabeller i SQL Data Warehouse kan användas för **var som helst** sessionen. Detta kan leda till mer modulära och hanterbar kod som i följande exempel:

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

## <a name="temporary-table-limitations"></a>Begränsningar för temporär tabell
SQL Data Warehouse införa några begränsningar när du implementerar temporära tabeller.  För närvarande bara begränsade temporära tabeller stöds.  Globala temporära tabeller stöds inte.  Vyer kan dessutom inte skapas på temporära tabeller.  Temporära tabeller kan bara skapas med hash eller resursallokering distribution.  Replikerade temporära tabelldistribution stöds inte. 

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [Tabellöversikt](sql-data-warehouse-tables-overview.md).

