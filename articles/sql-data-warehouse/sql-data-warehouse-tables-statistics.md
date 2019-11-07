---
title: Skapa, uppdatera statistik
description: Rekommendationer och exempel för att skapa och uppdatera statistik för att optimera frågor i tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c995358fc0135a1f9b504b57b23ecb3f6b41d6da
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692406"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Tabell statistik i Azure SQL Data Warehouse

Rekommendationer och exempel för att skapa och uppdatera statistik för att optimera frågor i tabeller i Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Varför använda statistik

Ju mer Azure SQL Data Warehouse vet om dina data, desto snabbare kan den köra frågor mot den. När du har läst in data i SQL Data Warehouse, är insamling av statistik för dina data ett av de viktigaste sakerna du kan göra för att optimera dina frågor. Frågeoptimeraren i SQL Data Warehouse är en kostnadsbaserad optimerare. Den Jämför kostnaden för olika fråge planer och väljer sedan planen med den lägsta kostnaden. I de flesta fall väljer den den plan som ska köras snabbast. Om optimeringen t. ex. beräknar att det datum då frågan filtreras på kommer att returnera en rad kommer den att välja en plan. Om den beräknar att det valda datumet returnerar 1 000 000 rader, returneras ett annat schema.

## <a name="automatic-creation-of-statistic"></a>Automatisk generering av statistik

När AUTO_CREATE_STATISTICS-alternativet för databas är på analyserar SQL Data Warehouse inkommande användar frågor för saknad statistik. Om statistik saknas skapar Query Optimering statistik för enskilda kolumner i frågeuttrycket eller kopplings villkor för att förbättra kardinalitet uppskattningar för frågeplan. Automatisk generering av statistik är för närvarande aktiverat som standard.

Du kan kontrol lera om ditt informations lager har AUTO_CREATE_STATISTICS konfigurerat genom att köra följande kommando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Om data lagret inte har AUTO_CREATE_STATISTICS konfigurerat rekommenderar vi att du aktiverar den här egenskapen genom att köra följande kommando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Dessa uttryck utlöser automatisk skapande av statistik:

- VÄLJ
- INFOGA-VÄLJ
- CTAS
- UPDATE
- DELETE
- FÖRKLARA när den innehåller en koppling eller förekomsten av ett predikat identifieras

> [!NOTE]
> Automatisk generering av statistik skapas inte för temporära eller externa tabeller.

Automatisk generering av statistik görs synkront så att du kan få en försämrad frågeresultat om kolumnerna saknar statistik. Hur lång tid det tar att skapa statistik för en enskild kolumn beror på tabellens storlek. För att undvika mätbar prestanda försämring, särskilt vid prestandatest, bör du se till att statistik har skapats först genom att köra benchmark-arbetsbelastningen innan du profilerar systemet.

> [!NOTE]
> Statistik skapandet loggas i [sys. DM-_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) under en annan användar kontext.

När automatisk statistik skapas, kommer de att ha formatet: _WA_Sys_< 8 siffror kolumn-ID i hex > _ < 8 siffer tabell-ID i hex >. Du kan visa statistik som redan har skapats genom att köra [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) -kommandot:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name är namnet på den tabell som innehåller den statistik som ska visas. Detta kan inte vara en extern tabell. Målet är namnet på mål indexet, statistiken eller kolumnen som statistik information ska visas för.

## <a name="updating-statistics"></a>Uppdaterar statistik

Ett bra tips är att uppdatera statistik för datum kolumner varje dag då nya datum läggs till. Varje gången nya rader läses in i data lagret läggs nya inläsnings datum eller transaktions datum till. Dessa ändrar data distributionen och gör statistiken inaktuell. Däremot kan statistik i en land/region-kolumn i en kund tabell aldrig behöva uppdateras, eftersom fördelning av värden vanligt vis inte ändras. Förutsatt att fördelningen är konstant mellan kunderna, kommer nya rader i tabell variationen inte att ändra data distributionen. Men om informations lagret bara innehåller ett land/region och du hämtar data från ett nytt land/en annan region, vilket resulterar i att data från flera länder/regioner lagras, måste du uppdatera statistik i kolumnen land/region.

Följande är rekommendationer om uppdaterings statistik:

|||
|-|-|
| **Frekvens för statistik uppdateringar**  | Försiktigt: varje dag </br> När du har läst in eller omvandlat dina data |
| **Sampling** |  Färre än 1 000 000 000 rader, Använd standard sampling (20 procent). </br> Med fler än 1 000 000 000 rader använder du samplingen av två procent. |

En av de första frågorna för att fråga när du felsöker en fråga är **"är statistiken uppdaterad?"**

Den här frågan är inte en som kan besvaras av data åldern. Ett uppdaterat statistik objekt kan vara gammalt om ingen material ändring har gjorts i underliggande data. När antalet rader har ändrats huvudsakligen, eller om det finns en väsentlig ändring i fördelning av värden för en kolumn, *är det dags* att uppdatera statistik.

