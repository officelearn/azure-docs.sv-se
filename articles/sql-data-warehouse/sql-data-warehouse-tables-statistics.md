---
title: Skapa, uppdatera statistik – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och exempel för att skapa och uppdatera Frågeoptimeringen statistik på tabellerna i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 05/09/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1a7ea00e8bdf4fa1a22dd765e5108dce72e2d380
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307470"
---
# <a name="creating-updating-statistics-on-tables-in-azure-sql-data-warehouse"></a>Skapa, uppdatera statistik på tabellerna i Azure SQL Data Warehouse
Rekommendationer och exempel för att skapa och uppdatera Frågeoptimeringen statistik på tabellerna i Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Varför använda statistik?
Ju mer Azure SQL Data Warehouse vet om dina data, desto snabbare den kan köra frågor mot den. Samla in statistik på dina data och läser in dem i SQL Data Warehouse är en av de viktigaste sakerna som du kan göra för att optimera dina frågor. Det beror på att Frågeoptimeringen SQL Data Warehouse är en kostnadsbaserad frågeoptimerare. Den jämför kostnaden för olika frågeplaner och väljer sedan planen med den lägsta kostnaden, som i de flesta fall är den plan som körs snabbast. Till exempel om optimering uppskattar att det datum som du filtrerar i frågan returnerar en rad, kan det välja ett annat schema än om den beräknar som det valda datumet returnerar 1 miljon rader.

## <a name="automatic-creation-of-statistics"></a>Automatisk generering av statistik
När automatiskt skapar statistik är valt AUTO_CREATE_STATISTICS, SQL Data Warehouse analyserar inkommande användarfrågor där kolumn statistik skapas för kolumner som saknar statistik. Frågeoptimeringen skapar statistik på enskilda kolumner i frågevillkoret för predikatet eller Anslut till att förbättra kardinalitet beräkningarna för frågeplanen. Automatisk generering av statistik är för närvarande aktiverat som standard.

Du kan kontrollera om ditt informationslager har det konfigurerade genom att köra följande kommando:

```sql
SELECT name, is_auto_create_stats_on 
FROM sys.databases
```
Om ditt informationslager inte har konfigurerats AUTO_CREATE_STATISTICS, rekommenderar vi att du aktiverar den här egenskapen genom att köra följande kommando:

```sql
ALTER DATABASE <yourdatawarehousename> 
SET AUTO_CREATE_STATISTICS ON
```
Följande instruktioner utlöser automatisk generering av statistik: Välj INSERT-Välj, CTAS, uppdatera, ta bort och FÖRKLARA när som innehåller en koppling eller förekomsten av ett predikat som har identifierats. 

> [!NOTE]
> Automatisk generering av statistik skapas inte för tillfällig eller externa tabeller.
> 

Automatisk generering av statistik skapas synkront, så en liten försämrad frågeprestanda kan tillkomma om dina kolumner inte redan har statistik som skapats för dessa. Skapa statistik kan ta några sekunder på en enda kolumn beroende på storleken på tabellen. För att undvika att mäta prestandaförsämring, särskilt i prestandatest, bör du kontrollera statistik har skapats först genom att köra benchmark-arbetsbelastningen innan Profileringen systemet.

> [!NOTE]
> Skapandet av statistik loggas också i [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016) under en annan användare.
> 

När automatisk statistik skapas de ska vara i formatet: _WA_Sys_< 8 siffra kolumn-id i Hex > _ < 8 siffra tabell-id i Hex >. Du kan visa statistik som redan har skapats genom att köra den [DBCC SHOW_STATISTICS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=sql-server-2017) kommando:

```sql
DBCC SHOW_STATISTICS (<tablename>, <targetname>)
```
Det första argumentet är en tabell som innehåller statistik som ska visas. Detta kan inte vara en extern tabell. Det andra argumentet är namnet på den målindex, statistik eller kolumnen som du vill visa statistikinformation.



## <a name="updating-statistics"></a>Uppdatera statistik

Ett bra tips är att uppdatera statistik på datumkolumner varje dag när nya datum har lagts till. Varje gång nya rader läses in i datalagret, nya belastningen datum eller datum har lagts till. Dessa ändra Datadistributionen och se statistik för gammal. Statistik på en land-kolumn i tabellen för en kund kan däremot aldrig måste uppdateras eftersom distributionen av värden inte ändras Allmänt. Anta att distributionen är konstant mellan kunder och kommer att lägga till nya rader i tabellen variationen inte att ändra Datadistribution. Men om ditt informationslager bara innehåller ett land och du importera data från ett nytt land, måste vilket resulterar i data från flera länder som lagras du uppdatera statistik i kolumnen land.

Här följer några rekommendationer som uppdaterar statistik:

|||
|-|-|
| **Hur ofta den uppdateras stats**  | Konservativ: varje dag <br></br> När du läser in eller omvandla dina data |
| **Sampling** |  Mindre än 1 miljard rader, Använd standard sampling (20 procent) <br></br> Med fler än 1 miljard rader är statistik över flera 2 procent bra |

