---
title: Exempel på minne
description: Prova Azure SQL Database In-Memory-tekniker med OLTP och columnstore-exempel.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b707d67c88eb550d397134b2294c1c5b0e1f7f7d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528219"
---
# <a name="in-memory-sample"></a>Exempel på minne

Med minnesteknik i Azure SQL Database kan du förbättra prestanda för ditt program och eventuellt minska kostnaderna för databasen. Genom att använda minnesminnestekniker i Azure SQL Database kan du uppnå prestandaförbättringar med olika arbetsbelastningar.

I den här artikeln ser du två exempel som illustrerar användningen av IN-Memory OLTP, samt columnstore index i Azure SQL Database.

Mer information finns i:
- [Oltp-översikt och användningsscenarier för minne](https://msdn.microsoft.com/library/mt774593.aspx) (innehåller referenser till fallstudier och information för att komma igång)
- [Dokumentation för IN-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore Indexer Guide](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridtransaktions-/analytisk bearbetning (HTAP), även känd som [operativ analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installera OLTP-exemplet i minnet

Du kan skapa exempeldatabasen AdventureWorksLT med några klick i [Azure-portalen](https://portal.azure.com/). Sedan förklarar stegen i det här avsnittet hur du kan berika din AdventureWorksLT-databas med IN-Memory OLTP-objekt och visa prestandafördelar.

En mer förenklad men mer visuellt tilltalande prestandademo för IN-Memory OLTP finns i:

- Släpp: [i-minne-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Källkod: [i-minne-oltp-demo-källkod](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

#### <a name="installation-steps"></a>Installationssteg

1. Skapa en Premium- eller affärskritisk databas på en server i [Azure-portalen.](https://portal.azure.com/) Ställ in **källan till** exempeldatabasen AdventureWorksLT. Detaljerade instruktioner finns i [Skapa din första Azure SQL-databas](sql-database-single-database-get-started.md).

2. Anslut till databasen med SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Kopiera [IN-Memory OLTP Transact-SQL-skriptet](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) till urklipp. T-SQL-skriptet skapar nödvändiga in-memory-objekt i AdventureWorksLT-exempeldatabasen som du skapade i steg 1.

4. Klistra in T-SQL-skriptet i SSMS och kör sedan skriptet. Klausulen `MEMORY_OPTIMIZED = ON` SKAPA TABELL uttalanden är avgörande. Ett exempel:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Fel 40536


Om du får felmeddelande 40536 när du kör T-SQL-skriptet kör du följande T-SQL-skript för att kontrollera om databasen stöder In-Memory:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Ett resultat av **0** innebär att In-Memory inte stöds, och **1** innebär att det stöds. Om du vill diagnostisera problemet kontrollerar du att databasen finns på Premium-tjänstnivån.


#### <a name="about-the-created-memory-optimized-items"></a>Om de skapade minnesoptimerade objekten

**Tabeller**: Exemplet innehåller följande minnesoptimerade tabeller:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Du kan granska minnesoptimerade tabeller via **Objektutforskaren** i SSMS.  > Högerklicka på >  **Tables****Tabellfilterfilterinställningar****Filter Settings** > **är minnesoptimerat**. Värdet är lika med 1.


Du kan också fråga om katalogvyerna, till exempel:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Inbyggt kompilerad lagrad procedur:** Du kan inspektera SalesLT.usp_InsertSalesOrder_inmem genom en katalogvyfråga:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Kör exempel-OLTP-arbetsbelastningen

Den enda skillnaden mellan följande två *lagrade procedurer* är att den första proceduren använder minnesoptimerade versioner av tabellerna, medan den andra proceduren använder de vanliga tabellerna på disken:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


I det här avsnittet ser du hur du använder verktyget handy **ostress.exe** för att köra de två lagrade procedurerna på stressiga nivåer. Du kan jämföra hur lång tid det tar för de två stresskörningarna att slutföras.


När du kör ostress.exe rekommenderar vi att du skickar parametervärden som utformats för båda följande:

- Kör ett stort antal samtidiga anslutningar med hjälp av -n100.
- Ha varje anslutningsloop hundratals gånger, med hjälp av -r500.


Du kanske vill börja med mycket mindre värden som -n10 och -r50 för att säkerställa att allt fungerar.


### <a name="script-for-ostressexe"></a>Skript för ostress.exe


I det här avsnittet visas det T-SQL-skript som är inbäddat i vår kommandorad ostress.exe. Skriptet använder objekt som har skapats av det T-SQL-skript som du installerade tidigare.


Följande skript infogar en exempelförsäljningsorder med fem radartiklar i följande minnesoptimerade *tabeller:*

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Om du vill göra *_ondisk* versionen av det föregående T-SQL-skriptet för ostress.exe ersätter du båda förekomsterna av *_inmem* delsträng med *_ondisk*. Dessa ersättningar påverkar namnen på tabeller och lagrade procedurer.


### <a name="install-rml-utilities-and-ostress"></a>Installera RML-verktyg och`ostress`


Helst planerar du att köra ostress.exe på en virtuell Azure-dator (VM). Du skulle skapa en [virtuell Azure-dator](https://azure.microsoft.com/documentation/services/virtual-machines/) i samma geografiska Azure-region där din AdventureWorksLT-databas finns. Men du kan köra ostress.exe på din bärbara dator istället.


Installera RML-verktygen (Replay Markup Language) på den virtuella datorn eller vilken värd du än väljer. Verktygen inkluderar ostress.exe.

Mer information finns i:
- Diskussionen ostress.exe i [Exempeldatabas för OLTP i minnet](https://msdn.microsoft.com/library/mt465764.aspx).
- [Exempeldatabas för IN-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Bloggen för att installera ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Kör *_inmem* stressarbetsbelastningen först


Du kan använda ett *RML-meddelandefönster* för att köra vår kommandorad ostress.exe. Kommandoradsparametrarna direkt `ostress` till:

- Kör 100 anslutningar samtidigt (-n100).
- Låt varje anslutning köra T-SQL-skriptet 50 gånger (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Så här kör du föregående ostress.exe-kommandorad:


1. Återställ databasdatainnehållet genom att köra följande kommando i SSMS om du vill ta bort alla data som infogats av tidigare körningar:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopiera texten på föregående ostress.exe-kommandorad till Urklipp.

3. Ersätt `<placeholders>` parametrarna -S -U -P -d med rätt verkliga värden.

4. Kör den redigerade kommandoraden i ett RML-cmd-fönster.


#### <a name="result-is-a-duration"></a>Resultatet är en varaktighet


När `ostress.exe` den är klar skrivs körningen som den sista utdataraden i fönstret RML Cmd. En kortare provkörning varade till exempel i ungefär 1,5 minuter:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Återställ, redigera för *_ondisk*och kör sedan om


När du har resultatet från *_inmem* körningen utför du följande steg för *_ondisk* körningen:


1. Återställ databasen genom att köra följande kommando i SSMS för att ta bort alla data som infogades av föregående körning:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Redigera kommandoraden ostress.exe om du vill ersätta alla *_inmem* med *_ondisk*.

3. Kör ostress.exe för andra gången och fånga varaktighetsresultatet.

4. Återigen, återställa databasen (för ansvarsfullt ta bort vad som kan vara en stor mängd testdata).


#### <a name="expected-comparison-results"></a>Förväntade jämförelseresultat

Våra minnestester har visat att prestanda förbättrades med **nio** gånger `ostress` för den här förenklade arbetsbelastningen, med att köras på en Azure VM i samma Azure-region som databasen.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installera exemplet med in-memory analytics


I det här avsnittet jämför du IO- och statistikresultaten när du använder ett columnstore-index jämfört med ett traditionellt b-trädindex.


För analyser i realtid på en OLTP-arbetsbelastning är det ofta bäst att använda ett icke-grupperat columnstore-index. Mer information finns i [Columnstore-index som beskrivs](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Förbereda analystestet för columnstore


1. Använd Azure-portalen för att skapa en ny AdventureWorksLT-databas från exemplet.
   - Använd det exakta namnet.
   - Välj valfri Premium-tjänstnivå.

2. Kopiera [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) till Urklipp.
   - T-SQL-skriptet skapar nödvändiga in-memory-objekt i AdventureWorksLT-exempeldatabasen som du skapade i steg 1.
   - Skriptet skapar tabellen Dimension och två faktatabeller. Faktumet bordlägger fylls med 3.5 miljon ror varje.
   - Skriptet kan ta 15 minuter att slutföra.

3. Klistra in T-SQL-skriptet i SSMS och kör sedan skriptet. Nyckelordet **COLUMNSTORE** i **CREATE INDEX-satsen** är avgörande, som i:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ställ In AdventureWorksLT på kompatibilitetsnivå 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Nivå 130 är inte direkt relaterad till funktionerna i minnet. Men nivå 130 ger i allmänhet snabbare frågeprestanda än 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Nyckeltabeller och columnstore-index


- Dbo. FactResellerSalesXL_CCI är en tabell som har ett grupperat columnstore-index, som har avancerad komprimering på *datanivå.*

- Dbo. FactResellerSalesXL_PageCompressed är en tabell som har ett motsvarande vanligt klustrade index, som bara komprimeras på *sidnivå.*


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Viktiga frågor för att jämföra columnstore-indexet


Det finns [flera T-SQL-frågetyper som du kan köra](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) för att se prestandaförbättringar. I steg 2 i T-SQL-skriptet, var uppmärksam på det här par frågor. De skiljer sig bara på en rad:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Ett grupperat columnstore-index finns i tabellen FactResellerSalesXL\_CCI.

Följande T-SQL-skriptutdrag skriver ut statistik för I/O och TIME för frågan för varje tabell.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

I en databas med P2-prisnivån kan du förvänta dig ungefär nio gånger prestandaökningen för den här frågan med hjälp av det klustrade columnstore-indexet jämfört med det traditionella indexet. Med P15 kan du förvänta dig cirka 57 gånger prestandavinsten genom att använda columnstore-indexet.



## <a name="next-steps"></a>Nästa steg

- [Snabbstart 1: OLTP-tekniker i minnet för snabbare T-SQL-prestanda](https://msdn.microsoft.com/library/mt694156.aspx)

- [Använda IN-Memory OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

- [Övervaka OLTP-lagring](sql-database-in-memory-oltp-monitoring.md) i minnet för OLTP i minnet


## <a name="additional-resources"></a>Ytterligare resurser

#### <a name="deeper-information"></a>Djupare information

- [Lär dig hur Quorum fördubblar nyckeldatabasens arbetsbelastning samtidigt som du sänker DTU med 70 % med IN-Memory OLTP i SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP i Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Lär dig mer om OLTP i minnet](https://msdn.microsoft.com/library/dn133186.aspx)

- [Läs mer om columnstore-index](https://msdn.microsoft.com/library/gg492088.aspx)

- [Läs mer om operativ analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

- Se [Vanliga arbetsbelastningsmönster och migreringsöverväganden](https://msdn.microsoft.com/library/dn673538.aspx) (som beskriver arbetsbelastningsmönster där OLTP i minnet ofta ger betydande prestandavinster)

#### <a name="application-design"></a>Programdesign

- [OLTP (optimering i minnet)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Använda IN-Memory OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Verktyg

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
