---
title: Optimera transaktioner för Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig att optimera prestanda för transaktionell koden i Azure SQL Data Warehouse och minimerar risken för lång återställningar.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/19/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 59467c0cd93141cef56e1c9d2f36b0870a589712
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795542"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Optimera transaktioner i Azure SQL Data Warehouse
Lär dig att optimera prestanda för transaktionell koden i Azure SQL Data Warehouse och minimerar risken för lång återställningar.

## <a name="transactions-and-logging"></a>Transaktioner och loggning
Transaktioner är en viktig komponent i en relationsdatabas-motor. SQL Data Warehouse använder transaktioner under dataändringar. Dessa transaktioner kan vara uttryckliga eller underförstådda. Enskild INSERT-, UPDATE- och DELETE-uttryck är alla exempel på implicit transaktioner. Explicita transaktioner använda BEGIN TRAN, genomför TRAN eller ROLLBACK TRAN. Explicita transaktioner används vanligtvis när flera ändringar instruktioner måste kopplas samman i en enda atomiska enhet. 

Azure SQL Data Warehouse sparar ändringar till databasen med transaktionsloggar. Varje distribution har sin egen transaktionsloggen. Transaktionen sparas loggen automatiskt. Det finns ingen konfiguration krävs. Dock samtidigt som den här processen garanterar skrivningen införs en kostnader i systemet. Du kan minimera den här effekten genom att skriva ett effektivt kod. Transaktionellt effektiv kod hamnar brett i två kategorier.

* Använd minimal loggning konstruktioner när det är möjligt
* Bearbetning av data med begränsade batchar för att undvika enda tidskrävande transaktioner
* Anta en partitionsväxling mönster för stora ändringar i en given partition

## <a name="minimal-vs-full-logging"></a>Minimal kontra fullständig loggning
Till skillnad från fullständigt loggade åtgärder som använder transaktionsloggen för att hålla reda på varje rad ändring, hålla minimalt loggade åtgärder reda på utsträckning allokeringar och endast metadata ändringar. Minimal loggning innebär därför loggning bara den information som krävs för att återställa transaktionen efter en misslyckad eller för en uttrycklig begäran (återställning TRAN). När mycket mindre information spåras i transaktionsloggen, presterar minimalt loggade åtgärden bättre än på samma sätt storlek fullständigt loggade åtgärden. Dessutom eftersom färre skrivningar går transaktionsloggen, en mycket mindre mängd loggdata genereras och det är flera i/o effektivt.

Säkerhetsgränsen transaktionen gäller endast för fullständigt loggade åtgärder.

> [!NOTE]
> Minimalt loggade åtgärder kan delta i explicita transaktioner. Det är möjligt att återställa minimalt loggade åtgärder som alla ändringar i allokering strukturer spåras. 
> 
> 

## <a name="minimally-logged-operations"></a>Minimalt loggade åtgärder
Följande åtgärder kan loggas minimalt:

* SKAPA TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT... VÄLJ
* SKAPA INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNKERA TABELLEN
* SLÄPPA TABELLEN
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Interna data movement åtgärder (till exempel SÄNDNING och BLANDA) påverkas inte av transaktionen säkerhet gränsen.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Minimal loggning med massinläsning
CTAS och INSERT... Välj är båda massåtgärder belastningen. Dock både påverkas av tabelldefinitionen mål och beror på belastningen scenario. I följande tabell beskrivs när massåtgärder helt eller minimalt loggas:  

| Primärt Index | Läs in Scenario | Loggningsläge |
| --- | --- | --- |
| Heap |Alla |**Minimal** |
| Grupperat Index |Tom måltabellen |**Minimal** |
| Grupperat Index |Inlästa rader inte överlappa befintliga sidor i mål |**Minimal** |
| Grupperat Index |Inlästa rader överlappar befintliga sidor i mål |Fullständig |
| Grupperat Columnstore-Index |Batchstorlek > = 102,400 per partition justerad distribution |**Minimal** |
| Grupperat Columnstore-Index |Batch-storlek < 102,400 per partition justerad distribution |Fullständig |

Det är värt att nämna att alla skrivningar för att uppdatera sekundära eller icke-grupperade index alltid är fullständigt loggade åtgärder.

> [!IMPORTANT]
> SQL Data Warehouse har 60-distributioner. Därför, förutsatt att alla rader fördelas jämnt och hamnar i en enda partition din batch måste innehålla 6,144,000 rader eller större loggas minimalt vid skrivning till ett grupperat Columnstore-Index. Om tabellen är partitionerad och raderna som ska infogas span partitionsgränser, behöver du 6,144,000 rader per partition gräns förutsatt att data fördelas jämnt. Varje partition i varje distribution måste oberoende överskrida 102 400 raden tröskelvärdet för insert minimalt vara inloggad på distributionsplatsen.
> 
> 