En av de första frågorna när du felsöker en fråga är **”är statistik uppdaterad”?**

Den här frågan är inte någon som kan betjänas av åldern på data. Ett objekt med uppdaterade statistik kanske gamla om det har förekommit utan väsentlig förändring i underliggande data. När antalet rader som har ändrats avsevärt eller om det finns en väsentlig förändring i distributionen av värden för en kolumn, *sedan* är det dags att uppdatera statistik.

Eftersom det finns inga dynamiska hanteringsvyn vyn för att avgöra om data i tabellen har ändrats sedan de senaste statistik har uppdaterats, kan att känna till ålder statistik ge dig med en del av en bild.  Du kan använda följande fråga för att fastställa den senaste gången statistik har uppdaterats i varje tabell.  

> [!NOTE]
> Kom ihåg att om det finns en väsentlig förändring i distributionen av värden för en kolumn, bör du uppdatera statistik, oavsett den senaste gången som de har uppdaterats.  
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

**Datum kolumner** i ett datalager, till exempel vanligtvis behöver frekventa uppdateringar av statistik. Varje gång nya rader läses in i datalagret, nya belastningen datum eller datum har lagts till. Dessa ändra Datadistributionen och se statistik för gammal.  Däremot behöva aldrig statistik på en kolumn för kön i en kundtabell som ska uppdateras. Anta att distributionen är konstant mellan kunder och kommer att lägga till nya rader i tabellen variationen inte att ändra Datadistribution. Men om ditt informationslager innehåller endast en kön och ett nytt krav resultat i flera kön, måste du uppdatera statistik i kolumnen kön.

