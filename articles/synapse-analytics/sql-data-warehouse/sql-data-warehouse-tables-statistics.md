---
title: Skapa, uppdatera statistik
description: Rekommendationer och exempel för att skapa och uppdatera frågeoptimeringsstatistik för tabeller i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5fae2bba0acc4ab462c91f7272694d032fc6ceaa
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742659"
---
# <a name="table-statistics-in-synapse-sql-pool"></a>Tabellstatistik i Synapse SQL-pool

I den här artikeln hittar du rekommendationer och exempel för att skapa och uppdatera frågeoptimeringsstatistik på tabeller i SQL-poolen.

## <a name="why-use-statistics"></a>Varför använda statistik

Ju mer SQL-pool vet om dina data, desto snabbare kan den köra frågor mot den. När du har läst in data i SQL-poolen är insamling av statistik om dina data en av de viktigaste sakerna du kan göra för att optimera dina frågor.

SQL pool fråga optimizer är en kostnadsbaserad optimizer. Den jämför kostnaden för olika frågeplaner och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den plan som ska köras snabbast.

Om till exempel optimeraren uppskattar att datumet då frågan filtreras på returnerar en rad väljer den en plan. Om den uppskattar att det valda datumet returnerar 1 miljon rader returneras en annan plan.

## <a name="automatic-creation-of-statistic"></a>Automatiskt skapande av statistik

När databasalternativet AUTO_CREATE_STATISTICS är aktiverat analyserar SQL-poolen inkommande användarfrågor för saknad statistik.

Om statistik saknas skapar frågeoptimeraren statistik för enskilda kolumner i frågepredikatet eller kopplingsvillkoret för att förbättra kardinalitetsuppskattningarna för frågeplanen.

> [!NOTE]
> Automatisk skapande av statistik är för närvarande aktiverat som standard.

Du kan kontrollera om SQL-poolen har AUTO_CREATE_STATISTICS konfigurerat genom att köra följande kommando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Om SQL-poolen inte har konfigurerats AUTO_CREATE_STATISTICS rekommenderar vi att du aktiverar den här egenskapen genom att köra följande kommando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Dessa uttalanden kommer att utlösa automatisk skapande av statistik:

- VÄLJ
- INFOGA-MARKERA
- CTAS
- UPDATE
- DELETE
- FÖRKLARA när man innehåller en koppling eller förekomsten av ett predikat upptäcks

> [!NOTE]
> Automatiskt skapande av statistik skapas inte i tillfälliga eller externa tabeller.

Automatisk skapande av statistik görs synkront så att du kan ådra dig något försämrade frågeprestanda om kolumnerna saknar statistik. Tiden för att skapa statistik för en enskild kolumn beror på tabellens storlek.

För att undvika mätbara prestandaförsämring bör du se till att statistik har skapats först genom att köra benchmark-arbetsbelastningen innan du profilerar systemet.

> [!NOTE]
> Skapandet av statistik loggas i [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) under en annan användarkontext.

