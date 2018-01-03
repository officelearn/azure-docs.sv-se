---
title: "Hantera statistik på tabellerna i SQL Data Warehouse | Microsoft Docs"
description: "Komma igång med statistik på tabellerna i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: 4d5777e69b7ea3fa206bf8909c255b998be69e8a
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Hantera statistik på tabellerna i SQL Data Warehouse
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

Ju mer SQL Data Warehouse medveten om dina data, desto snabbare den kan köra frågor mot dina data.  Hur du se SQL Data Warehouse om dina data är genom att samla in statistik om dina data. Med statistik om dina data är en av de viktigaste sakerna som du kan göra för att optimera dina frågor. Detta beror på att Frågeoptimeringen SQL Data Warehouse är en kostnad-baserade optimering. Den jämför kostnaden för olika frågeplaner och väljer plan med lägst kostnad som bör också vara planen som utför snabbast. Till exempel optimering uppskattar att datumet du filtrerar i frågan returnerar 1 rad, det kan välja väldigt annorlunda planera än om den beräknar de datum du har valt kommer returnerar 1 miljon rader.

Processen för att skapa och uppdatera statistik för närvarande är en manuell process, men det är mycket enkelt att göra.  Youw kommer snart att kunna skapa och uppdatera statistik på enskild kolumner och index automatiskt.  Du kan avsevärt automatisera hanteringen av statistik på dina data med hjälp av informationen nedan. 

## <a name="getting-started-with-statistics"></a>Komma igång med statistik
Provade statistik skapas för varje kolumn är ett enkelt sätt att komma igång med statistik. Inaktuella statistik leder till bästa möjliga prestanda. Det kan dock använda minne för att uppdatera statistik på alla kolumner eftersom dina data växer. 

Här följer några rekommendationer för olika scenarier:
| **Scenarier** | Rekommendation |
|:--- |:--- |
| **Kom igång** | Uppdatera alla kolumner när du har migrerat till SQL DW |
| **De viktigaste kolumn för statistik** | Hash-fördelningsnyckel |
| **Andra viktigaste kolumn för statistik** | Partitionsnyckel |
| **Andra viktiga kolumner för statistik** | Datum, ofta kopplingar, gruppera efter, HAVING och var |
| **Frekvens av statistik uppdateringar**  | Konservativ: varje dag <br></br> När du läser in eller Transformera data |
| **Sampling** |  Nedan 1 B rader, använder du standard provtagning (20%) <br></br> Med mer än 1 B rader tabeller är statistik på ett intervall för % 2 bra |

## <a name="updating-statistics"></a>Uppdatera statistik

Ett bra tips är att uppdatera statistik i datumkolumnerna varje dag som nya datum har lagts till. Varje gång nya rader läses in i datalagret, nya belastningen datum eller datum har lagts till. Dessa ändra Datadistributionen och gör uppgifterna inaktuella. Däremot behöva statistik i ett land-kolumn i en kundtabell aldrig uppdateras när fördelningen av värden i allmänhet inte ändra. Under förutsättning att distributionen är konstant mellan kunder, ska lägga till nya rader i tabellen variationen inte du ändra fördelningen data. Men om ditt data warehouse endast innehåller ett land och du hämta data från ett nytt land, måste vilket resulterar i data från flera länder lagras, definitivt du uppdatera statistik i kolumnen land.

En av de första frågorna när felsökning av en fråga är **”är statistik uppdaterade”?**

Den här frågan är inte en som kan betjänas av du åldern på data. Objektets aktuella statistik kan vara mycket gamla om det inte har ingen väsentlig ändring i underliggande data. När antalet rader som har ändrats betydligt eller så finns det en väsentlig förändring fördelningen av värden för en viss kolumn *sedan* är det dags att uppdatera statistik.

Eftersom det inte finns några DMV att avgöra om data i tabellen har ändrats sedan de senaste statistik har uppdaterats, kan vet du åldern på din statistik ge dig med en del av bilden.  Följande fråga kan användas för att fastställa senast statistiken där uppdateras vid varje tabell.  

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

**Datum kolumner** i datalagret, till exempel vanligtvis måste frekventa uppdateringar av statistik. Varje gång nya rader läses in i datalagret, nya belastningen datum eller datum har lagts till. Dessa ändra Datadistributionen och gör uppgifterna inaktuella.  Däremot behöva aldrig statistik på kolumnen kön för en kundtabellen uppdateras. Under förutsättning att distributionen är konstant mellan kunder, ska lägga till nya rader i tabellen variationen inte du ändra fördelningen data. Men om ditt data warehouse bara innehåller en kön och en ny krav resulterar i flera könen måste definitivt du uppdatera statistik i kolumnen kön.

Ytterligare förklaring finns [statistik] [ Statistics] på MSDN.

## <a name="implementing-statistics-management"></a>Implementera hantering av statistik
Det är ofta en bra idé att utöka din datainläsning process för att säkerställa att uppdateras i slutet av belastningen. Inläsningen är när tabeller ändras oftast deras storlek och/eller distribution av värden. Därför är detta en logisk plats för att implementera vissa hanteringsprocesser.

