---
title: Azure SQL Database i minnet tekniker | Microsoft Docs
description: Azure SQL Database i minnet tekniker förbättra prestanda för transaktionell och analytics arbetsbelastningar.
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 04/04/2018
ms.author: jodebrui
ms.openlocfilehash: 36a6b32851c4778db3405b6b9b35d9551181abf4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimera prestanda genom att använda InMemory-tekniker i SQL-databas

Du kan åstadkomma prestandaförbättringar med olika arbetsbelastningar med hjälp av InMemory-tekniker i Azure SQL Database: transaktionell (online transaktionsbearbetning (OLTP)), analytics (online analytical processing (OLAP)), och blandade (hybrid transaktion analytical processing (HTAP)). På grund av effektivare fråga och transaktionsbearbetning hjälpa InMemory-tekniker också dig att minska kostnaden. Vanligtvis behöver du inte uppgradera prisnivån för databasen för att uppnå prestandavinster. I vissa fall kan du även eventuellt minska prisnivån samtidigt ändå prestandaförbättringar med InMemory-teknik.

Här är två exempel på hur Minnesintern OLTP hjälpt att avsevärt förbättra prestanda:

- Med hjälp av Minnesintern OLTP [kvorum affärslösningar kunde fördubbla arbetsbelastningen samtidigt förbättra dtu: er med 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU innebär *database transaction unit*, och innehåller en mesurement resursförbrukning.
- Följande videoklipp visar viktig förbättringar i resursanvändningen med ett exempel på arbetsbelastning: [Minnesintern OLTP i Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Mer information finns i blogginlägget: [Minnesintern OLTP i Azure SQL Database-blogginlägg](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

InMemory-teknik finns i alla databaser i Premium-nivån, inklusive databaser i Premium elastiska pooler.

Följande videoklipp beskrivs möjliga prestandafördelar med InMemory-tekniker i Azure SQL Database. Kom ihåg att prestanda får du alltid ser beror på många faktorer, inklusive typ av arbetsbelastning och data, åtkomstmönster av databasen, och så vidare.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Azure SQL Database har följande InMemory-tekniker:

- *Minnesintern OLTP* ökar transaktion och minskar svarstiden för transaktionsbearbetning. Scenarier som har nytta i minnet OLTP är: hög genomströmning transaktionsbearbetning, till exempel handel och spel, datapåfyllning från händelser eller IoT-enheter, cachelagring datainläsning, och tillfällig tabell och tabellen variabeln scenarier.
- *Grupperade columnstore-index* minska lagring-storleken (upp till 10 gånger) och förbättra prestanda för frågor för rapportering och analys. Du kan använda den med faktatabeller i-dataarkiv att få mer data i databasen och förbättra prestanda. Du kan också använda den med historiska data i databasen att arkivera och kunna läsa upp till 10 gånger mer data.
- *Icke-grupperat columnstore-index* för HTAP hjälper dig att få realtid insikter om ditt företag genom att fråga databasen direkt, utan att behöva köra en dyr extrahera transformera, och läsa in (ETL)-processen och vänta tills den datalager fyllas. Icke-grupperat columnstore-index fjärrkörning mycket snabbt analytics frågor på OLTP-databasen samtidigt minska påverkan på operativa arbetsbelastningen.
- Du kan också ha en kombination av en minnesoptimerad tabell med ett columnstore-index. Den här kombinationen kan du utföra mycket snabbt transaktionsbearbetning och *samtidigt* köra analytics frågor snabbt på samma data.

Både columnstore-index och Minnesintern OLTP har varit en del av SQL Server-produkt sedan 2012 och 2014, respektive. Azure SQL Database och SQL Server delar samma implementering av InMemory-tekniker. Framöver, släpps nya funktioner för dessa tekniker i Azure SQL Database först innan de blir tillgängliga i SQL Server.

Det här avsnittet beskrivs aspekter av Minnesintern OLTP och columnstore-index som är specifika för Azure SQL Database och även exempel:
- Effekten av dessa tekniker visas på storleksbegränsningar för lagring och data.
- Du lär dig att hantera flytt av databaser som använder dessa tekniker mellan olika prisnivåer.
- Ser du två exempel som visar hur du använder i minnet OLTP samt columnstore-index i Azure SQL Database.

Se följande resurser för mer information.

Detaljerad information om tekniker:

- [Minnesintern OLTP översikt och Användningsscenarier](https://msdn.microsoft.com/library/mt774593.aspx) (innehåller referenser till kundfallstudier och information för att komma igång)
- [Dokumentation för OLTP i minnet](http://msdn.microsoft.com/library/dn133186.aspx)
- [Guide för Columnstore-index](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrid transaktionella/analytisk behandling (HTAP), även kallat [operativa analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

En kort introduktion på Minnesintern OLTP: [snabb Start 1: I minnet OLTP-teknik för snabbare prestanda för T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) (en annan artikel som hjälper dig att komma igång)

Djupgående video om tekniker:

- [Minnesintern OLTP i Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (som innehåller en demonstration av prestandafördelarna och steg för att återskapa de här resultaten returneras själv)
- [Minnesintern OLTP video: Vad det är och när/hur du använder den](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Kolumnlagringsindexet: I minnet Analytics videor från Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Lagrings- och storlek

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Storlek och lagring cap för OLTP i minnet

Minnesintern OLTP innehåller minnesoptimerade tabeller som används för lagring av användardata. Dessa tabeller krävs för att få plats i minnet. Eftersom du hantera minne direkt i SQL Database-tjänsten har vi konceptet för en kvot för användardata. Den här idén kallas *Minnesintern OLTP lagring*.

Varje stöds fristående databas prisnivå och varje elastisk pool prisnivån innehåller mängden lagringsutrymme som OLTP i minnet. Se [DTU-baserade gränserna för](sql-database-dtu-resource-limits.md) och [vCore-baserade gränserna för](sql-database-vcore-resource-limits.md).

Följande objekt räknas in i din Minnesintern OLTP lagring cap:

- Aktiva användare datarader i minnesoptimerade tabeller och tabellvariabler. Observera att den gamla raden versioner inte räknas in i fästpunkten.
- Index för minnesoptimerade tabeller.
- Operativ tillsyn ALTER TABLE-åtgärder.

Om du klickar på höljet felmeddelandet out av kvot och du inte längre kunna infoga eller uppdatera data. Ta bort data för att åtgärda felet, eller öka prisnivån för den databas eller poolen.

Mer information om övervakning i minnet OLTP lagringsanvändningen och konfigurera aviseringar när du nästan träffar fästpunkten finns [övervakaren InMemory-lagringen](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Om elastiska pooler

Med elastiska pooler delas Minnesintern OLTP-lagringen mellan alla databaser i poolen. Därför kan användning i en databas potentiellt påverka andra databaser. Det finns två åtgärder för den här:

- Konfigurera en `Max-eDTU` eller `MaxvCore` för databaser som är lägre än antalet eDTU eller vCore för poolen som helhet. Den här högsta värdet versaler i alla databaser i poolen, till samma storlek som motsvarar antalet eDTU i lagringsanvändningen Minnesintern OLTP.
- Konfigurera en `Min-eDTU` eller `MinvCore` som är större än 0. Detta minsta garanterar att varje databas i poolen har mängden tillgängligt lagringsutrymme för OLTP i minnet som motsvarar den konfigurerade `Min-eDTU` eller `vCore`.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Datastorlek och lagring för columnstore-index

Columnstore-index behöver inte ryms i minnet. Därför bara fästpunkten på storleken på index är största övergripande databasens storlek, som beskrivs i den [SQL Database servicenivåer](sql-database-service-tiers.md) artikel.

När du använder grupperade columnstore-index används kolumner komprimering för lagring av bastabellen. Den här komprimeringen kan avsevärt minska storleken för lagring av användardata, vilket innebär att du får plats mer data i databasen. Och komprimeringen kan förlängas med [kolumner arkivering komprimering](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Mängden komprimering som du kan åstadkomma beror på typen av uppgift, men 10 gånger komprimeringen är inte ovanligt.

Om du har en databas med en maximal storlek på 1 terabyte (TB) och du uppnå 10 gånger komprimering med hjälp av columnstore-index kan anpassa du totalt 10 TB användardata i databasen.

När du använder icke-grupperat columnstore-index lagras i bastabellen fortfarande i traditionella rowstore-format. Lagringsbesparingar är därför inte lika stor som med grupperade columnstore-index. Om du ersätter ett antal traditionella icke-grupperat index med en enda columnstore-index, kan du dock fortfarande se en övergripande besparingar i lagringsutrymme storleken för tabellen.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Flytta databaser som använder minnesinterna tekniker mellan prisnivåer

Det finns aldrig alla inkompatibiliteter eller andra problem när du uppgraderar till en högre prisnivå som från Standard till Premium. De tillgängliga funktioner och resurser som bara öka.

Men nedgradera prisnivån kan påverka din databas. Påverkan är särskilt tydligt när du Nedgradera från Premium till Standard eller grundläggande när databasen innehåller Minnesintern OLTP-objekt. Minnesoptimerade tabeller är inte tillgängliga efter nedgraderingen (även om de är synligt). Samma villkor gäller när du sänker prisnivån för en elastisk pool eller flytta en databas med InMemory-tekniker, till en Standard eller grundläggande elastisk pool.

### <a name="in-memory-oltp"></a>Minnesintern OLTP

*Nedgradera Basic-standarden*: Minnesintern OLTP stöds inte i databaser i Standard- eller Basic-nivån. Dessutom kan inte flytta en databas som har alla Minnesintern OLTP-objekt till Standard- eller Basic-nivån.

Det finns en programmässiga sätt att förstå om en viss databas stöder Minnesintern OLTP. Du kan köra följande Transact-SQL-fråga:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Om frågan returnerar **1**, i minnet OLTP stöds i den här databasen.

Ta bort alla minnesoptimerade tabeller och tabelltyper samt alla internt kompilerade moduler för T-SQL innan du nedgradera databasen till Standard/enkel. Följande frågor identifiera alla objekt som måste tas bort innan en databas kan nedgraderas till Standard/enkel:

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Nedgradera till en lägre nivå Premium*: Data i minnesoptimerade tabeller måste rymmas inom Minnesintern OLTP-lagring som är kopplad till prisnivån för databasen eller som är tillgängliga i den elastiska poolen. Om du försöker lägre prisnivån eller flytta databasen till en pool som inte har tillräckligt med lagringsutrymme för OLTP InMemory-åtgärden misslyckas.

### <a name="columnstore-indexes"></a>Columnstore-index

*Nedgradera till Basic eller Standard*: Columnstore-index stöds endast på Premium-prisnivån på standardnivån S3 och senare och inte på den grundläggande nivån. När du nedgradera databasen till en nivå som inte stöds eller nivå otillgänglig columnstore-index. Systemet har columnstore-index, men den använder aldrig indexet. Om du uppgraderar senare tillbaka till en stöds nivån eller en nivå, är columnstore-index klar att användas igen.

Om du har en **klustrade** columnstore-index hela tabellen blir tillgänglig efter nedgraderingen. Därför rekommenderar vi att du ta bort alla *klustrade* columnstore-index innan du nedgradera databasen till en nivå som inte stöds eller nivå.

*Nedgradera till en lägre nivå som stöds eller nivå*: den här nedgradering lyckas om hela databasen ryms inom den maximala databasstorleken för målet prisnivån eller inom tillgängligt lagringsutrymme i den elastiska poolen. Det finns ingen specifik inverkan från columnstore-index.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1 Installera Minnesintern OLTP-exempel

Du kan skapa AdventureWorksLT exempeldatabasen med ett par klick i den [Azure-portalen](https://portal.azure.com/). Sedan beskrivs stegen i det här avsnittet hur du kan utöka AdventureWorksLT databasen med InMemory-OLTP-objekt och visa prestandafördelarna.

En mer simplistic, men mer tilltalande prestanda demo för InMemory-OLTP finns:

- Utgåvan: [i-minne-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Källkod: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Installationssteg

1. I den [Azure-portalen](https://portal.azure.com/), skapa en Premium eller Business kritiska (förhandsgranskning) databas på en server. Ange den **källa** till AdventureWorksLT exempeldatabasen. Detaljerade instruktioner finns [skapa din första Azure SQL-databas](sql-database-get-started-portal.md).

2. Ansluta till databasen med SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Kopiera den [Minnesintern OLTP Transact-SQL-skript](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) till Urklipp. T-SQL-skript skapar nödvändiga InMemory-objekt i-exempeldatabasen AdventureWorksLT som du skapade i steg 1.

4. Klistra in T-SQL-skript i SSMS och sedan köra skriptet. Den `MEMORY_OPTIMIZED = ON` instruktionen CREATE TABLE-satser är avgörande. Exempel:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Fel 40536


Om det uppstår fel 40536 vid körning av T-SQL-skript, kör följande T-SQL-skript för att kontrollera om databasen stöder i minnet:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Ett resultat av **0** innebär att i minnet inte stöds och **1** innebär att stöds. Se till att databasen är på premiumnivån för att diagnosticera problemet.


#### <a name="about-the-created-memory-optimized-items"></a>Om objekt du har skapat minnesoptimerade

**Tabeller**: provet innehåller följande minnesoptimerade tabeller:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Du kan inspektera minnesoptimerade tabeller via den **Object Explorer** i SSMS. Högerklicka på **tabeller** > **Filter** > **filtrera inställningar** > **är Minnesoptimerad**. Värdet är lika med 1.


Eller fråga katalogvyer, exempelvis:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Internt kompilerade lagrade proceduren**: du kan inspektera SalesLT.usp_InsertSalesOrder_inmem via en katalog Visa fråga:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Kör exempel OLTP-arbetsbelastning

Den enda skillnaden mellan de följande två *lagrade procedurer* är att den första proceduren använder minnesoptimerade tabeller-versioner, medan andra proceduren använder vanliga på disken tabeller:

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


I det här avsnittet visas hur du använder den praktiska **ostress.exe** verktyg för att köra två lagrade procedurer på stress nivåer. Du kan jämföra hur lång tid det tar för två stress körs ska slutföras.


När du kör ostress.exe, rekommenderar vi att du skickar parametervärden som utformats för båda av följande:

- Kör ett stort antal samtidiga anslutningar med hjälp av - n100.
- Har varje anslutning slinga hundratals gånger, med hjälp av - r500.


Du kanske vill börja med mycket lägre värden som - n10 och -r50 så att allt fungerar.


### <a name="script-for-ostressexe"></a>Skriptet för ostress.exe


Det här avsnittet visar T-SQL-skript som är inbäddad i vår ostress.exe-kommandoraden. Skriptet använder objekt som har skapats av T-SQL-skript som du tidigare har installerat.


Följande skript infogar ett exempel ordern fem artiklar i följande minnesoptimerade *tabeller*:

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


Att göra den *_ondisk* versionen av föregående T-SQL-skriptet för ostress.exe, ska du ersätta båda förekomster av de *_inmem* substring med *_ondisk*. Dessa ersättningar påverka namnen på de tabeller och lagrade procedurer.


### <a name="install-rml-utilities-and-ostress"></a>Installera RML verktyg och ostress


Vi rekommenderar skulle du planerar att köra ostress.exe på en Azure-dator (VM). Du skapar en [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) i samma Azure geografiska region som där AdventureWorksLT databasen finns. Men du kan köra ostress.exe på din bärbara dator i stället.


På den virtuella datorn eller på det värd du väljer, installera verktyg för repetitionsattacker Markup Language (RML). Verktygen innehåller ostress.exe.

Mer information finns i:
- Ostress.exe diskussion i [exempeldatabasen för InMemory-OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
- [Exempel på databasen för InMemory-OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
- Den [bloggen för att installera ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Kör den *_inmem* betonar arbetsbelastning först


Du kan använda en *RML Cmd-Prompt* fönster för att köra vår ostress.exe kommandorad. Kommandoradsparametrar direkt ostress till:

- Kör 100 anslutningar samtidigt (-n100).
- Varje anslutning och köra T-SQL-skript 50 gånger (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Köra föregående ostress.exe kommandoraden:


1. Återställ databasen datainnehåll genom att köra följande kommando i SSMS att ta bort alla data som infogats av alla tidigare körs:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopiera texten i föregående ostress.exe kommandoraden till Urklipp.

3. Ersätt den `<placeholders>` för de parametrarna -S - U -P -d med rätt verkliga värden.

4. Kör redigerade kommandoraden i ett RML Cmd-fönster.


#### <a name="result-is-a-duration"></a>Resultatet är en varaktighet


När ostress.exe är klar skrivs kör varaktighet som den sista raden i utdata i RML Cmd-fönster. Till exempel varade en kortare testkörning ungefär 1,5 minuter:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Återställ, redigera för *_ondisk*, kör sedan


När du har resultatet från den *_inmem* körs, utför följande steg för den *_ondisk* kör:


1. Återställ databasen genom att köra följande kommando i SSMS att ta bort alla data som infogats av en tidigare körning:
```
EXECUTE Demo.usp_DemoReset;
```

2. Redigera ostress.exe kommandoraden för att byta ut all *_inmem* med *_ondisk*.

3. Kör ostress.exe för den andra gången och samla in resultatet varaktighet.

4. Igen och återställa databasen (för ansvarsfullt om du tar bort kan vara en stor mängd testdata).


#### <a name="expected-comparison-results"></a>Förväntade Jämförelseresultat

Våra tester i minnet har visat att prestanda förbättras av **nio gånger** för den här simplistic arbetsbelastning med ostress som körs på en Azure-dator i samma Azure-region som databas.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2 Installera InMemory-Analytics-exempel


I det här avsnittet jämför resultaten i/o och statistik när du använder ett columnstore-index jämfört med traditionell b-trädindex.


För analys i realtid på en OLTP-arbetsbelastning är det ofta bäst att använda ett icke-grupperat columnstore-index. Mer information finns i [Columnstore-index beskrivs](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Förbereda columnstore analytics test


1. Använda Azure portal för att skapa en ny AdventureWorksLT databas från exemplet.
 - Använda exakt samma namn.
 - Välj alla premiumnivån.

2. Kopiera den [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) till Urklipp.
 - T-SQL-skript skapar nödvändiga InMemory-objekt i-exempeldatabasen AdventureWorksLT som du skapade i steg 1.
 - Skriptet skapar dimensionstabellen och två faktatabeller. Faktatabellerna fylls i med 3.5 miljoner rader.
 - Skriptet kan ta 15 minuter att slutföra.

3. Klistra in T-SQL-skript i SSMS och sedan köra skriptet. Den **COLUMNSTORE** nyckelord i den **CREATE INDEX** -instruktionen är mycket viktigt, som:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ange AdventureWorksLT till kompatibilitetsnivå 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Nivå 130 är inte direkt relaterat till InMemory-funktioner. Men nivå 130 erbjuder bättre frågeprestanda än 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Viktiga tabeller och columnstore-index


- dbo. FactResellerSalesXL_CCI är en tabell som har ett grupperat columnstore-index som har avancerade komprimering vid den *data* nivå.

- dbo. FactResellerSalesXL_PageCompressed är en tabell som har en motsvarande reguljära grupperat index, som komprimeras bara på den *sidan* nivå.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Viktiga frågor för att jämföra columnstore-index


Det finns [flera T-SQL-fråga typer som du kan köra](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) få bättre prestanda. I steg 2 i T-SQL-skript kan du vara uppmärksam på detta par av frågor. De skiljer sig bara på en rad:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Ett grupperat columnstore-index är i FactResellerSalesXL\_CCI tabell.

Följande T-SQL-skript utdrag skrivs ut statistik för i/o och tid för frågan för varje tabell.


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

Du kan förvänta dig om nio gånger prestandafördelar för den här frågan med hjälp av grupperade columnstore-index jämfört med traditionell index i en databas med P2 prisnivå. Du kan förvänta dig om 57 gånger prestandafördelar med P15, med hjälp av columnstore-index.



## <a name="next-steps"></a>Nästa steg

- [Snabbstart 1: Minnesintern OLTP-teknik för snabbare T-SQL-prestanda](http://msdn.microsoft.com/library/mt694156.aspx)

- [Använd InMemory-OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

- [Övervakaren i minnet OLTP lagring](sql-database-in-memory-oltp-monitoring.md) för OLTP i minnet


## <a name="additional-resources"></a>Ytterligare resurser

#### <a name="deeper-information"></a>Mer detaljerad information

- [Lär dig hur kvorum fördubblar viktiga databasen arbetsbelastning och sänka DTU med 70% med InMemory-OLTP i SQL-databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Minnesintern OLTP i Azure SQL Database blogginlägget](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Lär dig mer om OLTP i minnet](http://msdn.microsoft.com/library/dn133186.aspx)

- [Lär dig mer om columnstore-index](https://msdn.microsoft.com/library/gg492088.aspx)

- [Lär dig mer om drift analys i realtid](http://msdn.microsoft.com/library/dn817827.aspx)

- Se [vanliga arbetsbelastning mönster och överväganden vid migrering](http://msdn.microsoft.com/library/dn673538.aspx) (som beskriver arbetsbelastning mönster där Minnesintern OLTP ofta ger betydande prestandavinster)

#### <a name="application-design"></a>Programmet design

- [OLTP (i minnet optimering) i minnet](http://msdn.microsoft.com/library/dn133186.aspx)

- [Använd InMemory-OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Verktyg

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