Läsa in data i en icke-tom tabell med ett grupperat index innehåller ofta en blandning av fullständigt loggade och minimalt loggade rader. Ett grupperat index är ett belastningsutjämnade träd (b-trädet) sidor. Om sidan som skrivs till redan innehåller rader från en annan transaktion, sedan loggas dessa skrivningar fullständigt. Men om sidan är tom loggas sedan skrivningen till sidan minimalt.

## <a name="optimizing-deletes"></a>Optimera borttagningar
Ta bort är fullständigt loggade åtgärden.  Om du vill ta bort en stor mängd data i en tabell eller en partition är det ofta mer praktiskt att `SELECT` data som du vill behålla, som kan köras som ett minimalt loggade åtgärden.  Skapa en ny tabell med för att välja data [CTAS](sql-data-warehouse-develop-ctas.md).  När du skapat använda [Byt namn på](/sql/t-sql/statements/rename-transact-sql) att byta ut din gamla tabell med den nyligen skapade tabellen.

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
UPPDATERING är fullständigt loggade åtgärden.  Om du behöver uppdatera ett stort antal rader i en tabell eller en partition kan det ofta vara mycket mer effektivt att använda minimalt loggade åtgärden som [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) gör.

I exemplet nedan en fullständig tabell konverterats update till ett CTAS så att minimal loggning är möjligt.

I det här fallet vi efterhand lägger till en beloppet för försäljning i tabellen:

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
> Skapa nytt stora tabeller kan dra nytta av funktioner för SQL Data Warehouse arbetsbelastning. Mer information finns i [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Optimera med Växla partition
Om inför storskaliga ändringar i en [tabell partition](sql-data-warehouse-tables-partition.md), och sedan klokt att en partitionsväxling mönster. Om data ändras är viktig och sträcker sig över flera partitioner, ger sedan iterera över partitionerna samma resultat.

Stegen för att utföra en partition växel är följande:

1. Skapa en tom av partition
2. Utför uppdateringen som en CTAS
3. Byta ut befintliga data till tabellen
4. Växla i nya data
5. Rensa data

Skapa följande helper procedur för att identifiera partitioner för att växla.  

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

Den här proceduren maximerar återanvändning och behåller partitionsväxling exempel mer kompakt.

Följande kod visar stegen som beskrivs ovan för att få en fullständig partitionsväxling rutinen.

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
För stora mängder data ändras, kan det vara klokt att dela upp igen i segment eller batchar att definiera omfattningen av arbetsenheten.

Följande kod är ett fungerande exempel. Batchstorleken har angetts till ett trivial antal om du vill markera tekniken. Batchstorleken vara betydligt större i själva verket. 

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

## <a name="pause-and-scaling-guidance"></a>Pausa och skalning vägledning
Azure SQL Data Warehouse kan du [pausa, fortsätta och skala](sql-data-warehouse-manage-compute-overview.md) ditt data warehouse på begäran. När du pausar du eller skala ditt SQL Data Warehouse, är det viktigt att förstå att alla pågående transaktioner avslutas omedelbart. Gör eventuella öppna transaktioner ska återställas till. Om din arbetsbelastning hade utfärdas en lång körs och ofullständiga dataändring innan åtgärden pausa eller skala, behöver detta verk du ångras. Den här ångras kan påverka den tid det tar att pausa eller skala din Azure SQL Data Warehouse-databas. 

> [!IMPORTANT]
> Båda `UPDATE` och `DELETE` är fullständigt loggade åtgärder och så att dessa Ångra/Gör om kan ta betydligt längre tid än motsvarande minimalt loggade åtgärder. 
> 
> 

Bästa scenario är att låta svarta data ändras transaktioner slutförd innan pausa eller skalning SQL Data Warehouse. Men kan det här scenariot inte alltid praktiskt. Överväg att något av följande alternativ för att minska risken för en lång återställning:

* Omarbetning långvariga åtgärder med hjälp av [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Bryt igen i segment; operativsystem för en delmängd av raderna

## <a name="next-steps"></a>Nästa steg
Se [transaktioner i SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) lära dig mer om isoleringsnivåer och transaktionell gränser.  En översikt över andra metodtips finns [Metodtips för SQL Data Warehouse](sql-data-warehouse-best-practices.md).