Mer information finns i allmänna riktlinjer för [statistik](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementera hantering av statistik
Det är ofta en bra idé att utöka din datainläsning process för att säkerställa att uppdateras i slutet av belastningen. Inläsningen är när tabeller oftast ändrar sin storlek och/eller deras distribution av värden. Därför är detta en logisk plats för att implementera vissa processer.

Följande riktlinjerna tillhandahålls för att uppdatera statistik under inläsningen:

* Kontrollera att varje inlästa tabell har minst ett statistik objekt uppdateras. Detta uppdaterar tabellinformation för storlek (radantal och antal sidor) som en del av uppdateringen statistik.
* Fokusera på kolumner som ingår i JOIN, GROUP BY, ORDER BY och DISTINCT-satser.
* Överväg att uppdatering av ”stigande nyckeln” kolumner som transaktion datum oftare, eftersom dessa värden inte inkluderas i statistik histogrammet.
* Överväg att uppdatera statisk distributionskolumner mindre ofta.
* Kom ihåg att varje statistik objekt uppdateras i följd. Bara implementera `UPDATE STATISTICS <TABLE_NAME>` är inte alltid perfekt, särskilt för breda tabeller med många av statistik objekt.

Mer information finns i [Kardinalitetsberäknare](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Exempel: Skapa statistik
De här exemplen visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen kommer att användas i frågor.

### <a name="create-single-column-statistics-with-default-options"></a>Skapa statistik för en kolumn med standardalternativ
För att skapa statistik på en kolumn, tillhandahåller du helt enkelt ett namn för objektet statistik och namnet på kolumnen.

Den här syntaxen använder alla standardalternativ. Som standard SQL Data Warehouse-exempel **20 procent** i tabellen när den skapar statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Skapa enkolumns-statistik genom att undersöka varje rad
Standard samplingsfrekvensen på 20 procent räcker för de flesta situationer. Du kan dock justera samplingsfrekvensen.

Använd följande syntax för att sampla hela tabellen:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Skapa enkolumns-statistik genom att ange urvalsstorlek
Du kan också ange urvalsstorlek procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Skapa enkolumns-statistik på bara några rader
Du kan också skapa statistik på en del av raderna i tabellen. Detta kallas en filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en specifik partition av en stor partitionerad tabell. Genom att skapa statistik på partitionen värdena kommer riktighet statistik förbättra och därmed förbättra frågeprestanda.

Det här exemplet skapar statistik på ett intervall med värden. Värdena kan enkelt definieras för att matcha en uppsättning värden i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Frågan måste rymmas i definitionen av objektet statistik för Frågeoptimeringen överväga användning av filtrerad statistik när de väljer den distribuerade frågeplanen. Med hjälp av det tidigare exemplet frågan var sats måste ange Kol1 värden mellan 2000101 och 20001231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Skapa statistik för en kolumn med alla alternativ
Du kan också kombinera alternativen tillsammans. I följande exempel skapas en filtrerad statistik objekt med en anpassad exempelstorlek:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Fullständiga referenser finns i [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Skapa statistik för flera kolumner
Om du vill skapa ett objekt med flera kolumnstatistik att helt enkelt använda i föregående exempel, men ange flera kolumner.

> [!NOTE]
> Histogram som används för att uppskatta antalet rader i frågeresultatet, är endast tillgänglig för den första kolumnen visas i statistik objektdefinition.
> 
> 

I det här exemplet är histogrammet på *produkten\_kategori*. Cross-kolumnstatistik beräknas *produkten\_kategori* och *produkten\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det finns en korrelation mellan *produkten\_kategori* och *produkten\_sub\_kategori*, ett flera kolumnstatistik-objekt kan vara användbart om dessa kolumner som kan nås på samma gång.

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
SQL Data Warehouse har inte en motsvarande sp_create_stats systemlagrade proceduren i SQL Server. Den här lagrade proceduren skapar en enda kolumn statistik objekt för varje kolumn på den databas som inte redan har statistik.

I följande exempel hjälper dig att komma igång med din databasdesign. Passa på att anpassa det efter dina behov:

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

För att skapa statistik på alla kolumner i tabellen med hjälp av standardinställningarna, bara anropa proceduren.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Anropa den här proceduren för att skapa statistik på alla kolumner i tabellen med hjälp av en fullscan:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
Ange 3 och exempelprocenten för att skapa Exempelstatistik för alla kolumner i tabellen.  Den här proceduren använder en 20 procent frekvens.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Skapa statistik för alla kolumner som samplas 

## <a name="examples-update-statistics"></a>Exempel: Uppdatera statistik
Om du vill uppdatera statistik, kan du:

- Uppdatera ett objekt i statistik. Ange namnet på objektet statistik som du vill uppdatera.
- Uppdatera alla statistik objekt i en tabell. Ange namnet på tabellen i stället för en viss statistik objekt.

### <a name="update-one-specific-statistics-object"></a>Uppdatera en viss statistik objektet
Använd följande syntax för att uppdatera en viss statistik objektet:

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

Den här instruktionen är enkel att använda. Kom bara ihåg att den uppdaterar *alla* statistik i tabellen, och därför kan utföra mer arbete än nödvändigt. Om prestandan inte är ett problem, är detta det enklaste och sättet att garantera att statistik är uppdaterade.

> [!NOTE]
> När du uppdaterar all statistik för en tabell i SQL Data Warehouse stöder en sökning till exempel tabellen för varje objekt i statistik. Om tabellen är stor och har många kolumner och många statistik, kan det vara mer effektivt att uppdatera enskilda statistik baserat på behov.
> 
> 

För en implementering av en `UPDATE STATISTICS` proceduren finns i [temporära tabeller](sql-data-warehouse-tables-temporary.md). Metoden implementering skiljer sig från föregående `CREATE STATISTICS` procedur, men resultatet är samma.

Läs den fullständiga syntaxen [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statistik metadata
Det finns flera systemvyer och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett objekt med statistik kan vara inaktuell med hjälp av funktionen statistik datum för att se när statistik skapades eller uppdaterades senast.

### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik
Dessa data innehåller information om statistik:

| Katalogvy | Beskrivning |
|:--- |:--- |
| [sys.Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |En rad för varje kolumn. |
| [sys.Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |En rad för varje objekt i databasen. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |En rad för varje schema i databasen. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |En rad för varje objekt i statistik. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |En rad för varje kolumn i objektet statistik. Länkar tillbaka till sys.columns. |
| [sys.Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |En rad för varje tabell (inklusive externa tabeller). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |En rad för varje datatyp. |

### <a name="system-functions-for-statistics"></a>Systemfunktioner för statistik
Dessa systemfunktioner är användbara för att arbeta med statistik:

| Systemfunktion | Beskrivning |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Datum statistik objektet uppdaterades senast. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Sammanfattning av nivå och detaljerad information om distributionen av värden som tolkas av objektet statistik. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera kolumner för statistik och funktioner i en vy
Den här vyn ger kolumner som är relaterade till statistik och resultat av funktionen STATS_DATE() tillsammans.

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
DBCC SHOW_STATISTICS() visar data som lagras i ett objekt med statistik. Dessa data finns i tre delar:

- Sidhuvud
- Densitet vektor
- Histogram

Huvudet metadata om statistik. Histogrammet visar fördelningen av värden i den första nyckelkolumnen objektets statistik. Densitet vektor mäter korrelation mellan kolumner. SQL Data Warehouse beräknar kardinalitet uppskattningar med data i objektet statistik.

### <a name="show-header-density-and-histogram"></a>Visa rubrik och densitet histogram
Det här enkla exemplet visar alla tre delar av ett objekt för statistik:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS()
Om du bara vill visa vissa delar, använda den `WITH` satsen och ange vilka delar som du vill se:

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
- Det går inte att ansluta till resultaten för specifika delmängder av statistikdata. Till exempel (STAT_HEADER ansluta DENSITY_VECTOR).
- NO_INFOMSGS kan inte anges för Undertryckning av meddelandet.
- Hakparenteser kring statistik namn kan inte användas.
- Det går inte att använda kolumnnamn för att identifiera statistik objekt.
- Anpassat fel 2767 stöds inte.

## <a name="next-steps"></a>Nästa steg
För ytterligare förbättra frågeprestanda, se [övervaka din arbetsbelastning](sql-data-warehouse-manage-monitor.md)

