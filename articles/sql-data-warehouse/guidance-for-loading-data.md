---
title: Metodtips för inläsning av data – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och prestandaoptimering för inläsning av data i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 48b0f0300ab563e8388c9e99f4f90cd24c56678d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Metodtips för inläsning av data i Azure SQL Data Warehouse
Rekommendationer och prestandaoptimering för inläsning av data i Azure SQL Data Warehouse. 

- Om du vill veta mer om PolyBase och hur du utformar en ELT-process (extrahering, inläsning och transformering) kan du läsa [Designa ELT för SQL Data Warehouse](design-elt-data-loading.md).
- En kurs i inläsning av data hittar du i [Använda PolyBase för att läsa in data från Azure Blob Storage till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="preparing-data-in-azure-storage"></a>Förbereda data i Azure Storage
Minska svarstiden genom att samplacera ditt lagringsskikt och datalager.

När du exporterar data till ett ORC-filformat kan du råka ut för ”slut på minne”-fel i Java när det finns kolumner med mycket text. Du kan undvika denna begränsning genom att bara exportera en del av kolumnerna.

PolyBase kan inte läsa in rader med data som överstiger 1 000 000 byte. När du placerar data i textfiler i Azure Blob Storage eller Azure Data Lake Store måste dessa data vara mindre än 1 000 000 byte. Den här begränsningen av byte gäller oavsett tabellschemat.

Alla filformat har olika prestandaegenskaper. Den snabbaste inläsningen får du om du använder komprimerade avgränsade textfiler. Skillnaden i prestanda mellan UTF-8 och UTF-16 är minimal.

Dela upp stora komprimerade filer i små komprimerade filer.

## <a name="running-loads-with-enough-compute"></a>Köra belastningar med tillräckligt med beräkning

För högsta hastighet för inläsning, kör du bara ett inläsningsjobb i taget. Om detta inte är möjligt, kör du ett minimalt antal belastningar samtidigt. Överväg att skala upp ditt informationslager innan belastningen om du förväntar dig ett stort inläsningsjobb.

För att köra inläsningar med lämpliga beräkningsresurser skapar du inläsningsanvändare som är avsedda att köra inläsningar. Tilldela varje inläsningsanvändare till en specifik resursklass. När du kör en inläsning loggar du in som en av inläsningsanvändarna och kör sedan inläsningen. Inläsningen körs med användarens resursklass.  Den här metoden är enklare än att försöka ändra en användares resursklass så att den passar det aktuella behovet av resursklass.

### <a name="example-of-creating-a-loading-user"></a>Exempel på att skapa en inläsningsanvändare
I det här exemplet skapas en inläsningsanvändare för resursklassen staticrc20. Det första steget är att **ansluta till huvudservern** och skapa en inloggning.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```
Anslut till informationslagret och skapa en användare. Följande kod förutsätter att du är ansluten till databasen mySampleDataWarehouse. Det visar hur du skapar en användare med namnet LoaderRC20 och ger användare kontrollbehörighet på en databas. Det lägger sedan till användaren som en medlem i staticrc20-databasrollen.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```
När du vill köra en inläsning med resurser för staticRC20-resursklasserna loggar du bara in som LoaderRC20 och kör inläsningen.

Kör inläsningar under statiska i stället för dynamiska resursklasser. Med hjälp av statiska resursklasser garanterar samma resurser oberoende av din [datalager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md). Om du använder en dynamisk resursklass varierar resurserna beroende på din servicenivå. För dynamiska klasser innebär en lägre servicenivå att du troligtvis behöver använda en större resursklass för din inläsningsanvändare.

## <a name="allowing-multiple-users-to-load"></a>Tillåta många användare att läsa in

Det finns ofta ett behov av att ha flera användare som kan läsa in data i informationslagret. Läser in med den [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) kräver behörighet på databasen.  CONTROL-behörigheten ger kontrollbehörighet till alla scheman. Du kanske inte vill att alla användare som läser in ska ha behörighet för alla scheman. Om du vill begränsa behörigheten använder du DENY CONTROL-instruktionen.

Anta att du har följande databasscheman: schema_A för avdelning A och schema_B för avdelning B. Då låter du användare_A och användare_B vara användare för PolyBase-inläsning i avdelning A respektive avdelning B. Båda har beviljats kontrollbehörigheter till databasen. De som skapat schema_A och B låser nu deras scheman med DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

User_A och user_B är nu utelåsta från den andra avdelningens schema.


## <a name="loading-to-a-staging-table"></a>Inläsning i en mellanlagringstabell

För att uppnå högsta inläsningshastighet vid flytt av data till en informationslagertabell, läs in data i en mellanlagringstabellen.  Definiera mellanlagringstabellen som en heap och använd resursallokering som distributionsalternativ. 

Överväg att inläsning vanligtvis är en tvåstegsprocess där du först läser in till en mellanlagringstabellen och sedan infogar data i en informationslagertabell för produktion. Om produktionstabellen använder en hash-distribution, kan den totala tiden för att läsa in och infoga bli snabbare om du definierar mellanlagringstabellen i hash-distributionen. Inläsning till mellanlagringstabellen tar längre tid, men det andra steget i att infoga rader i produktionstabellen skapar inte dataförflyttning över distributioner.

## <a name="loading-to-a-columnstore-index"></a>Inläsning till ett columnstore-index

Kolumnlagringsindex kräver en stor mängd minne för att komprimera data i högkvalitativa radgrupper. För bästa komprimerings- och indexeffektivitet behöver kolumnlagringsindexet komprimera högst 1 048 576 rader till varje radgrupp. Vid brist på minne kanske kolumnlagringsindexet inte kan uppnå den maximala komprimeringsgraden. Detta påverkar i sin tur frågeprestanda. Mer detaljer finns i [Minnesoptimering för kolumnlagring](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- För att säkerställa att inläsningsanvändaren har tillräckligt med minne för att uppnå maximal komprimeringsgrad ska du använda inläsningsanvändare som är medlemmar i en mellanstor eller stor resursklass. 
- Läs in tillräckligt med rader för att helt fylla de nya radgrupperna. Under en massinläsning komprimeras var 1 048 576:e rad direkt till columnstore som en fullständig radgrupp. Belastningar med färre än 102 400 rader skickar raderna till deltastore där raderna förvaras i ett b-trädindex. Om du läser in för få rader kan alla rader hamna i deltalagringen och inte bli komprimerade direkt i kolumnlagringsformatet.


## <a name="handling-loading-failures"></a>Hantera inläsningsfel

Vid en inläsning med en extern tabell kan ett felmeddelande som ser ut ungefär så här visas: *"Frågan avbröts. Det högsta tröskelvärdet för avslag nåddes vid inläsning från en extern källa"*. Detta meddelande anger att dina externa data innehåller ändrade poster. En datapost anses vara ändrade om datatyperna och antalet kolumner inte matchar kolumndefinitionen för den externa tabellen eller om data inte följer det angivna externa filformatet. 

Du kan åtgärda de ändrade posterna genom att se till att definitionerna för den externa tabellen och det externa filformatet är korrekta och att dina externa data följer dessa definitioner. Om en delmängd av de externa dataposterna är felaktiga kan du välja att avvisa dessa poster för dina frågor genom att använda avvisningsalternativen i CREATE EXTERNAL TABLE.

## <a name="inserting-data-into-a-production-table"></a>Infoga data i en produktionstabell
En engångsinläsning i en liten tabell med en [INSERT-instruktion](/sql/t-sql/statements/insert-transact-sql) eller till och med en regelbunden inläsning på nytt av en sökning kan fungera tillräckligt bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Enskilda infogningar är emellertid inte lika effektiva som en massinläsning. 

Om du har tusentals eller fler enskilda infogningar under dagen bör du gruppera dem så att du kan infoga dem med en massinläsning.  Utveckla dina processer så att enskilda infogningar bifogas i en fil och skapa sedan en annan process som regelbundet läser in filen.

## <a name="creating-statistics-after-the-load"></a>Skapa statistik efter inläsningen

För att få bättre frågeprestanda är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller efter betydande dataändringar.  En detaljerad förklaring av statistik finns i [Statistik](sql-data-warehouse-tables-statistics.md). Följande exempel skapar statistik på fem kolumner i tabellen Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Rotera lagringsnycklar
Det är en bra säkerhetsrutin att regelbundet ändra åtkomstnyckeln till din Blob Storage. Du har två lagringsnycklar för ditt blob storage-konto, som gör det möjligt att överföra nycklarna.

Så här roterar du Azure Storage-kontonycklar:

För varje lagringskonto vars nyckel har ändrats utfärdar du [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql).

Exempel:

Den ursprungliga nyckeln skapas

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
    ``` 

Rotera nyckel från nyckel 1 till nyckel 2

    ```sq;
    ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2' 
    ```

Det behövs inga andra ändringar i underliggande externa datakällor.


## <a name="next-steps"></a>Nästa steg
Om du vill övervaka datainläsningen läser du [Övervaka arbetsbelastningen med datahanteringsvyer](sql-data-warehouse-manage-monitor.md).



