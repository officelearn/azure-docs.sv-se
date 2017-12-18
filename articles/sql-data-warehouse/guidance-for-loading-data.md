---
title: "Vägledning för inläsning av data i Azure SQL Data Warehouse | Microsoft Docs"
description: "Rekommendationer om hur du läser in data och utför ELT med Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Vägledning för inläsning av data i Azure SQL Data Warehouse
Rekommendationer och prestandaoptimering för inläsning av data i Azure SQL Data Warehouse. 

- Om du vill veta mer om PolyBase och hur du utformar en ELT-process (extrahering, laddning och transformering) kan du läsa [Designa ELT för SQL Data Warehouse](design-elt-data-loading.md).
- En kurs i inläsning av data hittar du i [Använda PolyBase för att läsa in data från Azure Blob Storage till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="extract-source-data"></a>Extrahera källdata

När du exporterar data till ett ORC-filformat från SQL Server eller Azure SQL Data Warehouse kan kolumner med mycket text vara begränsade till bara 50 kolumner på grund av ”slut på minne”-fel i Java. Du kan undvika detta genom att bara exportera en del av kolumnerna.


## <a name="land-data-to-azure"></a>Placera data i Azure
PolyBase kan inte läsa in rader med data som överstiger en miljon byte. När du placerar data i textfiler i Azure Blob Storage eller Azure Data Lake Store måste dessa data vara mindre än en miljon byte. Det gäller oavsett vilket tabellschema som definierats.

Minska svarstiden genom att samplacera ditt lagringsskikt och datalager.

## <a name="data-preparation"></a>Förberedelse av data

Alla filformat har olika prestandaegenskaper. Den snabbaste inläsningen får du om du använder komprimerade avgränsade textfiler. Skillnaden i prestanda mellan UTF-8 och UTF-16 är minimal.

Dela upp stora komprimerade filer i små komprimerade filer.

## <a name="create-designated-loading-users"></a>Skapa avsedda inläsningsanvändare
För att köra inläsningar med lämpliga beräkningsresurser skapar du inläsningsanvändare som är avsedda att köra inläsningar. Tilldela varje inläsningsanvändare till en specifik resursklass. När du kör en inläsning loggar du in som en av inläsningsanvändarna och kör sedan inläsningen. Inläsningen körs med användarens resursklass.  Den här metoden är enklare än att försöka ändra en användares resursklass så att den passar det aktuella behovet av resursklass.

Den här koden skapar en inläsningsanvändare för resursklassen staticrc20. Den ger användare kontrollbehörighet på en databas och lägger sedan till användaren som en medlem i staticrc20-databasrollen. När du vill köra en inläsning med resurser för staticRC20-resursklasserna loggar du bara in som LoaderRC20 och kör inläsningen. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

Kör inläsningar under statiska i stället för dynamiska resursklasser. Genom att använda statiska resursklasser ser du till att resurserna är desamma oavsett din [servicenivå](performance-tiers.md#service-levels). Om du använder en dynamisk resursklass varierar resurserna beroende på din servicenivå. För dynamiska klasser innebär en lägre servicenivå att du troligtvis behöver använda en större resursklass för din inläsningsanvändare.

### <a name="example-for-isolating-loading-users"></a>Exempel på isolering av inläsningsanvändare

Det finns ofta ett behov av att ha flera användare som kan läsa in data i SQL DW. Eftersom [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] kräver fullständiga behörigheter till databasen kommer till slut flera användare att ha fullständig behörighet till alla scheman. Du kan begränsa detta med hjälp av instruktionen DENY CONTROL.

Anta att du har följande databasscheman: schema_A för avdelning A och schema_B för avdelning B. Då låter du användarna användare_A och användare_B vara användare för PolyBase-inläsning i avdelning A respektive avdelning B. Båda har beviljats fullständiga behörigheter till databasen. De som skapat schema_A och B låser nu deras scheman med DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

User_A och user_B är nu utelåsta från den andra avdelningens schema.


## <a name="load-to-a-staging-table"></a>Inläsning i en mellanlagringstabell

Högsta inläsningshastighet uppnås vid inläsning i en round_robin, heap-mellanlagringstabell. Det är det effektivaste sättet att flytta data från Azure Storage-skiktet till SQL Data Warehouse.

Skala upp ditt datalager om du förväntar dig ett stort inläsningsjobb.

Kör endast ett inläsningsjobb åt gången så att du får optimala inläsningsprestanda

### <a name="optimize-columnstore-index-loads"></a>Optimera kolumnlagringsindexinläsningar

Kolumnlagringsindex kräver mycket minne för att komprimera data i högkvalitativa radgrupper. För bästa komprimerings- och indexeffektivitet behöver kolumnlagringsindexet komprimera 1 048 576 rader till varje radgrupp. Detta är det maximala antalet rader per radgrupp. Vid brist på minne kanske kolumnlagringsindexet inte kan uppnå den maximala komprimeringsgraden. Detta påverkar i sin tur frågeprestanda. Mer detaljer finns i [Minnesoptimering för kolumnlagring](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- För att säkerställa att inläsningsanvändaren har tillräckligt med minne för att uppnå maximal komprimeringsgrad ska du använda inläsningsanvändare som är medlemmar i en mellanstor eller stor resursklass. 
- Läs in tillräckligt med rader för att helt fylla de nya radgrupperna. Vid en massinläsning hamnar alla 1 048 576 rader direkt i kolumnlagringen. Vid inläsningar med färre än 102 400 rader skickas raderna till deltalagringen som förvarar raderna i ett grupperat index tills det finns tillräcklig många för att de ska kunna komprimeras. Om du läser in för få rader kan alla rader hamna i deltalagringen och inte bli komprimerade direkt i kolumnlagringsformatet.


### <a name="handling-loading-failures"></a>Hantera inläsningsfel

Vid en inläsning med en extern tabell kan ett felmeddelande som ser ut ungefär så här visas: *"Frågan avbröts. Det högsta tröskelvärdet för avslag nåddes vid inläsning från en extern källa"*. Detta anger att dina externa data innehåller *felaktiga* poster. En datapost anses vara ”felaktig” om de faktiska datatyperna/antalet kolumner inte matchar kolumndefinitionen för den externa tabellen eller om data inte följer det angivna externa filformatet. 

Du kan åtgärda detta genom att se till att definitionerna för den externa tabellen och det externa filformatet är korrekta och att dina externa data följer dessa definitioner. Om en delmängd av de externa dataposterna är felaktiga kan du välja att avvisa dessa poster för dina frågor genom att använda avvisningsalternativen i CREATE EXTERNAL TABLE DDL.



## <a name="insert-data-into-production-table"></a>Infoga data i produktionstabeller
Här följer rekommendationer om hur du infogar rader i produktionstabellerna.


### <a name="batch-insert-statements"></a>Gruppinfogningsinstruktioner
En engångsinläsning i en liten tabell med en [INSERT-instruktion](/sql/t-sql/statements/insert-transact-sql.md) eller till och med en regelbunden inläsning på nytt av en sökning kan fungera bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Enskilda infogningar är inte lika effektiva som en massinläsning. 

Om du har tusentals eller fler enskilda infogningar under dagen bör du gruppera dem så att du kan infoga dem med en massinläsning.  Utveckla dina processer så att enskilda infogningar bifogas i en fil och skapa sedan en annan process som regelbundet läser in filen.

### <a name="create-statistics-after-the-load"></a>Skapa statistik efter inläsningen

För att få bättre frågeprestanda är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller efter betydande dataändringar.  En detaljerad förklaring av statistik finns i [Statistics][Statistics]. Följande exempel skapar statistik på fem kolumner i tabellen Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Rotera lagringsnycklar
Det är en bra säkerhetsrutin att regelbundet ändra åtkomstnyckeln till din Blob Storage. Du har två lagringsnycklar för ditt Blog Storage-konto. Det är för att du ska kunna rotera nycklarna.

Så här roterar du Azure Storage-kontonycklar:

1. Skapa andra databasbegränsade autentiseringsuppgifter baserat på åtkomstnyckeln för den sekundära lagringen.
2. Skapa en andra extern datakälla baserad på de nya autentiseringsuppgifterna.
3. Släpp och skapa de externa tabellerna så att de pekar till de nya externa datakällorna. 

När du har migrerat dina externa tabeller till den nya datakällan utför du dessa rensningsåtgärder:

1. Släpp den första extern datakällan.
2. Släpp de första databasbegränsade autentiseringsuppgifterna baserat på åtkomstnyckeln för den primära lagringen.
3. Logga in på Azure och återskapa den primära åtkomstnyckeln så att den är redo för din nästa rotering.


## <a name="next-steps"></a>Nästa steg
Om du vill övervaka inläsningsprocessen läser du [Övervaka arbetsbelastningen med datahanteringsvyer](sql-data-warehouse-manage-monitor.md).



