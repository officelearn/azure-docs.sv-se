---
title: Metodtips för datainläsning för Synapse SQL-pool
description: Rekommendationer och prestandaoptimeringar för inläsning av data med Synapse SQL-pool.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e170a789727fb0de36705895245cc638d30ee3d7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745506"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Metodtips för inläsning av data med Synapse SQL-pool

I den här artikeln får du lära dig rekommendationer och prestandaoptimeringar för inläsning av data med HJÄLP AV SQL-pool.

## <a name="preparing-data-in-azure-storage"></a>Förbereda data i Azure Storage

För att minimera svarstiden samlokalisera lagringslagret och SQL-poolen.

När du exporterar data till ett ORC-filformat kan du råka ut för ”slut på minne”-fel i Java när det finns kolumner med mycket text. Du kan undvika denna begränsning genom att bara exportera en del av kolumnerna.

PolyBase kan inte läsa in rader som har mer än 1 000 000 byte data. När du placerar data i textfiler i Azure Blob Storage eller Azure Data Lake Store måste dessa data vara mindre än 1 000 000 byte. Den här begränsningen av byte gäller oavsett tabellschemat.

Alla filformat har olika prestandaegenskaper. Den snabbaste inläsningen får du om du använder komprimerade avgränsade textfiler. Skillnaden i prestanda mellan UTF-8 och UTF-16 är minimal.

Dela upp stora komprimerade filer i små komprimerade filer.

## <a name="running-loads-with-enough-compute"></a>Köra belastningar med tillräckligt med beräkning

För högsta hastighet för inläsning, kör du bara ett inläsningsjobb i taget. Om det inte är möjligt, kör ett minimalt antal laster samtidigt. Om du förväntar dig ett stort inläsningsjobb kan du överväga att skala upp SQL-poolen före inläsningen.

För att köra inläsningar med lämpliga beräkningsresurser skapar du inläsningsanvändare som är avsedda att köra inläsningar. Tilldela varje inläsningsanvändare till en viss resursklass eller arbetsbelastningsgrupp. Om du vill köra en inläsning loggar du in som en av de inläsningsanvändare och kör sedan belastningen. Inläsningen körs med användarens resursklass.  

> [!NOTE]
> Den här metoden är enklare än att försöka ändra en användares resursklass så att den passar det aktuella behovet av resursklass.

### <a name="example-of-creating-a-loading-user"></a>Exempel på att skapa en inläsningsanvändare

I det här exemplet skapas en inläsningsanvändare för resursklassen staticrc20. Det första steget är att **ansluta till huvudservern** och skapa en inloggning.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Anslut till SQL-poolen och skapa en användare. Följande kod förutsätter att du är ansluten till databasen som kallas mySampleDataWarehouse. Den visar hur du skapar en användare som heter LoaderRC20 och ger användaren behörighet för en databas. Sedan läggs användaren till som medlem i rollen staticrc20-databas.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Om du vill köra en belastning med resurser för de statiskaRC20-resursklasserna loggar du in som LoaderRC20 och kör belastningen.

Kör inläsningar under statiska i stället för dynamiska resursklasser. Om du använder de statiska resursklasserna garanteras samma resurser oavsett [dina informationslagerenheter.](what-is-a-data-warehouse-unit-dwu-cdwu.md) Om du använder en dynamisk resursklass varierar resurserna beroende på din servicenivå.

För dynamiska klasser innebär en lägre servicenivå att du troligtvis behöver använda en större resursklass för din inläsningsanvändare.

## <a name="allowing-multiple-users-to-load"></a>Tillåta många användare att läsa in

