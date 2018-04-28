---
title: Skapa, uppdatera statistik - Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och exempel för att skapa och uppdatera frågan optimering statistik på tabellerna i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: a8d91714e6864ff0a9816f5ec518878334f6ba84
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="creating-updating-statistics-on-tables-in-azure-sql-data-warehouse"></a>Skapa, uppdatera statistik på tabellerna i Azure SQL Data Warehouse
Rekommendationer och exempel för att skapa och uppdatera frågan optimering statistik på tabellerna i Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Varför använda statistik?
Ju mer Azure SQL Data Warehouse medveten om dina data, desto snabbare den kan köra frågor mot den. Samla in statistik på dina data och läsa in informationen i SQL Data Warehouse är en av de viktigaste sakerna som du kan göra för att optimera dina frågor. Detta beror på att Frågeoptimeringen SQL Data Warehouse är en kostnad-baserade optimering. Den jämför kostnaden för olika frågeplaner och väljer plan med lägst kostnad, som i de flesta fall är den plan som kör den snabbaste. Till exempel om optimering uppskattar att datumet du filtrerar i frågan returnerar en rad, kan det välja ett annat schema än om den beräknar som det valda datumet returnerar 1 miljoner rader.

Processen för att skapa och uppdatera statistik för närvarande är en manuell process, men den är enkel att göra.  Du kommer snart att kunna skapa och uppdatera statistik på enskild kolumner och index automatiskt.  Du kan avsevärt automatisera hanteringen av statistik på dina data med hjälp av följande information. 

## <a name="scenarios"></a>Scenarier
Provade statistik skapas för varje kolumn är ett enkelt sätt att komma igång. Inaktuella statistik leda till något sämre prestanda. Uppdatera statistik på alla kolumner i takt med dina data växer kan dock använda minne. 

Här följer några rekommendationer för olika scenarier:
| **scenario** | Rekommendation |
|:--- |:--- |
| **Kom igång** | Uppdatera alla kolumner när du har migrerat till SQL Data Warehouse |
| **De viktigaste kolumn för statistik** | Hash-fördelningsnyckel |
| **Andra viktigaste kolumn för statistik** | Partitionsnyckeln |
| **Andra viktiga kolumner för statistik** | Datum, ofta ansluter till, gruppera efter, HAVING, och där |
| **Frekvens av statistik uppdateringar**  | Konservativ: varje dag <br></br> När du läser in eller Transformera data |
| **Sampling** |  Mindre än 1 miljard rader, använda provtagning standard (20 procent) <br></br> Med mer än 1 miljard rader är statistik på 2 procent-intervallet bra |

## <a name="updating-statistics"></a>Uppdatera statistik

Ett bra tips är att uppdatera statistik i datumkolumnerna varje dag som nya datum har lagts till. Varje gång nya rader läses in i datalagret, nya belastningen datum eller datum har lagts till. Dessa ändra fördelningen data och se statistik för gammal. Statistik om en land-kolumn i tabellen för en kund kan däremot aldrig behöver uppdateras eftersom fördelningen av värden i allmänhet inte ändra. Under förutsättning att distributionen är konstant mellan kunder, ska lägga till nya rader i tabellen variationen inte du ändra fördelningen data. Men om ditt data warehouse endast innehåller ett land och du hämta data från ett nytt land, måste vilket resulterar i data från flera länder lagras, du uppdatera statistik i kolumnen land.

En av de första frågorna när du felsöker en fråga är **”är statistik uppdaterade”?**

Den här frågan är inte en som kan betjänas av du åldern på data. Objektets aktuella statistik kanske gamla om det inte har ingen väsentlig ändring i underliggande data. Om antalet rader som har ändrats betydligt, eller en väsentlig förändring fördelningen av värden för en kolumn, *sedan* är det dags att uppdatera statistik.

Eftersom det finns ingen dynamisk hanteringsvy att avgöra om data i tabellen har ändrats sedan de senaste statistik har uppdaterats, kan vet du åldern på din statistik ge dig med en del av bilden.  Du kan använda följande fråga för att fastställa den senast din statistik har uppdaterats i varje tabell.  

> [!NOTE]
> Kom ihåg att om det finns en väsentlig förändring fördelningen av värden för en kolumn, bör du uppdatera statistik oavsett de uppdaterades senast.  
> 
> 

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

**Datum kolumner** i datalagret, till exempel vanligtvis måste frekventa uppdateringar av statistik. Varje gång nya rader läses in i datalagret, nya belastningen datum eller datum har lagts till. Dessa ändra fördelningen data och se statistik för gammal.  Däremot kanske statistik på kolumnen i tabellen för en kund kön aldrig behöver uppdateras. Under förutsättning att distributionen är konstant mellan kunder, ska lägga till nya rader i tabellen variationen inte du ändra fördelningen data. Men om ditt data warehouse innehåller endast en kön och en ny krav resulterar i flera könen, måste du uppdatera statistik i kolumnen kön.

