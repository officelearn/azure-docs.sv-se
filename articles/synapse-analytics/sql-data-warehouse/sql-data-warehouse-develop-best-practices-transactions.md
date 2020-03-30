---
title: Optimizing transactions (Optimera transaktioner)
description: Lär dig hur du optimerar prestanda för transaktionskoden i SQL Analytics samtidigt som du minimerar risken för långa återställningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 700f4717db652d678255aaa9fce6ff8b8ff3b52f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350592"
---
# <a name="optimizing-transactions-in-sql-analytics"></a>Optimera transaktioner i SQL Analytics
Lär dig hur du optimerar prestanda för transaktionskoden i SQL Analytics samtidigt som du minimerar risken för långa återställningar.

## <a name="transactions-and-logging"></a>Transaktioner och loggning
Transaktioner är en viktig komponent i en relationsdatabasmotor. SQL Analytics använder transaktioner under dataändring. Dessa transaktioner kan vara explicita eller implicita. Enkel INFOGA, UPPDATERA och TA BORT-satser är alla exempel på implicita transaktioner. Explicita transaktioner använder BEGIN TRAN, COMMIT TRAN eller ROLLBACK TRAN. Explicita transaktioner används vanligtvis när flera ändringssatser måste kopplas samman i en enda atomenhet. 

SQL Analytics genomför ändringar i databasen med hjälp av transaktionsloggar. Varje distribution har en egen transaktionslogg. Transaktionsloggskrivningar är automatiska. Det krävs ingen konfiguration. Men även om denna process garanterar att skriva det inte införa en overhead i systemet. Du kan minimera den här effekten genom att skriva transaktionseffektiv kod. Transaktionseffektiv kod hör i stort sett in i två kategorier.

* Använd minimala loggningskonstruktioner när det är möjligt
* Bearbeta data med hjälp av begränsade batchar för att undvika ovanliga tidskrävande transaktioner
* Anta ett partitionsväxlingsmönster för stora ändringar av en viss partition

## <a name="minimal-vs-full-logging"></a>Minimal kontra fullständig loggning
Till skillnad från helt loggade åtgärder, som använder transaktionsloggen för att hålla reda på varje radändring, håller minimalt loggade åtgärder endast reda på omfattningsallokeringar och metadataändringar. Därför innebär minimal loggning loggning endast den information som krävs för att återställa transaktionen efter ett fel, eller för en explicit begäran (ROLLBACK TRAN). Eftersom mycket mindre information spåras i transaktionsloggen, presterar en minimalt loggad åtgärd bättre än en lika stor fullständigt loggad åtgärd. Dessutom, eftersom färre skrivningar går transaktionsloggen, genereras en mycket mindre mängd loggdata och så är mer I / O-effektiv.

Transaktionssäkerhetsgränserna gäller endast för fullständigt loggade åtgärder.

> [!NOTE]
> Minimalt loggade åtgärder kan delta i explicita transaktioner. När alla ändringar i allokeringsstrukturer spåras är det möjligt att återställa minimalt loggade åtgärder. 
> 
> 

## <a name="minimally-logged-operations"></a>Minimalt loggade åtgärder
Följande åtgärder kan loggas minimalt:

* SKAPA TABELL SOM SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* Infoga.. Välj
* CREATE INDEX
* ÄNDRA OMBYGGNAD AV INDEX
* DROP INDEX
* TRUNKERA TABELL
* SLÄPP TABELL
* ÄNDRA PARTITION FÖR TABELLVÄXEL

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Interna datarörelser (till exempel BROADCAST och SHUFFLE) påverkas inte av transaktionssäkerhetsgränsen.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Minimal loggning med massbelastning
CTAS och INSERT... SELECT är båda massbelastningsåtgärder. Båda påverkas dock av måltabelldefinitionen och är beroende av belastningsscenariot. I följande tabell förklaras när massåtgärder är helt eller minimalt inloggade:  

| Primärt index | Inläsningsscenario | Loggningsläge |
| --- | --- | --- |
| Heap |Alla |**Minimal** |
| Grupperat index |Tom måltabell |**Minimal** |
| Grupperat index |Inlästa rader överlappar inte befintliga sidor i målet |**Minimal** |
| Grupperat index |Inlästa rader överlappar befintliga sidor i målet |Fullständig |
| Grupperat Columnstore-index |Batchstorlek >= 102 400 per partitionsjusterad fördelning |**Minimal** |
| Grupperat Columnstore-index |Batchstorlek < 102 400 per partitionsjusterad fördelning |Fullständig |

Det är värt att notera att alla skrivningar för att uppdatera sekundära eller icke-klustrade index alltid kommer att vara helt loggade operationer.