Det finns ofta ett behov av att ha flera användare ladda data i en SQL-pool. Inläsning med [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kräver kontrollbehörigheter för databasen.  CONTROL-behörigheten ger kontrollbehörighet till alla scheman.

Du kanske inte vill att alla användare som läser in ska ha behörighet för alla scheman. Om du vill begränsa behörigheten använder du DENY CONTROL-instruktionen.

Anta att du har följande databasscheman: schema_A för avdelning A och schema_B för avdelning B. Då låter du användare_A och användare_B vara användare för PolyBase-inläsning i avdelning A respektive avdelning B. Båda har beviljats kontrollbehörigheter till databasen. De som skapat schema_A och B låser nu deras scheman med DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A och user_B är nu utelåst från den andra avd schema.

## <a name="loading-to-a-staging-table"></a>Inläsning i en mellanlagringstabell

Om du vill uppnå den snabbaste inläsningshastigheten för att flytta data till en SQL-biljardtabell läser du in data i en mellanlagringstabell.  Definiera mellanlagringstabellen som en heap och använd resursallokering som distributionsalternativ.

Tänk på att inläsning vanligtvis är en tvåstegsprocess där du först läser in till en mellanlagringstabell och sedan infogar data i en produktions-SQL-pooltabell. Om produktionstabellen använder en hash-distribution, kan den totala tiden för att läsa in och infoga bli snabbare om du definierar mellanlagringstabellen i hash-distributionen.

Inläsning till mellanlagringstabellen tar längre tid, men det andra steget i att infoga rader i produktionstabellen skapar inte dataförflyttning över distributioner.

## <a name="loading-to-a-columnstore-index"></a>Inläsning till ett columnstore-index

Kolumnlagringsindex kräver en stor mängd minne för att komprimera data i högkvalitativa radgrupper. För bästa komprimerings- och indexeffektivitet behöver kolumnlagringsindexet komprimera högst 1 048 576 rader till varje radgrupp.

Vid brist på minne kanske kolumnlagringsindexet inte kan uppnå den maximala komprimeringsgraden. Det här scenariot påverkar i sin tur frågeprestanda. Mer detaljer finns i [Minnesoptimering för kolumnlagring](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- För att säkerställa att inläsningsanvändaren har tillräckligt med minne för att uppnå maximal komprimeringsgrad ska du använda inläsningsanvändare som är medlemmar i en mellanstor eller stor resursklass.
- Läs in tillräckligt med rader för att helt fylla de nya radgrupperna. Under en massinläsning komprimeras var 1 048 576:e rad direkt till columnstore som en fullständig radgrupp. Belastningar med färre än 102 400 rader skickar raderna till deltastore där raderna förvaras i ett b-trädindex.

> [!NOTE]
> Om du läser in för få rader kan alla dirigeras till deltastore och inte komprimeras direkt till columnstore-format.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Öka batchstorleken när du använder SqLBulkCopy API eller bcp

Inläsning med PolyBase ger det högsta dataflödet med SQL-pool. Om du inte kan använda PolyBase för att läsa in och måste använda [SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)bör du överväga att öka batchstorleken för bättre dataflöde.

> [!TIP]
> En batchstorlek mellan 100 K till 1 M rader är den rekommenderade baslinjen för att bestämma optimal batchstorlek kapacitet.

## <a name="handling-loading-failures"></a>Hantera inläsningsfel

Vid en inläsning med en extern tabell kan ett felmeddelande som ser ut ungefär så här visas: *"Frågan avbröts. Det högsta tröskelvärdet för avslag nåddes vid inläsning från en extern källa"*. Detta meddelande anger att dina externa data innehåller ändrade poster.

En datapost anses vara smutsig om den uppfyller något av följande villkor:

- Datatyperna och antalet kolumner matchar inte kolumndefinitionerna för den externa tabellen.
- Data överensstämmer inte med det angivna externa filformatet.

Du kan åtgärda de ändrade posterna genom att se till att definitionerna för den externa tabellen och det externa filformatet är korrekta och att dina externa data följer dessa definitioner.

Om en delmängd av externa dataposter är smutsiga kan du välja att avvisa dessa poster för dina frågor genom att använda avvisningsalternativen i [SKAPA EXTERN TABELL (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="inserting-data-into-a-production-table"></a>Infoga data i en produktionstabell

En engångsinläsning i en liten tabell med en [INSERT-instruktion](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) eller till och med en regelbunden inläsning på nytt av en sökning kan fungera tillräckligt bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Enskilda infogningar är emellertid inte lika effektiva som en massinläsning.

Om du har tusentals eller fler enskilda infogningar under dagen bör du gruppera dem så att du kan infoga dem med en massinläsning.  Utveckla dina processer så att enskilda infogningar bifogas i en fil och skapa sedan en annan process som regelbundet läser in filen.

## <a name="creating-statistics-after-the-load"></a>Skapa statistik efter inläsningen

För att få bättre frågeprestanda är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller efter betydande dataändringar. Skapa statistik kan göras manuellt eller så kan du aktivera [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

En detaljerad förklaring av statistik finns i [Statistik](sql-data-warehouse-tables-statistics.md). I följande exempel visas hur du manuellt skapar statistik för fem kolumner i Customer_Speed tabellen.

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

För varje lagringskonto vars nyckel har ändrats utfärdar du [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Exempel:

Den ursprungliga nyckeln skapas

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotera nyckel från nyckel 1 till nyckel 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Det behövs inga andra ändringar i underliggande externa datakällor.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om PolyBase och hur du utformar en ELT-process (extrahering, inläsning och transformering) kan du läsa [Designa ELT för SQL Data Warehouse](design-elt-data-loading.md).
- En kurs i inläsning av data hittar du i [Använda PolyBase för att läsa in data från Azure Blob Storage till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).
- Om du vill övervaka datainläsningen läser du [Övervaka arbetsbelastningen med datahanteringsvyer](sql-data-warehouse-manage-monitor.md).
