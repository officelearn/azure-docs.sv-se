---
title: Exempel på Azure SQL Database InMemory-| Microsoft Docs
description: Prova Azure SQL Database minnesinterna tekniker med OLTP och columnstore exempel.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 51cf04509608435117e0368b25952a58f7fc3557
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609709"
---
# <a name="in-memory-sample"></a>InMemory-exempel

Minnesinterna tekniker i Azure SQL Database kan du förbättra programmets prestanda och minska kostnaden för din databas. Du kan uppnå prestandaförbättringar med olika arbetsbelastningar med hjälp av minnesinterna tekniker i Azure SQL Database.

I den här artikeln visas två exempel som illustrerar användningen av In-Memory OLTP, samt columnstore-index i Azure SQL Database.

Mer information finns i:
- [InMemory-OLTP-översikt och Användningsscenarier](https://msdn.microsoft.com/library/mt774593.aspx) (innehåller referenser till kundfallstudier och information för att komma igång)
- [Dokumentation för Minnesintern OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guide för Kolumnlagringsindex](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrid transaktionella/analytisk bearbetning (HTAP), även kallat [operativa analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installera InMemory-OLTP-exempel

Du kan skapa AdventureWorksLT-exempeldatabasen med ett par klick i den [Azure-portalen](https://portal.azure.com/). Sedan beskrivs stegen i det här avsnittet hur du kan utöka din AdventureWorksLT-databasen med In-Memory OLTP-objekt och demonstrera prestandafördelarna.

En mer förenklad, men mer visuellt tilltalande prestanda demo för In-Memory OLTP finns här:

- Version: [i-minne-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Källkod: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Installationssteg

1. I den [Azure-portalen](https://portal.azure.com/), skapa en Premium- eller affärskritisk databas på en server. Ange den **källa** till AdventureWorksLT-exempeldatabasen. Detaljerade anvisningar finns i [skapa din första Azure SQL-databas](sql-database-get-started-portal.md).

2. Ansluta till databasen med SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Kopiera den [InMemory-OLTP Transact-SQL-skript](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) till Urklipp. T-SQL-skript skapar nödvändiga InMemory-objekt i AdventureWorksLT-exempeldatabasen som du skapade i steg 1.

4. Klistra in T-SQL-skript i SSMS och sedan köra skriptet. Den `MEMORY_OPTIMIZED = ON` satsen CREATE TABLE-instruktioner är avgörande. Exempel:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Fel 40536


Om du får fel 40536 när du kör T-SQL-skript, kör du följande T-SQL-skript för att kontrollera om databasen stöder i minnet:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Ett resultat av **0** innebär att i minnet inte stöds, och **1** innebär att det finns stöd. Se till att databasen är på Premium-tjänstnivå för att felsöka problemet.


#### <a name="about-the-created-memory-optimized-items"></a>Om skapade minnesoptimerade objekt

**Tabeller**: Exemplet innehåller följande minnesoptimerade tabeller:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Du kan inspektera minnesoptimerade tabeller via den **Object Explorer** i SSMS. Högerklicka på **tabeller** > **Filter** > **filterinställningar** > **är Minnesoptimerad**. Värdet är lika med 1.


Eller du kan fråga katalogvyer, till exempel:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Internt kompilerade lagrade procedurer**: Du kan visa SalesLT.usp_InsertSalesOrder_inmem via en catalog view-fråga:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Kör exemplet OLTP-arbetsbelastning

Den enda skillnaden mellan följande två *lagrade procedurer* är att den första proceduren använder sig av minnesoptimerade versionerna av tabellerna, andra proceduren använder vanliga på disken tabeller:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


I det här avsnittet visas hur du använder den till hands **ostress.exe** verktyg för att köra två lagrade procedurer på stressigt nivåer. Du kan jämföra hur lång tid det tar för de två stress körningarna ska slutföras.


När du kör ostress.exe, rekommenderar vi att du skickar parametervärden som utformats för båda av följande:

- Kör ett stort antal samtidiga anslutningar, genom att använda - n100.
- Har varje anslutning slinga hundratals gånger, av med - r500.


Dock kanske du vill börja med mycket lägre värden som - n10 och -r50 så att allt fungerar.


### <a name="script-for-ostressexe"></a>Skript för ostress.exe


Det här avsnittet visar T-SQL-skript som är inbäddad i vår ostress.exe från kommandoraden. Skriptet använder objekt som har skapats av T-SQL-skriptet som du har installerat tidigare.


Följande skript infogar ett exempel på försäljningsorder med fem radobjekt i följande minnesoptimerade *tabeller*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
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


Att göra den *_ondisk* version av det föregående T-SQL-skriptet för ostress.exe Ersätt båda förekomsterna av den *_inmem* understräng med *_ondisk*. Dessa ersättningar påverka namnen på tabeller och lagrade procedurer.


### <a name="install-rml-utilities-and-ostress"></a>Installera RML verktyg och `ostress`


Vi rekommenderar att du skulle planerar att köra ostress.exe på en Azure-dator (VM). Du skapar en [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) i samma Azure geografiska region som där AdventureWorksLT-databasen finns. Men du kan köra ostress.exe på din bärbara dator i stället.


På den virtuella datorn eller på det värd som du väljer, installera verktyg för repetitionsattacker Markup Language (RML). Verktygen innehåller ostress.exe.

Mer information finns i:
- Ostress.exe diskussion i [exempeldatabasen för In-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Exempel på databasen för In-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- Den [blogg för att installera ostress.exe](https://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Kör den *_inmem* först vara påfrestande för arbetsbelastning


Du kan använda en *RML Cmd-Prompt* fönster för att köra vår ostress.exe kommandorad. Kommandoradsparametrar direct `ostress` till:

- Kör 100 anslutningar samtidigt (-n100).
- Har varje anslutning och köra T-SQL-skriptet 50 gånger (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Så här kör kommandoraden i föregående ostress.exe:


1. Återställa data databasinnehåll genom att köra följande kommando i SSMS, för att ta bort alla data som infogats av alla tidigare körningar:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopiera texten i föregående ostress.exe kommandoraden till Urklipp.

3. Ersätt den `<placeholders>` för de parametrarna -S - U -P -d med korrekta värden för verkliga.

4. Kör redigerade kommandoraden i en RML kommandofönstret.


#### <a name="result-is-a-duration"></a>Resultatet är en varaktighet


När `ostress.exe` har slutförts skriver varaktighet för körning som den sista raden i utdata i RML Cmd-fönstret. Till exempel varat en kortare testkörning ungefär 1,5 minuter:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Återställ, redigera för *_ondisk*, kör sedan


När du har resultatet från den *_inmem* kör, utför följande steg för den *_ondisk* kör:


1. Återställ databasen genom att köra följande kommando i SSMS för att ta bort alla data som infogats av den tidigare körningen:
```
EXECUTE Demo.usp_DemoReset;
```

2. Redigera ostress.exe kommandoraden för att byta ut all *_inmem* med *_ondisk*.

3. Kör ostress.exe för den andra gången och samla in resultatet varaktighet.

4. Igen, återställa databasen (för ansvarsfullt tar du bort det kan vara en stor mängd testdata).


#### <a name="expected-comparison-results"></a>Förväntade Jämförelseresultat

Vår minnesinterna tester har visat som prestanda förbättras av **nio gånger** för den här förenklad arbetsbelastning med `ostress` som körs på en virtuell Azure-dator i samma Azure-region som databasen.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installera minnesinterna Analytics-exempel


I det här avsnittet jämför resultaten i/o och statistik när du använder ett columnstore-index jämfört med traditionella b-trädindex.


För analys i realtid på en OLTP-arbetsbelastning är det ofta bäst att använda ett icke-grupperat columnstore-index. Mer information finns i [Columnstore-index beskrivs](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Förbereda columnstore analytics testet


1. Använd Azure-portalen för att skapa en ny AdventureWorksLT-databasen från exemplet.
 - Använda det exakta namnet.
 - Välj alla Premium-tjänstnivån.

2. Kopiera den [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) till Urklipp.
 - T-SQL-skript skapar nödvändiga InMemory-objekt i AdventureWorksLT-exempeldatabasen som du skapade i steg 1.
 - Skriptet skapar dimensionstabellen och två faktatabeller. Faktatabellerna fylls med 3,5 miljoner rader.
 - Skriptet kan ta 15 minuter att slutföra.

3. Klistra in T-SQL-skript i SSMS och sedan köra skriptet. Den **COLUMNSTORE** nyckelord i den **CREATE INDEX** instruktionen är avgörande, som i:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ställ in AdventureWorksLT på kompatibilitetsnivå 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Nivå 130 är inte direkt relaterade till InMemory-funktioner. Men nivå 130 har vanligtvis bättre frågeprestanda än 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Viktiga tabeller och columnstore-index


- dbo. FactResellerSalesXL_CCI är en tabell som har ett grupperat columnstore-index som har avancerade komprimering vid den *data* nivå.

- dbo. FactResellerSalesXL_PageCompressed är en tabell som har en motsvarande regelbundna grupperat index som komprimeras bara den *sidan* nivå.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Viktiga frågor att jämföra columnstore-index


Det finns [flera frågetyper i T-SQL som du kan köra](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) att prestanda kan förbättras. I steg 2 i T-SQL-skriptet uppmärksam på den här par frågor. De skiljer sig åt endast på en rad:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Ett grupperat columnstore-index är i FactResellerSalesXL\_CCI tabell.

I följande T-SQL-skript utdrag skriver ut statistik för i/o och tid för frågan i varje tabell.


```
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

Du kan förvänta dig ungefär nio gånger prestandaökning för den här frågan med hjälp av grupperade columnstore-indexet jämfört med traditionella index i en databas med P2 prisnivån. Du kan förvänta dig ungefär 57 gånger prestandaökning med P15, med hjälp av columnstore-indexet.



## <a name="next-steps"></a>Nästa steg

- [Snabbstart 1: InMemory-OLTP-tekniker för snabbare prestanda för T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Använda Minnesintern OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

- [Övervaka Minnesintern OLTP-lagring](sql-database-in-memory-oltp-monitoring.md) för In-Memory OLTP


## <a name="additional-resources"></a>Ytterligare resurser

#### <a name="deeper-information"></a>Mer detaljerad information

- [Lär dig hur kvorum fördubblar viktiga databasarbetsbelastningen samtidigt som du minskar DTU med 70% med In-Memory OLTP i SQL-databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Minnesintern OLTP i Azure SQL Database-blogginlägget](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Lär dig mer om Minnesintern OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [Lär dig mer om columnstore-index](https://msdn.microsoft.com/library/gg492088.aspx)

- [Lär dig mer om operativa analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

- Se [vanliga mönster för arbetsbelastningen och överväganden vid migrering](https://msdn.microsoft.com/library/dn673538.aspx) (som beskriver arbetsbelastningmönster där In-Memory OLTP ofta ger betydande prestandavinster)

#### <a name="application-design"></a>Programmets design

- [Minnesintern OLTP (minnesoptimering)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Använda Minnesintern OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Verktyg

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