> [!IMPORTANT]
> En SQL Analytics-databas har 60 distributioner. Om alla rader distribueras jämnt och landar i en enda partition måste därför batchen innehålla 6 144 000 rader eller större för att vara minimalt inloggad när du skriver till ett clustered columnstore-index. Om tabellen är partitionerad och raderna infogas intervallpartitionsgränser, behöver du 6 144 000 rader per partitionsgräns förutsatt att även datadistribution. Varje partition i varje distribution måste självständigt överskrida 102 400-radtröskeln för att insatsen ska vara minimalt inloggad i distributionen.
> 
> 

Att läsa in data i en icke-tom tabell med ett klustrade index kan ofta innehålla en blandning av fullständigt loggade och minimalt loggade rader. Ett grupperat index är ett balanserat träd (b-träd) med sidor. Om sidan som skrivs till redan innehåller rader från en annan transaktion loggas dessa skrivningar helt. Men om sidan är tom kommer skrivningen till den sidan att loggas minimalt.

## <a name="optimizing-deletes"></a>Optimera borttagningar
DELETE är en fullständigt loggad åtgärd.  Om du behöver ta bort en stor mängd data i en tabell `SELECT` eller en partition är det ofta mer meningsfullt att de data du vill behålla, som kan köras som en minimalt loggad åtgärd.  Om du vill markera data skapar du en ny tabell med [CTAS](sql-data-warehouse-develop-ctas.md).  När du har skapat det använder du [BYT NAMN](/sql/t-sql/statements/rename-transact-sql) FÖR att byta ut din gamla tabell med den nyskapade tabellen.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimera uppdateringar
UPDATE är en fullständigt loggad åtgärd.  Om du behöver uppdatera ett stort antal rader i en tabell eller en partition kan det ofta vara mycket effektivare att använda en minimalt loggad åtgärd som [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) för att göra det.

I exemplet nedan har en fullständig tabelluppdatering konverterats till ett CTAS så att minimal loggning är möjlig.

I det här fallet lägger vi i efterhand till ett rabattbelopp till försäljningen i tabellen:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Återskapa stora tabeller kan dra nytta av att använda SQL Analytics arbetsbelastningshanteringsfunktioner. Mer information finns i [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Optimera med partitionsväxling
Om man står inför storskaliga ändringar inuti en [tabellpartition](sql-data-warehouse-tables-partition.md)är ett partitionsväxlingsmönster vettigt. Om dataändringen är betydande och sträcker sig över flera partitioner, uppnår iterering över partitionerna samma resultat.

Stegen för att utföra en partitionsväxel är följande:

1. Skapa en tom utpartition
2. Utföra "uppdateringen" som en CTAS
3. Växla ut befintliga data till uttabellen
4. Växla in nya data
5. Rensa data

Men för att identifiera partitioner att byta, skapa följande hjälpprocedur.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Den här proceduren maximerar återanvändning av kod och håller partitionsväxlingsexempelet mer kompakt.

Följande kod visar de steg som tidigare nämnts för att uppnå en fullständig partitionsväxlingsrutin.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimera loggning med små batchar
För stora datamodifieringsåtgärder kan det vara klokt att dela upp åtgärden i segment eller batchar för att begränsa arbetsenheten.

En följande kod är ett fungerande exempel. Batchstorleken har ställts in på ett trivialt tal för att markera tekniken. I verkligheten skulle partiets storlek vara betydligt större. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pausa och skala vägledning
Med SQL Analytics kan du [pausa, återuppta och skala](sql-data-warehouse-manage-compute-overview.md) din SQL-pool på begäran. När du pausar eller skalar SQL-poolen är det viktigt att förstå att alla transaktioner under flygning avslutas omedelbart. vilket gör att alla öppna transaktioner återställs. Om din arbetsbelastning hade utfärdat en tidskrävande och ofullständig dataändring före paus- eller skalningsåtgärden måste det här arbetet ångras. Den här undergången kan påverka den tid det tar att pausa eller skala DIN SQL-pool. 

> [!IMPORTANT]
> Båda `UPDATE` `DELETE` och är helt loggade åtgärder och så dessa ångra / gör om åtgärder kan ta betydligt längre tid än motsvarande minimalt loggade åtgärder. 
> 
> 

Det bästa scenariot är att låta i flygdata ändringstransaktioner slutföras innan pausa eller skala SQL pool. Men det här scenariot kanske inte alltid är praktiskt. Om du vill minska risken för en lång återställning bör du överväga något av följande alternativ:

* Skriva om tidskrävande åtgärder med [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Dela upp operationen i segment. arbetar på en delmängd av raderna

## <a name="next-steps"></a>Nästa steg
Se [Transaktioner i SQL Analytics](sql-data-warehouse-develop-transactions.md) om du vill veta mer om isoleringsnivåer och transaktionsgränser.  En översikt över andra metodtips finns i [bästa praxis för SQL Data Warehouse](sql-data-warehouse-best-practices.md).