Vissa riktlinjerna tillhandahålls nedan för att uppdatera statistiken under inläsningen:

* Kontrollera att varje inlästa tabell har minst ett statistik objekt uppdateras. Detta uppdaterar tabeller storlek (radantal och antal sidor) information som en del av uppdateringen statistik.
* Fokusera på kolumner som ingår i koppling, GROUP BY, ORDER BY och DISTINCT-satser
* Överväg att uppdatera ”stigande nyckeln” kolumner som transaktionen datum oftare, eftersom dessa värden inte inkluderas i statistik histogram.
* Överväg att uppdatera statiska distributionskolumner mindre ofta.
* Kom ihåg att varje statistik objekt uppdateras i serien. Bara implementera `UPDATE STATISTICS <TABLE_NAME>` kanske inte är optimal - särskilt för många tabeller med många statistik objekt.

> [!NOTE]
> Mer information om [stigande nyckeln] finns i SQL Server 2014 kardinalitet beräkning av modellen vitboken.
> 
> 

Ytterligare förklaring finns [kardinalitet uppskattning] [ Cardinality Estimation] på MSDN.

## <a name="examples-create-statistics"></a>Exempel: Skapa statistik
De här exemplen visar hur du använder olika alternativ för att skapa statistik. Vilka alternativ som du använder för varje kolumn beror på egenskaperna för dina data och hur kolumnen används i frågor.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. Skapa statistik för en kolumn med standardalternativ
Om du vill skapa statistik på en kolumn, bara ange ett namn för objektet statistik och namnet på kolumnen.

Den här syntaxen använder alla standardalternativ. Som standard SQL Data Warehouse **exempel 20 procent** i tabellen när den skapar statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Skapa enkolumns-statistik genom att undersöka varje rad
Standard samplingsfrekvensen 20 procent räcker för de flesta situationer. Du kan dock justera samplingsfrekvensen.

Om du vill använda fullständig tabellen använder du följande syntax:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Exempel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Skapa enkolumns-statistik genom att ange provtagning
Du kan också ange exempelstorleken procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Skapa enkolumns-statistik på bara några rader
Ett annat alternativ du kan skapa statistik på en del av raderna i tabellen. Detta kallas en filtrerad statistik.

Du kan till exempel använda filtrerad statistik när du planerar att fråga en specifik partition för en stor partitionerad tabell. Genom att skapa statistik på partitionen värdena kommer riktighet statistik förbättra och därför förbättra frågeprestanda.

Det här exemplet skapar statistik på ett intervall med värden. Värdena kan enkelt definieras för att matcha intervallet för värden i en partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Frågan måste rymmas i definitionen av objektet statistik för frågeoptimeraren överväga att använda filtrerad statistik när de väljer distribuerade frågeplanen. Med hjälp av det tidigare exemplet, fråga där sats måste ange Kol1 värden mellan 2000101 och 20001231.
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Skapa enkolumns-statistik med alla alternativ
Du kan självklart kombinera alternativen tillsammans. Exemplet nedan skapar en filtrerad statistik-objekt med en anpassad provtagning:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Fullständiga referenser finns [CREATE STATISTICS] [ CREATE STATISTICS] på MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Skapa flera kolumner statistik
Om du vill skapa flera kolumnstatistik att helt enkelt använda föregående exempel, men ange fler kolumner.

> [!NOTE]
> Histogram som används för att beräkna antalet rader i frågeresultatet, är endast tillgängligt för den första kolumnen visas i statistik objektdefinition.
> 
> 

I det här exemplet är histogrammet på *produkten\_kategori*. Statistik för Cross-kolumnen beräknas på *produkten\_kategori* och *produkten\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Eftersom det inte finns en korrelation mellan *produkten\_kategori* och *produkten\_sub\_kategori*, en flera kolumner kan vara användbart om de här kolumnerna kan nås på samma gång.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Skapa statistik på alla kolumner i en tabell
Ett sätt att skapa statistik är att problem CREATE STATISTICS kommandon när du har skapat tabellen.

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

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Använda en lagrad procedur för att skapa statistik på alla kolumner i en databas
SQL Data Warehouse saknar en systemets lagrade procedur som motsvarar [sp_create_stats] [] i SQL Server. Den här lagrade proceduren skapar en kolumn statistik-objekt på varje kolumn i databasen som inte redan har statistik.

Detta hjälper dig att komma igång med din databasdesign. Du kan anpassa efter dina behov.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

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
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
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

Om du vill skapa statistik på alla kolumner i tabellen med den här proceduren bara anropa proceduren.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Exempel: uppdatera statistik
Om du vill uppdatera statistik, kan du:

1. Uppdatera ett objekt av statistik. Ange namnet på objektet statistik som du vill uppdatera.
2. Uppdatera alla statistik objekt i en tabell. Ange namnet på tabellen i stället för en viss statistik-objektet.

