---
title: Skapa, uppdatera statistik
description: Rekommendationer och exempel för att skapa och uppdatera frågeoptimeringsstatistik i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 798fec4dacb33a9f16de319062baf12adaffdbd0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428750"
---
# <a name="statistics-in-synapse-sql"></a>Statistik i Synapse SQL

I den här artikeln finns rekommendationer och exempel för att skapa och uppdatera frågeoptimeringsstatistik med hjälp av Synapse SQL-resurser: SQL-pool och SQL on-demand (förhandsversion).

## <a name="statistics-in-sql-pool"></a>Statistik i SQL-pool

### <a name="why-use-statistics"></a>Varför använda statistik

Ju mer SQL-poolresursen vet om dina data, desto snabbare kan den köra frågor. När du har läst in data i SQL-poolen är insamling av statistik om dina data en av de viktigaste sakerna du kan göra för frågeoptimering.  

SQL pool fråga optimizer är en kostnadsbaserad optimizer. Den jämför kostnaden för olika frågeplaner och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den plan som ska köras snabbast.

Om till exempel optimeraren uppskattar att datumet då frågan filtreras på returnerar en rad väljer den en plan. Om den uppskattar att det valda datumet returnerar 1 miljon rader returneras en annan plan.

### <a name="automatic-creation-of-statistics"></a>Automatiskt skapande av statistik

SQL-poolen analyserar inkommande användarfrågor för saknad statistik när databasalternativet AUTO_CREATE_STATISTICS är inställt på `ON`.  Om statistik saknas skapar frågeoptimeraren statistik för enskilda kolumner i frågepredikatet eller kopplingsvillkoret. Den här funktionen används för att förbättra kardinalitetsuppskattningar för frågeplanen.

> [!IMPORTANT]
> Automatisk skapande av statistik är för närvarande aktiverat som standard.

Du kan kontrollera om ditt informationslager har AUTO_CREATE_STATISTICS konfigurerat genom att köra följande kommando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Om ditt informationslager inte har AUTO_CREATE_STATISTICS aktiverat rekommenderar vi att du aktiverar den här egenskapen genom att köra följande kommando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Dessa uttalanden utlöser automatiskt skapandet av statistik:

- VÄLJ
- INFOGA-MARKERA
- CTAS
- UPDATE
- DELETE
- FÖRKLARA när man innehåller en koppling eller förekomsten av ett predikat upptäcks

> [!NOTE]
> Det automatiska skapandet av statistik genereras inte i tillfälliga eller externa tabeller.

Automatisk skapande av statistik görs synkront. Därför kan du ådra dig något försämrade frågeprestanda om kolumnerna saknar statistik. Tiden för att skapa statistik för en enskild kolumn beror på tabellens storlek.

För att undvika mätbara prestandaförsämring bör du se till att statistik har skapats först genom att köra benchmark-arbetsbelastningen innan du profilerar systemet.

> [!NOTE]
> Skapandet av statistik loggas i [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) under en annan användarkontext.