När automatisk statistik skapas tar de formen: _WA_Sys_<8-siffriga kolumn-ID i Hex>_<8-siffrigt tabell-ID i Hex>. Du kan visa statistik som redan har skapats genom att köra kommandot [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Den table_name är namnet på den tabell som innehåller den statistik som ska visas. Den här tabellen kan inte vara en extern tabell. Målet är namnet på målindex, statistik eller kolumn som statistikinformationen ska visas för.

## <a name="update-statistics"></a>Uppdatera statistik

En bästa praxis är att uppdatera statistik om datumkolumner varje dag när nya datum läggs till. Varje gång nya rader läses in i SQL-poolen läggs nya inläsningsdatum eller transaktionsdatum till. Dessa tillägg ändrar datafördelningen och gör statistiken inaktuella.

Statistik för en land-/regionkolumn i en kundtabell kanske aldrig behöver uppdateras eftersom värdefördelningen i allmänhet inte ändras. Om du antar att fördelningen är konstant mellan kunder kommer det inte att ändra datadistributionen om du lägger till nya rader i tabellvariationen.

Men om din SQL-pool bara innehåller ett land/en region och du tar in data från ett nytt land/en ny region, vilket resulterar i att data från flera länder/regioner lagras, måste du uppdatera statistik om kolumnen land/region.

Följande är rekommendationer uppdatera statistik:

|||
|-|-|
| **Frekvens av statistik uppdateringar**  | Konservativ: Dagligen </br> Efter inläsning eller omformning av data |
| **Samling** |  Mindre än 1 miljard rader, använd standardprovtagning (20 procent). </br> Med mer än 1 miljard rader, använd provtagning på två procent. |

En av de första frågorna att ställa när du felsöker en fråga **är: "Är statistiken uppdaterad?"**

Den här frågan är inte en som kan besvaras av dataåldern. Ett aktuellt statistikobjekt kan vara gammalt om det inte har skett någon väsentlig ändring av underliggande data.

> [!TIP]
> När antalet rader har ändrats avsevärt, eller om det finns en väsentlig förändring i fördelningen av värden för en *kolumn,* är det dags att uppdatera statistik.

Det finns ingen dynamisk hanteringsvy för att avgöra om data i tabellen har ändrats sedan den senaste gången statistiken uppdaterades. Att veta åldern på din statistik kan ge dig en del av bilden.

Du kan använda följande fråga för att avgöra när statistiken uppdaterades senast i varje tabell.

> [!NOTE]
> Om det sker en väsentlig förändring i fördelningen av värden för en kolumn bör du uppdatera statistik oavsett när de senast uppdaterades.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Datumkolumner** i en SQL-pool behöver till exempel ofta uppdateringar av statistik. Varje gång nya rader läses in i SQL-poolen läggs nya inläsningsdatum eller transaktionsdatum till. Dessa tillägg ändrar datafördelningen och gör statistiken inaktuella.

Omvänt kan det hända att statistik om en könskolumn i en kundtabell aldrig behöver uppdateras. Om du antar att fördelningen är konstant mellan kunder kommer det inte att ändra datadistributionen om du lägger till nya rader i tabellvariationen.

Om SQL-poolen bara innehåller ett kön och ett nytt krav resulterar i flera kön måste du uppdatera statistik om könskolumnen.

Mer information finns i allmän vägledning för [statistik](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="implementing-statistics-management"></a>Genomförande av statistikhantering

Det är ofta en bra idé att utöka datainläsningsprocessen för att säkerställa att statistiken uppdateras i slutet av belastningen.

Databelastningen är när tabeller oftast ändrar sin storlek och/eller sin fördelning av värden. Datainläsning är en logisk plats för att implementera vissa hanteringsprocesser.

Följande vägledande principer finns för att uppdatera din statistik under belastningsprocessen:

- Kontrollera att varje inläst tabell har minst ett statistikobjekt uppdaterat. Detta uppdaterar tabellstorleken (radantal och antal sidor) som en del av statistikuppdateringen.
- Fokusera på kolumner som deltar i JOIN-, GROUP BY-, ORDER BY- och DISTINCT-satser.
- Överväg att uppdatera kolumner med stigande nyckel, till exempel transaktionsdatum oftare, eftersom dessa värden inte kommer att inkluderas i statistik histogrammet.
- Överväg att uppdatera statiska distributionskolumner mindre ofta.
- Kom ihåg att varje statistikobjekt uppdateras i följd. Att bara `UPDATE STATISTICS <TABLE_NAME>` implementera är inte alltid idealiskt, särskilt inte för breda tabeller med många statistikobjekt.

Mer information finns i [Kardinalitetsuppskattning](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="examples-create-statistics"></a>Exempel: Skapa statistik

De här exemplen visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ du använder för varje kolumn beror på datas egenskaper och hur kolumnen ska användas i frågor.

### <a name="create-single-column-statistics-with-default-options"></a>Skapa statistik med en kolumn med standardalternativ

Om du vill skapa statistik i en kolumn anger du ett namn på statistikobjektet och namnet på kolumnen.

Den här syntaxen använder alla standardalternativ. Som standard tar SQL-pool prov **20 procent** av tabellen när den skapar statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Ett exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa statistik med en kolumn genom att granska varje rad

Standardprovtagningshastigheten på 20 procent är tillräcklig för de flesta situationer. Du kan dock justera samplingsfrekvensen.

Om du vill prova hela tabellen använder du den här syntaxen:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Ett exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa statistik med en kolumn genom att ange exempelstorleken

Du kan också ange exempelstorleken som en procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa statistik med en kolumn på endast några av raderna

Du kan också skapa statistik på en del av raderna i tabellen. Detta kallas filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en viss partition i en stor partitionerad tabell. Genom att skapa statistik om endast partitionsvärdena förbättras statistikens noggrannhet och förbättrar därför frågeprestanda.

I det här exemplet skapas statistik för ett värdeintervall. Värdena kan enkelt definieras för att matcha intervallet av värden i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> För att frågeoptimeraren ska kunna använda filtrerad statistik när den väljer den distribuerade frågeplanen måste frågan passa in i definitionen av statistikobjektet. Med föregående exempel måste frågans WHERE-sats ange kol1-värden mellan 2000101 och 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa statistik med en kolumn med alla alternativ

Du kan också kombinera alternativen tillsammans. I följande exempel skapas ett filtrerat statistikobjekt med en anpassad exempelstorlek:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Den fullständiga referensen finns i [SKAPA STATISTIK](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="create-multi-column-statistics"></a>Skapa statistik med flera kolumner

Om du vill skapa ett statistikobjekt med flera kolumner använder du föregående exempel, men anger fler kolumner.

> [!NOTE]
> Histogrammet, som används för att uppskatta antalet rader i frågeresultatet, är endast tillgängligt för den första kolumnen i statistikobjektdefinitionen.

I det här exemplet finns histogrammet på *produktkategorin\_*. Statistik över flera kolumner beräknas på *produktkategori\_* och *\_produkt sub_category:*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produktkategori\_* och *\_produktunderkategori\_* kan ett statistikobjekt med flera kolumner vara användbart om dessa kolumner används samtidigt.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Skapa statistik för alla kolumner i en tabell

Ett sätt att skapa statistik är att utfärda skapa statistikkommandon när du har skapat tabellen:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Använda en lagrad procedur för att skapa statistik för alla kolumner i en databas

SQL-poolen har ingen systemlagd procedur som motsvarar sp_create_stats i SQL Server. Den här lagrade proceduren skapar ett en kolumnstatistikobjekt i varje kolumn i databasen som inte redan har statistik.

I följande exempel kan du komma igång med databasdesignen. Känn dig fri att anpassa den till dina behov.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Om du vill skapa statistik för alla kolumner i tabellen med standardvärden kör du den lagrade proceduren.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Om du vill skapa statistik för alla kolumner i tabellen med hjälp av en fullscan anropar du den här proceduren.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Om du vill skapa exempelstatistik för alla kolumner i tabellen anger du 3 och exempelprocenten. Den här proceduren använder en samplingsfrekvens på 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Exempel: Uppdatera statistik

Om du vill uppdatera statistik kan du:

- Uppdatera ett statistikobjekt. Ange namnet på det statistikobjekt som du vill uppdatera.
- Uppdatera alla statistikobjekt i en tabell. Ange namnet på tabellen i stället för ett specifikt statistikobjekt.

### <a name="update-one-specific-statistics-object"></a>Uppdatera ett specifikt statistikobjekt

Använd följande syntax för att uppdatera ett visst statistikobjekt:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Ett exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Genom att uppdatera specifika statistikobjekt kan du minimera den tid och de resurser som krävs för att hantera statistik. Detta kräver en tanke att välja de bästa statistik objekt att uppdatera.

### <a name="update-all-statistics-on-a-table"></a>Uppdatera all statistik i en tabell

En enkel metod för att uppdatera alla statistikobjekt i en tabell är:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ett exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS-satsen är enkel att använda. Kom bara ihåg att den uppdaterar *all* statistik på bordet, och därför kan utföra mer arbete än vad som är nödvändigt. Om prestanda inte är ett problem är detta det enklaste och mest kompletta sättet att garantera att statistiken är uppdaterad.

> [!NOTE]
> När du uppdaterar all statistik i en tabell gör SQL-poolen en genomsökning för att prova tabellen för varje statistikobjekt. Om tabellen är stor och har många kolumner och många statistik kan det vara mer effektivt att uppdatera individuell statistik baserat på behov.

Ett genomförande av `UPDATE STATISTICS` ett förfarande finns i [Tillfälliga tabeller](sql-data-warehouse-tables-temporary.md). Implementeringsmetoden skiljer sig något `CREATE STATISTICS` från föregående förfarande, men resultatet är detsamma.

Den fullständiga syntaxen finns i [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="statistics-metadata"></a>Metadata för statistik

Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistikobjekt kan vara inaktuella genom att använda funktionen statistikdatum för att se när statistik senast skapades eller uppdaterades.

### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik

Dessa systemvyer ger information om statistik:

| Katalogvy | Beskrivning |
|:--- |:--- |
| [sys.kolumner](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje kolumn. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje objekt i databasen. |
| [sys.scheman](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje schema i databasen. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje statistikobjekt. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje kolumn i statistikobjektet. Länkar tillbaka till sys.columns. |
| [sys.tabeller](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje tabell (inkluderar externa tabeller). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje datatyp. |

### <a name="system-functions-for-statistics"></a>Systemfunktioner för statistik

Dessa systemfunktioner är användbara för att arbeta med statistik:

| Systemfunktion | Beskrivning |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum då statistikobjektet senast uppdaterades. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Sammanfattande nivå och detaljerad information om fördelningen av värden enligt statistikobjektet. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistikkolumner och funktioner i en vy

Den här vyn sammanför kolumner som relaterar till statistik och resultat från funktionen STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Dbcc-exempel SHOW_STATISTICS()

DBCC SHOW_STATISTICS() visar data som finns i ett statistikobjekt. Dessa data finns i tre delar:

- Huvud
- Densitet vektor
- Histogram

Rubrikmetadata om statistiken. Histogrammet visar värdefördelningen i den första nyckelkolumnen i statistikobjektet. Densitetsvektorn mäter korrelation mellan kolumner.

> [!NOTE]
> SQL-pool beräknar kardinalitetsuppskattningar med någon av data i statistikobjektet.

### <a name="show-header-density-and-histogram"></a>Visa sidhuvud, densitet och histogram

I det här enkla exemplet visas alla tre delarna i ett statistikobjekt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Ett exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS()

Om du bara är intresserad av att `WITH` visa specifika delar använder du satsen och anger vilka delar du vill se:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ett exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() skillnader

DBCC SHOW_STATISTICS() implementeras striktare i SQL-poolen jämfört med SQL Server:

- Odokumenterade funktioner stöds inte.
- Det går inte att använda Stats_stream.
- Det går inte att koppla resultat för specifika delmängder av statistikdata. Till exempel STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS kan inte ställas in för meddelandeundertryckning.
- Hakparenteser runt statistiknamn kan inte användas.
- Det går inte att använda kolumnnamn för att identifiera statistikobjekt.
- Anpassat fel 2767 stöds inte.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du förbättrar frågeprestanda ytterligare finns i [Övervaka din arbetsbelastning](sql-data-warehouse-manage-monitor.md)