### <a name="a-update-one-specific-statistics-object"></a>A. Uppdatera en specifik statistik objekt
Använd följande syntax för att uppdatera en specifik statistik objektet:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Exempel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Du kan minimera den tid och resurser som krävs för att hantera statistik genom att uppdatera statistik för specifika objekt. Detta kräver vissa tankar, men för att välja de bästa statistik objekt att uppdatera.

### <a name="b-update-all-statistics-on-a-table"></a>B. Uppdatera all statistik för en tabell
Detta visar en enkel metod för att uppdatera statistik objekt i en tabell.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Exempel:

```sql
UPDATE STATISTICS dbo.table1;
```

Den här instruktionen är enkla att använda. Kom ihåg detta uppdaterar alla statistik i tabellen och därför kan utföra mer arbete än nödvändigt. Om prestanda inte är ett problem, men detta är definitivt det enklaste och sättet att garantera statistik är uppdaterade.

> [!NOTE]
> När du uppdaterar all statistik för en tabell, stöder SQL Data Warehouse en sökning, till exempel tabellen för varje statistik. Om tabellen är stor, har många kolumner och många statistik, kan det vara mer effektivt att uppdatera enskilda statistik baserat på behov.
> 
> 

För en implementering av en `UPDATE STATISTICS` proceduren finns i [temporära tabeller] [ Temporary] artikel. Metoden implementering är något annorlunda till den `CREATE STATISTICS` proceduren ovan men slutresultatet är samma.

Den fullständiga syntaxen finns [Update Statistics] [ Update Statistics] på MSDN.

## <a name="statistics-metadata"></a>Statistik metadata
Det finns flera systemvy och funktioner som du kan använda för att hitta information om statistik. Du kan till exempel se om ett statistik-objekt kan vara inaktuell med hjälp av funktionen stats datum när statistiken skapades eller uppdaterades senast.

### <a name="catalog-views-for-statistics"></a>Katalogvyer för statistik
Dessa systemvyer innehåller information om statistik:

| Vyn katalog | Beskrivning |
|:--- |:--- |
| [sys.Columns][sys.columns] |En rad för varje kolumn. |
| [sys.Objects][sys.objects] |En rad för varje objekt i databasen. |
| [sys.schemas][sys.schemas] |En rad för varje schema i databasen. |
| [sys.stats][sys.stats] |En rad för varje objekt i statistik. |
| [sys.stats_columns][sys.stats_columns] |En rad för varje kolumn i statistik-objektet. Länkar till sys.columns. |
| [sys.Tables][sys.tables] |En rad för varje tabell (inklusive externa tabeller). |
| [sys.table_types][sys.table_types] |En rad för varje datatyp. |

### <a name="system-functions-for-statistics"></a>Systemfunktioner för statistik
Dessa systemfunktioner är användbara för att arbeta med statistik:

| Systemfunktionen | Beskrivning |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Datum statistik objektet senast uppdaterades. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Innehåller översiktlig nivå och detaljerad information om distributionen av värden som tolkas av statistik-objektet. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinera statistik kolumner och funktioner i en vy
Den här vyn visar kolumnerna som relaterar till statistik och fås [STATS_DATE()] [] funktionen tillsammans.

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
DBCC SHOW_STATISTICS() visar de data som lagras i ett statistik-objekt. Dessa data finns i tre delar.

1. Sidhuvud
2. Densitet Vector
3. Histogram

Huvudet metadata om statistik. Histogrammet visar fördelningen av värden i den första nyckelkolumnen i objektet statistik. Densitet vector åtgärder mellan kolumnen korrelation. SQLDW beräknar kardinalitet uppskattningar med data i statistik-objektet.

### <a name="show-header-density-and-histogram"></a>Visa sidhuvud, densitet och histogram
Det här enkla exemplet visar alla tre delar av ett objekt av statistik.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Visa en eller flera delar av DBCC SHOW_STATISTICS();
Om du bara vill visa specifika delar använder den `WITH` satsen och ange vilka delar som du vill visa:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Exempel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() skillnader
DBCC SHOW_STATISTICS() implementeras striktare i SQL Data Warehouse jämfört med SQL Server.

1. Odokumenterade funktioner stöds inte
2. Det går inte att använda Stats_stream
3. Kan inte ansluta resultat för specifika delmängder av statistikdata, t.ex. (STAT_HEADER koppling DENSITY_VECTOR)
4. NO_INFOMSGS kan inte anges för Undertryckning meddelande
5. Hakparenteser runt statistik namn kan inte användas
6. Det går inte att använda kolumnnamn för att identifiera statistik objekt
7. Anpassade fel 2767 stöds inte

## <a name="next-steps"></a>Nästa steg
Mer information finns i [DBCC SHOW_STATISTICS] [ DBCC SHOW_STATISTICS] på MSDN.  Mer information finns i artiklar på [tabell översikt][Overview], [Data tabelltyper][Data Types], [distribuerar en tabell] [ Distribute], [Indexering av en tabell][Index], [partitionering en tabell] [ Partition] och [Temporära tabeller][Temporary].  Mer information om metodtips finns [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