Det finns ingen dynamisk hanterings vy för att avgöra om data i tabellen har ändrats sedan den senaste tids statistiken uppdaterades. Att veta ålder på din statistik kan ge dig en del av bilden. Du kan använda följande fråga för att avgöra när statistiken senast uppdaterades i varje tabell.

> [!NOTE]
> Om det finns en väsentlig förändring i fördelningen av värden för en kolumn bör du uppdatera statistiken, oavsett när de uppdaterades senast.

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

**Datum kolumner** i ett data lager, till exempel vanligt vis behöver ofta förekommande statistik uppdateringar. Varje gången nya rader läses in i data lagret läggs nya inläsnings datum eller transaktions datum till. Dessa ändrar data distributionen och gör statistiken inaktuell. Det kan hända att statistik i en köns kolumn i en kund tabell aldrig behöver uppdateras. Förutsatt att fördelningen är konstant mellan kunderna, kommer nya rader i tabell variationen inte att ändra data distributionen. Men om ditt informations lager bara innehåller en kön och ett nytt krav resulterar i flera kön, måste du uppdatera statistik i kön kön.

Mer information finns i allmän vägledning för [statistik](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementera statistik hantering

Det är ofta en bra idé att utöka data inläsnings processen så att statistiken uppdateras i slutet av belastningen. Data inläsningen är när tabeller ofta ändrar storlek och/eller deras fördelning av värden. Detta är därför en logisk plats för att implementera vissa hanterings processer.

Följande GUID-principer används för att uppdatera din statistik under inläsnings processen:

* Se till att varje inläst tabell har minst ett uppdaterat statistik objekt. Detta uppdaterar informationen i tabell storlek (antal rader och sid antal) som en del av statistiken uppdatera.
* Fokusera på kolumner som ingår i JOIN-, GROUP BY-, ORDER BY-och DISTINCT-satser.
* Överväg att uppdatera "ascending Key"-kolumner som transaktions datum oftare, eftersom dessa värden inte kommer att ingå i statistik histogrammet.
* Överväg att uppdatera statiska distributions kolumner mindre ofta.
* Kom ihåg att varje statistik objekt uppdateras i följd. Att bara implementera `UPDATE STATISTICS <TABLE_NAME>` är inte alltid idealiskt, särskilt för breda tabeller med massor av statistik objekt.

Mer information finns i [beräkning av kardinalitet](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Exempel: skapa statistik

I de här exemplen visas hur du använder olika alternativ för att skapa statistik. De alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen kommer att användas i frågor.

### <a name="create-single-column-statistics-with-default-options"></a>Skapa en statistik med en kolumn med standard alternativ

Om du vill skapa statistik för en kolumn anger du bara ett namn för objektet statistik och namnet på kolumnen.

I den här syntaxen används alla standard alternativ. Som standard SQL Data Warehouse exempel **20 procent** av tabellen när statistiken skapas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Till exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa en statistik med en kolumn genom att undersöka varje rad

Standard samplings frekvensen på 20 procent räcker för de flesta situationer. Du kan dock justera samplings frekvensen.

Använd följande syntax för att sampla den fullständiga tabellen:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Till exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa en statistik med en kolumn genom att ange prov storleken

Du kan också ange exempel storleken i procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa statistik med en kolumn på bara några av raderna

Du kan också skapa statistik på en del av raderna i tabellen. Detta kallas för en filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en speciell partition i en stor partitionerad tabell. Genom att skapa statistik enbart på partitionsalternativ förbättras noggrannheten i statistiken, och därför förbättras frågans prestanda.

I det här exemplet skapas statistik för ett värde intervall. Värdena kan enkelt definieras för att matcha värde intervallet i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> För att fråga optimeringen ska överväga att använda filtrerad statistik när den väljer den distribuerade frågan måste frågan passa inuti definitionen av statistik objekt. I föregående exempel måste frågans WHERE-sats ange col1-värden mellan 2000101 och 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa statistik med en kolumn med alla alternativ

Du kan också kombinera alternativen tillsammans. I följande exempel skapas ett filtrerat statistik objekt med en anpassad exempel storlek:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Fullständig referens finns i [skapa statistik](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Skapa statistik för flera kolumner

Om du vill skapa ett statistik objekt med flera kolumner, använder du bara föregående exempel, men anger fler kolumner.

> [!NOTE]
> Histogrammet, som används för att uppskatta antalet rader i frågeresultatet, är bara tillgängligt för den första kolumnen som anges i statistik objekt definitionen.

I det här exemplet är histogrammet i *kategorin produkt\_* . Statistik över kolumner beräknas för *produkt\_kategori* och *produkt\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produkt\_kategori* och *produkt\_under\_kategori*, kan ett statistik objekt med flera kolumner vara användbart om dessa kolumner används samtidigt.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Skapa statistik för alla kolumner i en tabell

Ett sätt att skapa statistik är att utfärda kommandon för att skapa statistik när tabellen har skapats:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Använd en lagrad procedur för att skapa statistik för alla kolumner i en databas

SQL Data Warehouse har ingen lagrad system procedur som motsvarar sp_create_stats i SQL Server. Den här lagrade proceduren skapar ett enda kolumn statistik objekt på varje kolumn i databasen som inte redan har statistik.

I följande exempel får du hjälp att komma igång med databas designen. Det är kostnads fritt att anpassa den efter dina behov:

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

Om du vill skapa statistik för alla kolumner i tabellen med hjälp av standardvärdena kör du den lagrade proceduren.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Anropa den här proceduren för att skapa statistik för alla kolumner i tabellen med en fullscan:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Om du vill skapa exempel statistik för alla kolumner i tabellen anger du 3 och exemplet procent. I den här proceduren används 20 procents samplings frekvens.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Så här skapar du exempel statistik för alla kolumner

## <a name="examples-update-statistics"></a>Exempel: uppdatera statistik

Om du vill uppdatera statistiken kan du:

- Uppdatera ett statistik objekt. Ange namnet på det statistik objekt som du vill uppdatera.
- Uppdatera alla statistik objekt i en tabell. Ange namnet på tabellen i stället för ett enskilt statistik objekt.

### <a name="update-one-specific-statistics-object"></a>Uppdatera ett enskilt statistik objekt

Använd följande syntax för att uppdatera ett enskilt statistik objekt:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Till exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Genom att uppdatera vissa statistik objekt kan du minimera den tid och de resurser som krävs för att hantera statistik. Detta kräver vissa tanke på att välja de bästa statistik objekt som ska uppdateras.

### <a name="update-all-statistics-on-a-table"></a>Uppdatera all statistik för en tabell

En enkel metod för att uppdatera alla statistik objekt i en tabell är:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Till exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS-instruktionen är enkel att använda. Kom bara ihåg att den uppdaterar *all* statistik i tabellen och kan därför utföra mer arbete än vad som behövs. Om prestanda inte är ett problem är det här det enklaste och mest kompletta sättet att garantera att statistiken är aktuell.

> [!NOTE]
> När du uppdaterar all statistik för en tabell, SQL Data Warehouse göra en genomsökning för att sampla tabellen för varje statistik objekt. Om tabellen är stor och har många kolumner och många statistik, kan det vara mer effektivt att uppdatera individuell statistik utifrån behov.

En implementering av en `UPDATE STATISTICS` procedur finns i [temporära tabeller](sql-data-warehouse-tables-temporary.md). Implementerings metoden skiljer sig något från föregående `CREATE STATISTICS` procedur, men resultatet är detsamma.

Fullständig syntax finns i [Uppdatera statistik](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Metadata för statistik

Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistik objekt kan vara inaktuellt genom att använda funktionen statistik datum för att se när statistiken senast skapades eller uppdaterades.

### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik

Dessa system visningar innehåller information om statistik:

| katalogvy | Beskrivning |
|:--- |:--- |
| [sys. columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |En rad för varje kolumn. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |En rad för varje objekt i databasen. |
| [sys. schema](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |En rad för varje schema i databasen. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |En rad för varje statistik objekt. |
| [sys. stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |En rad för varje kolumn i objektet statistik. Länkar tillbaka till sys. columns. |
| [sys. tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |En rad för varje tabell (inklusive externa tabeller). |
| [sys. table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |En rad för varje datatyp. |

### <a name="system-functions-for-statistics"></a>System funktioner för statistik

Dessa system funktioner är användbara när du arbetar med statistik:

| System funktion | Beskrivning |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Datum då statistik objekt senast uppdaterades. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Sammanfattnings nivå och detaljerad information om distributionen av värden som förstås av statistik objekt. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistik kolumner och funktioner i en vy

I den här vyn visas kolumner som relaterar till statistik och resultat från STATS_DATE ()-funktionen tillsammans.

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

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS ()-exempel

DBCC SHOW_STATISTICS () visar data som lagras i ett statistik objekt. Dessa data ingår i tre delar:

- Huvud
- Densitets vektor
- Tillägget

Metadata för sidhuvudet om statistiken. Histogrammet visar fördelningen av värden i den första nyckel kolumnen i statistik-objektet. Täthets vektorn mäter en jämförelse mellan kolumner. SQL Data Warehouse beräknar kardinalitet uppskattningar med alla data i objektet statistik.

### <a name="show-header-density-and-histogram"></a>Visa rubrik, densitet och histogram

Det här enkla exemplet visar alla tre delarna i ett statistik objekt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Till exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS ()

Om du bara vill visa vissa delar använder du `WITH`-satsen och anger vilka delar du vill se:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Till exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS () skillnader

DBCC SHOW_STATISTICS () är mer strikt implementerad i SQL Data Warehouse jämfört med SQL Server:

- Det finns inte stöd för icke-dokumenterade funktioner.
- Det går inte att använda Stats_stream.
- Det går inte att ansluta resultat för vissa del mängder av statistik data. Till exempel STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS kan inte ställas in för meddelande under tryckning.
- Hakparenteser runt statistik namn kan inte användas.
- Det går inte att använda kolumn namn för att identifiera statistik objekt.
- Anpassad fel 2767 stöds inte.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förbättra prestanda för frågor finns i [övervaka din arbets belastning](sql-data-warehouse-manage-monitor.md)