Mer information finns i allmänna riktlinjer för [statistik](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementera hantering av statistik
Det är ofta en bra idé att utöka datainläsning processen för att säkerställa att uppdateras i slutet av belastningen. Inläsningen är när tabeller ändras oftast deras storlek och/eller distribution av värden. Därför är detta en logisk plats för att implementera vissa hanteringsprocesser.

Följande riktlinjerna tillhandahålls för att uppdatera statistiken under inläsningen:

* Kontrollera att varje inlästa tabell har minst ett statistik objekt uppdateras. Detta uppdaterar tabellen storlek (radantal och antal sidor) information som en del av uppdateringen statistik.
* Fokusera på kolumner som ingår i koppling, GROUP BY, ORDER BY och DISTINCT-satser.
* Överväg att uppdatera ”stigande nyckeln” kolumner som transaktionen datum oftare, eftersom dessa värden inte inkluderas i statistik histogram.
* Överväg att uppdatera statiska distributionskolumner mindre ofta.
* Kom ihåg att varje statistik objekt uppdateras i följd. Bara implementera `UPDATE STATISTICS <TABLE_NAME>` inte alltid är perfekt, särskilt för många tabeller med många statistik objekt.

Mer information finns i [kardinalitet beräkning av](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Exempel: Skapa statistik
De här exemplen visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen används i frågor.

### <a name="create-single-column-statistics-with-default-options"></a>Skapa statistik för en kolumn med standardalternativ
Om du vill skapa statistik på en kolumn, bara ange ett namn för objektet statistik och namnet på kolumnen.

Den här syntaxen använder alla standardalternativ. Som standard SQL Data Warehouse prover **20 procent** i tabellen när den skapar statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa enkolumns-statistik genom att undersöka varje rad
Standard samplingsfrekvensen 20 procent räcker för de flesta situationer. Du kan dock justera samplingsfrekvensen.

Om du vill använda fullständig tabellen använder du följande syntax:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa enkolumns-statistik genom att ange provtagning
Du kan också ange exempelstorleken procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa enkolumns-statistik på bara några rader
Du kan också skapa statistik på en del av raderna i tabellen. Detta kallas en filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en specifik partition för en stor partitionerad tabell. Genom att skapa statistik på partitionen värdena kommer riktighet statistik förbättra och därför förbättra frågeprestanda.

Det här exemplet skapar statistik på ett intervall med värden. Värdena kan enkelt definieras för att matcha intervallet för värden i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Frågan måste rymmas i definitionen av objektet statistik för frågeoptimeraren överväga att använda filtrerad statistik när de väljer distribuerade frågeplanen. Med hjälp av det tidigare exemplet, fråga där sats måste ange Kol1 värden mellan 2000101 och 20001231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa enkolumns-statistik med alla alternativ
Du kan också kombinera alternativen tillsammans. I följande exempel skapas en filtrerad statistik-objekt med en anpassad provtagning:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Fullständiga referenser finns [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Skapa flera kolumner statistik
Om du vill skapa ett objekt med flera kolumnstatistik att bara använda föregående exempel, men ange fler kolumner.

> [!NOTE]
> Histogram som används för att beräkna antalet rader i frågeresultatet, är endast tillgängligt för den första kolumnen visas i statistik objektdefinition.
> 
> 

I det här exemplet är histogrammet på *produkten\_kategori*. Statistik för Cross-kolumnen beräknas på *produkten\_kategori* och *produkten\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produkten\_kategori* och *produkten\_sub\_kategori*, ett flera kolumnstatistik-objekt kan vara användbart om dessa kolumnerna nås på samma gång.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Skapa statistik på alla kolumner i en tabell
Ett sätt att skapa statistik är att utfärda kommandon för CREATE STATISTICS när du har skapat tabellen:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Använda en lagrad procedur för att skapa statistik på alla kolumner i en databas
SQL Data Warehouse saknar en motsvarande sp_create_stats systemets lagrade procedur i SQL Server. Den här lagrade proceduren skapar en kolumn statistik-objekt på varje kolumn i databasen som inte redan har statistik.

I följande exempel hjälper dig att komma igång med din databasdesign. Du kan anpassa efter dina behov:

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

Bara anropa proceduren för att skapa statistik på alla kolumner i tabellen med standardinställningar.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Anropa den här proceduren för att skapa statistik på alla kolumner i tabellen med hjälp av en fullscan:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
Om du vill skapa provade statistik på alla kolumner i tabellen, anger du 3 och exempel procent.  Den här proceduren använder en 20 procent frekvens.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Prov att skapa statistik på alla kolumner 

## <a name="examples-update-statistics"></a>Exempel: Uppdatera statistik
Om du vill uppdatera statistik, kan du:

- Uppdatera ett objekt av statistik. Ange namnet på objektet statistik som du vill uppdatera.
- Uppdatera alla statistik objekt i en tabell. Ange namnet på tabellen i stället för en viss statistik-objektet.

### <a name="update-one-specific-statistics-object"></a>Uppdatera en specifik statistik objekt
Använd följande syntax för att uppdatera en specifik statistik objektet:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Du kan minimera den tid och resurser som krävs för att hantera statistik genom att uppdatera statistik för specifika objekt. Detta kräver vissa tror att välja de bästa statistik objekt att uppdatera.

### <a name="update-all-statistics-on-a-table"></a>Uppdatera all statistik för en tabell
Detta visar en enkel metod för att uppdatera statistik objekt i en tabell:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

Den här instruktionen är enkla att använda. Men kom ihåg att uppdateringarna *alla* statistik i tabellen, och därför kan utföra mer arbete än nödvändigt. Om prestanda inte är ett problem, är detta det enklaste och sättet att garantera att statistik är uppdaterade.

> [!NOTE]
> När du uppdaterar all statistik för en tabell, stöder SQL Data Warehouse en sökning att exempel tabellen för varje statistik-objekt. Om tabellen är stort och har många kolumner och många statistik, kan det vara mer effektivt att uppdatera enskilda statistik baserat på behov.
> 
> 

För en implementering av en `UPDATE STATISTICS` proceduren, se [temporära tabeller](sql-data-warehouse-tables-temporary.md). Metoden implementering är skiljer sig från den föregående `CREATE STATISTICS` procedur, men resultatet är samma.

Den fullständiga syntaxen finns [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statistik metadata
Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistik-objekt kan vara inaktuell med hjälp av funktionen stats datum när statistiken skapades eller uppdaterades senast.

### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik
Dessa systemvyer innehåller information om statistik:

| katalogvyn | Beskrivning |
|:--- |:--- |
| [sys.Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |En rad för varje kolumn. |
| [sys.Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |En rad för varje objekt i databasen. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |En rad för varje schema i databasen. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |En rad för varje objekt i statistik. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |En rad för varje kolumn i statistik-objektet. Länkar till sys.columns. |
| [sys.Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |En rad för varje tabell (inklusive externa tabeller). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |En rad för varje datatyp. |

### <a name="system-functions-for-statistics"></a>Systemfunktioner för statistik
Dessa systemfunktioner är användbara för att arbeta med statistik:

| Systemfunktionen | Beskrivning |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Datum statistik objektet senast uppdaterades. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Översikt över nivå och detaljerad information om distributionen av värden som tolkas av statistik-objektet. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistik kolumner och funktioner i en vy
Den här vyn visar kolumnerna som relaterar till statistik och fås funktionen STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
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

## <a name="dbcc-showstatistics-examples"></a>DBCC SHOW_STATISTICS() exempel
DBCC SHOW_STATISTICS() visar de data som lagras i ett statistik-objekt. Dessa data kommer in tre delar:

- Sidhuvud
- Densitet vector
- Histogram

Huvudet metadata om statistik. Histogrammet visar fördelningen av värden i den första nyckelkolumnen i objektet statistik. Densitet vector åtgärder mellan kolumnen korrelation. SQL Data Warehouse beräknar kardinalitet uppskattningar med data i statistik-objektet.

### <a name="show-header-density-and-histogram"></a>Visa sidhuvud, densitet och histogram
Det här enkla exemplet visar alla tre delar av ett objekt för statistik:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS()
Om du bara är intresserad av att visa vissa delar av `WITH` satsen och ange vilka delar som du vill visa:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() skillnader
DBCC SHOW_STATISTICS() implementeras striktare i SQL Data Warehouse jämfört med SQL Server:

- Odokumenterade funktioner stöds inte.
- Det går inte att använda Stats_stream.
- Det går inte att ansluta resultat för specifika delmängder av statistikdata. Till exempel (STAT_HEADER ansluta DENSITY_VECTOR).
- NO_INFOMSGS kan inte anges för Undertryckning av meddelandet.
- Hakparenteser runt statistik namn kan inte användas.
- Det går inte att använda kolumnnamn för att identifiera statistik objekt.
- Anpassade fel 2767 stöds inte.

## <a name="next-steps"></a>Nästa steg
För ytterligare förbättra frågeprestanda, se [övervaka din arbetsbelastning](sql-data-warehouse-manage-monitor.md)