När automatisk statistik skapas tar de formen: _WA_Sys_<8-siffriga kolumn-ID i Hex>_<8-siffrigt tabell-ID i Hex>. Du kan visa redan skapad statistik genom att köra kommandot [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Den table_name är namnet på den tabell som innehåller den statistik som ska visas, vilket inte kan vara en extern tabell. Målet är namnet på målindex, statistik eller kolumn som statistikinformationen ska visas för.

### <a name="update-statistics"></a>Uppdatera statistik

En bästa praxis är att uppdatera statistik om datumkolumner varje dag när nya datum läggs till. Varje gång nya rader läses in i informationslagret läggs nya inläsningsdatum eller transaktionsdatum till. Dessa tillägg ändrar datafördelningen och gör statistiken inaktuella.

Statistik för en lands- eller regionkolumn i en kundtabell kanske aldrig behöver uppdateras eftersom värdefördelningen vanligtvis inte ändras. Om du antar att fördelningen är konstant mellan kunder kommer det inte att ändra datadistributionen om du lägger till nya rader i tabellvariationen.

Men när ditt informationslager bara innehåller ett land eller en region och du tar in data från ett nytt land eller en ny region, måste du uppdatera statistik om land- eller regionkolumnen.

Följande är rekommendationer uppdatera statistik:

|||
|-|-|
| **Frekvens av statistik uppdateringar**  | Konservativ: Dagligen </br> Efter inläsning eller omformning av data |
| **Samling** |  Mindre än 1 miljard rader, använd standardprovtagning (20 procent). </br> Med mer än 1 miljard rader, använd provtagning på två procent. |

### <a name="determine-last-statistics-update"></a>Ta reda på den senaste uppdateringen av statistik

En av de första frågorna att ställa när du felsöker en fråga **är: "Är statistiken uppdaterad?"**

Den här frågan är inte en som kan besvaras av dataåldern. Ett aktuellt statistikobjekt kan vara gammalt om det inte har skett någon väsentlig ändring av underliggande data. När antalet rader har ändrats avsevärt, eller om en väsentlig förändring i *then* fördelningen av värden för en kolumn inträffar, är det dags att uppdatera statistiken.

Det finns ingen dynamisk hanteringsvy tillgänglig för att avgöra om data i tabellen har ändrats sedan den senaste gången statistiken uppdaterades. Att veta åldern på din statistik kan ge dig en del av bilden. Du kan använda följande fråga för att avgöra när statistiken uppdaterades senast i varje tabell.

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

**Datumkolumner** i ett informationslager behöver till exempel ofta uppdateringar av statistik. Varje gång nya rader läses in i informationslagret läggs nya inläsningsdatum eller transaktionsdatum till. Dessa tillägg ändrar datafördelningen och gör statistiken inaktuella.

Statistik om en könskolumn i en kundtabell kanske aldrig behöver uppdateras. Om du antar att fördelningen är konstant mellan kunder kommer det inte att ändra datadistributionen om du lägger till nya rader i tabellvariationen.

Men om ditt informationslager bara innehåller ett kön och ett nytt krav resulterar i flera kön måste du uppdatera statistik om könskolumnen. Mer information finns i artikeln [Statistik.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Genomförande av statistikhantering

Det är ofta en bra idé att utöka datainläsningsprocessen för att säkerställa att statistiken uppdateras i slutet av belastningen. Datainläsningen är när tabeller oftast ändrar storlek, fördelning av värden eller båda. Därför är inläsningsprocessen en logisk plats för att implementera vissa hanteringsprocesser.

Följande vägledande principer finns för att uppdatera din statistik under belastningsprocessen:

- Kontrollera att varje inläst tabell har minst ett statistikobjekt uppdaterat. Den här processen uppdaterar tabellstorleken (radantal och antal sidor) som en del av statistikuppdateringen.
- Fokusera på kolumner som deltar i JOIN-, GROUP BY-, ORDER BY- och DISTINCT-satser.
- Överväg att uppdatera kolumner med stigande nyckel, till exempel transaktionsdatum oftare, eftersom dessa värden inte tas med i statistikbladet.
- Överväg att uppdatera statiska distributionskolumner mindre ofta.
- Kom ihåg att varje statistikobjekt uppdateras i följd. Att bara `UPDATE STATISTICS <TABLE_NAME>` implementera är inte alltid idealiskt, särskilt inte för breda tabeller med många statistikobjekt.

Mer information finns i [Kardinalitetsuppskattning](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Exempel: Skapa statistik

De här exemplen visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ du använder för varje kolumn beror på datas egenskaper och hur kolumnen ska användas i frågor.

#### <a name="create-single-column-statistics-with-default-options"></a>Skapa statistik med en kolumn med standardalternativ

Om du vill skapa statistik i en kolumn anger du ett namn på statistikobjektet och namnet på kolumnen.
Den här syntaxen använder alla standardalternativ. Som standard tar SQL-pool prov **20 procent** av tabellen när den skapar statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Ett exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa statistik med en kolumn genom att granska varje rad

Standardprovtagningshastigheten på 20 procent är tillräcklig för de flesta situationer. Du kan dock justera samplingsfrekvensen. Om du vill prova hela tabellen använder du den här syntaxen:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Ett exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa statistik med en kolumn genom att ange exempelstorleken

Ett annat alternativ som du har är att ange exempelstorleken som en procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa statistik med en kolumn på endast några av raderna

Du kan också skapa statistik på en del av raderna i tabellen, som kallas filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en viss partition i en stor partitionerad tabell. Genom att skapa statistik om endast partitionsvärdena förbättras statistikens noggrannhet. Du kommer också att uppleva en förbättring av frågeprestanda.

I det här exemplet skapas statistik för ett värdeintervall. Värdena kan enkelt definieras för att matcha intervallet av värden i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> För att frågeoptimeraren ska kunna använda filtrerad statistik när den väljer den distribuerade frågeplanen måste frågan passa in i definitionen av statistikobjektet. Med föregående exempel måste frågans WHERE-sats ange kol1-värden mellan 2000101 och 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa statistik med en kolumn med alla alternativ

Du kan också kombinera alternativen tillsammans. I följande exempel skapas ett filtrerat statistikobjekt med en anpassad exempelstorlek:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Den fullständiga referensen finns i [SKAPA STATISTIK](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="create-multi-column-statistics"></a>Skapa statistik med flera kolumner

Om du vill skapa ett statistikobjekt med flera kolumner använder du föregående exempel, men anger fler kolumner.

> [!NOTE]
> Histogrammet, som används för att uppskatta antalet rader i frågeresultatet, är endast tillgängligt för den första kolumnen i statistikobjektdefinitionen.

I det här exemplet finns histogrammet på *produktkategorin\_*. Statistik över flera kolumner beräknas på *produktkategori\_* och *\_produkt sub_category:*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produktkategori\_* och *\_produktunderkategori\_* kan ett statistikobjekt med flera kolumner vara användbart om dessa kolumner används samtidigt.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Skapa statistik för alla kolumner i en tabell

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Använda en lagrad procedur för att skapa statistik för alla kolumner i en databas

SQL-poolen har inte en systemlagd procedur som motsvarar sp_create_stats i SQL Server. Den här lagrade proceduren skapar ett en kolumnstatistikobjekt i varje kolumn i databasen som inte redan har statistik.
I följande exempel kan du komma igång med databasdesignen. Anpassa den gärna efter dina behov:

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

Om du vill skapa statistik för alla kolumner i tabellen med hjälp av en fullscan anropar du den här proceduren:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Om du vill skapa exempelstatistik för alla kolumner i tabellen anger du 3 och exempelprocenten. Proceduren nedan använder en samplingsfrekvens på 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Exempel: Uppdatera statistik

Om du vill uppdatera statistik kan du:

- Uppdatera ett statistikobjekt. Ange namnet på det statistikobjekt som du vill uppdatera.
- Uppdatera alla statistikobjekt i en tabell. Ange namnet på tabellen i stället för ett specifikt statistikobjekt.

#### <a name="update-one-specific-statistics-object"></a>Uppdatera ett specifikt statistikobjekt

Använd följande syntax för att uppdatera ett visst statistikobjekt:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Ett exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Genom att uppdatera specifika statistikobjekt kan du minimera den tid och de resurser som krävs för att hantera statistik. Den här åtgärden kräver en tanke för att välja de bästa statistikobjekten som ska uppdateras.

#### <a name="update-all-statistics-on-a-table"></a>Uppdatera all statistik i en tabell

En enkel metod för att uppdatera alla statistikobjekt i en tabell är:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ett exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS-satsen är enkel att använda. Kom bara ihåg att det uppdaterar *all* statistik på bordet, vilket föranleder mer arbete än vad som är nödvändigt. Om prestanda inte är ett problem är den här metoden det enklaste och mest kompletta sättet att garantera att statistiken är uppdaterad.

> [!NOTE]
> När du uppdaterar all statistik i en tabell gör SQL-poolen en genomsökning för att prova tabellen för varje statistikobjekt. Om tabellen är stor och har många kolumner och många statistik kan det vara mer effektivt att uppdatera individuell statistik baserat på behov.

En implementering av `UPDATE STATISTICS` en procedur finns i [Tillfälliga tabeller](develop-tables-temporary.md). Implementeringsmetoden skiljer sig något `CREATE STATISTICS` från föregående förfarande, men resultatet är detsamma.
För hela syntaxen finns i [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="statistics-metadata"></a>Metadata för statistik

Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistikobjekt kan vara inaktuella med hjälp av funktionen STATS_DATE(). STATS_DATE() kan du se när statistik senast skapades eller uppdaterades.

#### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik

Dessa systemvyer ger information om statistik:

| Katalogvy | Beskrivning |
|:--- |:--- |
| [sys.kolumner](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje kolumn. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje objekt i databasen. |
| [sys.scheman](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje schema i databasen. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje statistikobjekt. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje kolumn i statistikobjektet. Länkar tillbaka till sys.columns. |
| [sys.tabeller](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje tabell (inkluderar externa tabeller). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |En rad för varje datatyp. |

#### <a name="system-functions-for-statistics"></a>Systemfunktioner för statistik

Dessa systemfunktioner är användbara för att arbeta med statistik:

| Systemfunktion | Beskrivning |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum då statistikobjektet senast uppdaterades. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Sammanfattande nivå och detaljerad information om fördelningen av värden enligt statistikobjektet. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistikkolumner och funktioner i en vy

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

### <a name="dbcc-show_statistics-examples"></a>Dbcc-exempel SHOW_STATISTICS()

DBCC SHOW_STATISTICS() visar data som finns i ett statistikobjekt. Dessa data finns i tre delar:

- Huvud
- Densitet vektor
- Histogram

Rubriken är metadata om statistiken. Histogrammet visar värdefördelningen i den första nyckelkolumnen i statistikobjektet. Densitetsvektorn mäter korrelation mellan kolumner. SQL-pool beräknar kardinalitetsuppskattningar med någon av data i statistikobjektet.

#### <a name="show-header-density-and-histogram"></a>Visa sidhuvud, densitet och histogram

I det här enkla exemplet visas alla tre delarna i ett statistikobjekt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Ett exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS()

Om du bara är intresserad av att `WITH` visa specifika delar använder du satsen och anger vilka delar du vill se:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ett exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() skillnader

DBCC SHOW_STATISTICS() implementeras striktare i SQL-poolen jämfört med SQL Server:

- Odokumenterade funktioner stöds inte.
- Kan inte använda Stats_stream.
- Det går inte att koppla resultaten för specifika delmängder av statistikdata. Till exempel STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS kan inte ställas in för meddelandedämpning.
- Hakparenteser runt statistiknamn kan inte användas.
- Det går inte att använda kolumnnamn för att identifiera statistikobjekt.
- Anpassat fel 2767 stöds inte.

### <a name="next-steps"></a>Nästa steg

Mer information om hur du förbättrar frågeprestanda ytterligare finns i [Övervaka din arbetsbelastning](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>Statistik i SQL på begäran (förhandsgranskning)

Statistik skapas per viss kolumn för viss datauppsättning (lagringssökväg).

### <a name="why-use-statistics"></a>Varför använda statistik

Ju mer SQL on-demand (preview) vet om dina data, desto snabbare kan köra frågor mot den. Att samla in statistik om dina data är en av de viktigaste sakerna du kan göra för att optimera dina frågor. SQL on-demand-frågeoptimeraren är en kostnadsbaserad optimerare. Den jämför kostnaden för olika frågeplaner och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den plan som ska köras snabbast. Om till exempel optimeraren uppskattar att datumet då frågan filtreras på returnerar en rad väljer den en plan. Om den uppskattar att det valda datumet returnerar 1 miljon rader returneras en annan plan.

### <a name="automatic-creation-of-statistics"></a>Automatiskt skapande av statistik

SQL on-demand analyserar inkommande användarfrågor för saknad statistik. Om statistik saknas skapar frågeoptimeraren statistik för enskilda kolumner i frågepredikatet eller kopplingsvillkoret för att förbättra kardinalitetsuppskattningarna för frågeplanen.

SELECT-satsen utlöser automatiskt skapande av statistik.

> [!NOTE]
> Automatisk skapande av statistik är aktiverat för parkettfiler. För CSV-filer måste du skapa statistik manuellt tills automatisk skapande av CSV-filer statistik stöds.

Automatisk skapande av statistik görs synkront så att du kan ådra dig något försämrade frågeprestanda om kolumnerna saknar statistik. Tiden för att skapa statistik för en enskild kolumn beror på storleken på de filer som är avsedda.

### <a name="manual-creation-of-statistics"></a>Manuellt skapande av statistik

Med SQL on-demand kan du skapa statistik manuellt. För CSV-filer måste du skapa statistik manuellt eftersom automatisk generering av statistik inte är aktiverat för CSV-filer. Se exemplen nedan för instruktioner om hur du skapar statistik manuellt.

### <a name="updating-statistics"></a>Uppdatera statistik

Ändringar av data i filer, ta bort och lägga till filer resulterar i ändringar av datadistribution och gör statistik inaktuell. I så fall måste statistiken uppdateras.

SQL on-demand återskapar automatiskt statistik om data ändras avsevärt. Varje gång statistik skapas automatiskt sparas också datauppsättningens aktuella tillstånd: filsökvägar, storlekar, senaste ändringsdatum.

När statistiken är inaktuell skapas nya. Algoritmen går igenom data och jämför den med datauppsättningens aktuella tillstånd. Om storleken på ändringarna är större än det specifika tröskelvärdet tas gammal statistik bort och återskapas över den nya datauppsättningen.

Manuell statistik deklareras aldrig inaktuella.

> [!NOTE]
> Automatisk rekreation av statistik är påslagen för parkett filer. För CSV-filer måste du släppa och skapa statistik manuellt tills automatisk skapande av CSV-filer statistik stöds. Läs exemplen nedan om hur du släpper och skapar statistik.

En av de första frågorna att ställa när du felsöker en fråga **är: "Är statistiken uppdaterad?"**

När antalet rader har ändrats avsevärt, eller om det finns en väsentlig förändring i fördelningen av värden för en *kolumn,* är det dags att uppdatera statistik.

> [!NOTE]
> Om det sker en väsentlig förändring i fördelningen av värden för en kolumn bör du uppdatera statistik oavsett när de senast uppdaterades.

### <a name="implementing-statistics-management"></a>Genomförande av statistikhantering

Du kanske vill utöka datapipelinen så att statistiken uppdateras när data ändras avsevärt genom tillägg, radering eller ändring av filer.

Följande vägledande principer finns för att uppdatera din statistik:

- Kontrollera att datauppsättningen har minst ett statistikobjekt uppdaterat. Den här uppdaterar storleksinformation (antal rader och antal sidor) som en del av statistikuppdateringen.
- Fokusera på kolumner som deltar i JOIN-, GROUP BY-, ORDER BY- och DISTINCT-satser.
- Uppdatera kolumner med stigande nyckel, till exempel transaktionsdatum oftare eftersom dessa värden inte inkluderas i statistikbladet.
- Uppdatera statiska distributionskolumner mindre ofta.

Mer information finns i [Kardinalitetsuppskattning](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Exempel: Skapa statistik för kolumn i OPENROWSET-sökvägen

Följande exempel visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ du använder för varje kolumn beror på datas egenskaper och hur kolumnen ska användas i frågor.

> [!NOTE]
> Du kan bara skapa statistik med en kolumn just nu.

Följande lagrade procedur används för att skapa statistik:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Argument: @stmt [ = ] N'statement_text' - Anger en Transact-SQL-sats som returnerar kolumnvärden som ska användas för statistik. Du kan använda TABLESAMPLE för att ange exempel på data som ska användas. Om TABLESAMPLE inte har angetts används FULLSCAN.

```sql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV-sampling fungerar inte just nu, endast FULLSCAN stöds för CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa statistik med en kolumn genom att granska varje rad

Om du vill skapa statistik i en kolumn anger du en fråga som returnerar den kolumn som du behöver statistik för.

Om du inte anger något annat använder SQL on-demand 100 % av data som tillhandahålls i datauppsättningen när den skapar statistik.

Om du till exempel vill skapa statistik med standardalternativ (FULLSCAN) för en årskolumn i datauppsättningen baserat på filen population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa statistik med en kolumn genom att ange exempelstorleken

Du kan ange exempelstorleken som en procent:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Exempel: Uppdatera statistik

Om du vill uppdatera statistik måste du släppa och skapa statistik. Följande lagrade procedur används för att släppa statistik:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Argument: @stmt [ = ] N'statement_text' - Anger samma Transact-SQL-uttryck som användes när statistiken skapades.

Om du vill uppdatera statistiken för årskolumnen i datauppsättningen, som baseras på filen population.csv, måste du släppa och skapa statistik:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Exempel: Skapa statistik för extern tabellkolumn

Följande exempel visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ du använder för varje kolumn beror på datas egenskaper och hur kolumnen ska användas i frågor.

> [!NOTE]
> Du kan bara skapa statistik med en kolumn just nu.

Om du vill skapa statistik i en kolumn anger du ett namn på statistikobjektet och namnet på kolumnen.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argument: external_table Anger extern tabell om att statistik ska skapas.

FULLSCAN Beräkna statistik genom att skanna alla rader. FULLSCAN och SAMPLE 100 PROCENT har samma resultat. FULLSCAN kan inte användas med sample-alternativet.

EXEMPELNUMMER PROCENT Anger den ungefärliga procentandelen eller antalet rader i tabellen eller indexerad vy som frågeoptimeraren ska använda när den skapar statistik. Antalet kan vara från 0 till 100.

PROV kan inte användas med alternativet FULLSCAN.

> [!NOTE]
> CSV-sampling fungerar inte just nu, endast FULLSCAN stöds för CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa statistik med en kolumn genom att granska varje rad

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa statistik med en kolumn genom att ange exempelstorleken

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState on census_external_table (STATENAME) WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Exempel: Uppdatera statistik

Om du vill uppdatera statistik måste du släppa och skapa statistik. Släpp statistik först:

```sql
DROP STATISTICS census_external_table.sState
```

Och skapa statistik:

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Nästa steg

Ytterligare förbättringar av frågeprestanda finns i [Metodtips för SQL-pool](best-practices-sql-pool.md#maintain-statistics).
