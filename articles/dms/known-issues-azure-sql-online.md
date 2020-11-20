---
title: 'Kända problem: online-migreringar till SQL Database'
titleSuffix: Azure Database Migration Service
description: Läs om kända problem/begränsningar för migrering med online-migreringar för att Azure SQL Database med hjälp av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 748c3f8e38b98b2cbdcfecdf7d755827230fdb3d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962475"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Kända problem/migrerings begränsningar med online-migreringar till Azure SQL Database

Kända problem och begränsningar som är kopplade till online-migrering från SQL Server till Azure SQL Database beskrivs nedan.

> [!IMPORTANT]
> Migrering av SQL_variant data typer stöds inte för migrering av SQL Server Azure SQL Database.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrering av temporala tabeller stöds inte

**Symptom**

Om din käll databas består av en eller flera temporala tabeller, Miss lyckas migreringen av databasen under åtgärden "fullständig data inläsning" och du kan se följande meddelande:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exempel på temporal Table-fel](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Lösning**

Följ dessa steg.

1. Hitta de temporala tabellerna i ditt käll schema med hjälp av frågan nedan.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Exkludera dessa tabeller från bladet **Konfigurera inställningar för migrering** där du anger tabeller för migrering.

3. Kör migrerings aktiviteten igen.

**Resurser**

Mer information finns i artikel [temporala tabeller](/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migrering av tabeller innehåller en eller flera kolumner med data typen hierarchyid

**Symptom**

Du kan se ett SQL-undantag som föreslår att "ntext är inkompatibelt med hierarchyid" under åtgärden "fullständig data inläsning":

![exempel på hierarchyid-fel](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Lösning**

Följ dessa steg.

1. Hitta de användar tabeller som innehåller kolumner med data typen hierarchyid med hjälp av frågan nedan.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Exkludera dessa tabeller från bladet **Konfigurera inställningar för migrering** där du anger tabeller för migrering.

3. Kör migrerings aktiviteten igen.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Migrerings fel med olika integritets överträdelser med aktiva utlösare i schemat under "fullständig data inläsning" eller "stegvis data synkronisering"

**Lösning**

Följ dessa steg.

1. Hitta de utlösare som för närvarande är aktiva i käll databasen med hjälp av frågan nedan:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Inaktivera utlösare på käll databasen med hjälp av stegen i artikeln [inaktivera UTlösare (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Kör migrerings aktiviteten igen.

### <a name="support-for-lob-data-types"></a>Stöd för LOB-datatyper

**Symptom**

Om längden på den stora objekt kolumnen (LOB) är större än 32 KB, kan data bli trunkerade på målet. Du kan kontrol lera längden på LOB-kolumnen med frågan nedan:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Lösning**

Om du har en LOB-kolumn som är större än 32 KB kan du kontakta teknik teamet på [fråga Azure Database-migreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problem med Timestamp-kolumner

**Symptom**

Azure Database Migration Service migrerar inte källans tidsstämpel-värde. i stället genererar Azure Database Migration Service ett nytt tidsstämpel-värde i mål tabellen.

**Lösning**

Om du behöver Azure Database Migration Service att migrera det exakta tidsstämpel-värdet som lagras i käll tabellen, kontaktar du teknik teamet i [fråga Azure Database-migreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Fel vid datamigrering innehåller inte ytterligare information på bladet databas detaljerat läge

**Symptom**

När du kommer över migreringsåtgärder i vyn databas information visas inte länken **datamigrerings fel** i det övre menyfliksområdet. det kan inte finnas ytterligare information som är speciell för migrerings felen.

![exempel på fel vid datamigrering](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Lösning**

Använd följande steg för att få detaljerad information om felen.

1. Stäng bladet databas detaljerat läge för att visa skärmen migrering av aktivitet.

     ![skärmen migrering av aktivitet](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Välj **se fel information** för att visa vissa fel meddelanden som hjälper dig att felsöka migrerings fel.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Geografi data typen stöds inte i SQLDB online-migrering

**Symptom**

Migreringen Miss lyckas med ett fel meddelande som innehåller följande text:

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**Lösning**

Även om Azure Database Migration Service stöder geografi data typen för offline-migrering till Azure SQL Database, stöds inte geografi-datatypen för online-migrering. Försök med alternativa metoder för att ändra data typen vid källan till en typ som stöds innan du försöker använda Azure Database Migration Service för en online-migrering av den här databasen.

### <a name="supported-editions"></a>Versioner som stöds

**Symptom**

Migreringen Miss lyckas med ett fel meddelande som innehåller följande text:

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**Lösning**

Stöd för online-migreringar till Azure SQL Database att använda Azure Database Migration Service utökar endast Enterprise-, standard-och Developer-versionerna. Se till att du använder en version som stöds innan du påbörjar migreringsprocessen.