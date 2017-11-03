---
title: "Guide för att använda PolyBase i SQL Data Warehouse | Microsoft Docs"
description: "Riktlinjer och rekommendationer för att använda PolyBase i SQL Data Warehouse-scenarier."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.openlocfilehash: e8ae0eb96200c167a8758df4ce20b51452cc59a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guide för att använda PolyBase i SQL Data Warehouse
Den här handboken innehåller praktisk information för att använda PolyBase i SQL Data Warehouse.

Om du vill komma igång finns det [Läs in data med PolyBase] [ Load data with PolyBase] kursen.

## <a name="rotating-storage-keys"></a>Rotera lagringsnycklar
Då kommer du vill ändra åtkomstnyckeln för till blob-lagring av säkerhetsskäl.

Det mest elegant sättet att utföra den här uppgiften är att följa en process som kallas ”rotera nycklarna”. Kanske såg du att du har två lagringsnycklar för blob storage-konto. Detta är så att du kan överföra

Rotera dina nycklar för Azure storage-konto är tre enkla steg

1. Skapa andra omfång databasautentiseringsuppgiften baserat på den sekundära lagringsåtkomstnyckel
2. Skapa extern datakälla baserat på den här nya autentiseringsuppgifter
3. Ta bort och skapa den externa tabeller som pekar på den externa datakällan

När du har migrerat alla externa tabeller till den externa datakällan och sedan kan du utföra rensningen uppgifter:

1. Släpp första extern datakälla
2. Släpp första databas-omfattande autentisering baserat på den primära lagringsåtkomstnyckel
3. Logga in på Azure och återskapa den primära åtkomstnyckeln som är redo för nästa gång



## <a name="load-data-with-external-tables"></a>Läs in data med externa tabeller
Det här exemplet läser data från Azure blob storage till SQL Data Warehouse-databas.

Lagra data direkt tar bort dataöverföringstid för frågor. Lagra data med ett columnstore-index förbättrar prestanda för analys av upp till 10 x.

Det här exemplet använder CREATE TABLE AS SELECT-instruktion för att läsa in data. Den nya tabellen ärver kolumnerna som namnges i frågan. Datatyperna för kolumnerna ärver från den externa tabelldefinitionen.

CREATE TABLE AS SELECT är en hög performant Transact-SQL-uttryck som läser in data i parallellt datornoderna för ditt SQL Data Warehouse.  Den ursprungligen utvecklades för massivt parallell bearbetning (MPP) motorn i Analytics Platform System och är nu i SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Se [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

> [!NOTE]
> En belastning som använder en extern tabell kan misslyckas med fel *”frågan avbröts--avvisa den maximala tröskelvärdet nåddes vid läsning från en extern källa”*. Detta anger att externa data innehåller *felaktig* poster. ”Felaktig' anses vara en post om de faktiska data typer/antalet kolumner inte matchar kolumndefinitioner för extern tabell eller om data inte överensstämmer med det angivna externa filformatet. Kontrollera att din extern tabell och definitioner för externa filformat är korrekta och externa data överensstämmer med dessa definitioner för att åtgärda detta. Om en delmängd av externa dataposter är inaktuella kan du avvisa posterna för dina frågor genom att använda avvisa i Skapa extern tabell DDL.
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>Skapa statistik på nyinlästa data
SQL Data Warehouse stöder inte än automatiskt skapande eller uppdatering av statistik.  För att få bästa möjliga prestanda från dina frågor, är det viktigt att statistik skapas på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar.  En detaljerad förklaring av statistik finns i ämnet [Statistik][Statistics] i ämnesgruppen Utveckla.  Nedan visas ett enkelt exempel på hur du skapar statistik på tabellerna som lästs in i det här exemplet.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>Exportera data med externa tabeller
Det här avsnittet visar hur du exporterar data från SQL Data Warehouse till Azure blob storage med hjälp av externa tabeller. Det här exemplet använder skapa externa TABLE AS SELECT som är en hög performant Transact-SQL-instruktionen för att exportera data parallellt från compute-noder.

I följande exempel skapas en extern tabell Weblogs2014 med kolumndefinitionerna och data från dbo. Webbloggar tabell. Den externa tabelldefinitionen lagras i SQL Data Warehouse och resultatet av SELECT-instruktionen exporteras till katalogen ”/ Arkivera/log2014 /” i blob-behållaren som anges av datakällan. Data exporteras i formatet angiven text.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Isolera läsa in användare
Det är ofta behöver flera användare som kan läsa in data i en SQL DW. Eftersom den [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] kräver behörighet på databasen, du kommer att få flera användare med åtkomst kontroll över alla scheman. Du kan använda instruktionen NEKA kontroll för att begränsa detta.

Exempel: Överväg att databasen scheman schema_A för Avd A och schema_B för Avd B låt databasen användare user_A och user_B vara användare för PolyBase läser in i Avd A och B, respektive. De båda ha beviljats behörighet för databasen.
Skapare av A och B nu schemalås ned sina scheman med NEKA:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Med den här, bör user_A och user_B nu utelåst från andra Avd schema.
 
## <a name="polybase-performance-optimizations"></a>PolyBase prestandaoptimering
För att uppnå optimal prestanda med PolyBase inläsning föreslår vi följande:
- Dela upp stora komprimerade filer i mindre komprimerade filer. Komprimeringstyper idag är inte delbara. Prestanda påverkas genom att läsa in en enda stor fil.
- Läs in i en round_robin heap mellanlagringstabell för snabbaste inläsning hastighet. Det här är det effektivaste sättet att flytta data från lagringsskikt till datalagret.
- Alla filformat har olika prestandaegenskaper. För den snabbaste avgränsade Använd komprimeras textfiler. Skillnaden mellan UTF-8 och UTF-16-prestanda är minimal.
- Samplacera dina lagringsskikt och datalagret för att minimera fördröjning
- Skala upp ditt informationslager om du räknar med att en stor inläsning jobb.

## <a name="polybase-limitations"></a>PolyBase-begränsningar
PolyBase i SQL DW har följande begränsningar som måste beaktas när du skapar ett jobb för inläsning:
- En enda rad får inte vara större än 1 000 000 byte. Detta gäller oavsett tabellschemat definierats.
- När data exporteras till en ORC-filformat från SQL Server eller Azure SQL Data Warehouse tunga textkolumner begränsas till så lite som 50 kolumner på grund av java slut på minne. Undvik detta genom att exportera en delmängd av kolumnerna.





## <a name="next-steps"></a>Nästa steg
Mer information om hur du flyttar data till SQL Data Warehouse finns i [översikt över datamigrering][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
