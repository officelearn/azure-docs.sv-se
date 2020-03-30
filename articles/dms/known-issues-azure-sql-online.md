---
title: 'Kända problem: Onlinemigreringar till SQL Database'
titleSuffix: Azure Database Migration Service
description: Lär dig mer om kända problem/migreringsbegränsningar med onlinemigreringar till Azure SQL Database med hjälp av Migreringstjänsten för Azure Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650783"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Kända problem/migreringsbegränsningar med onlinemigreringar till Azure SQL Database

Kända problem och begränsningar som är associerade med onlinemigreringar från SQL Server till Azure SQL Database beskrivs nedan.

> [!IMPORTANT]
> Med onlinemigreringar av SQL Server till Azure SQL Database stöds inte migrering av SQL_variant datatyper.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrering av tidstabeller stöds inte

**Symptom**

Om källdatabasen består av en eller flera temporala tabeller misslyckas databasmigrering under åtgärden "Fullständig databelastning" och följande meddelande kan visas:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exempel på tidstabellfel](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Workaround**

Följ dessa steg.

1. Leta reda på de tidsmässiga tabellerna i källschemat med frågan nedan.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Uteslut dessa tabeller från bladet **Konfigurera migreringsinställningar,** där du anger tabeller för migrering.

3. Kör migreringsaktiviteten igen.

**Resurser**

Mer information finns i artikeln [Temporal Tables](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migrering av tabeller innehåller en eller flera kolumner med hierarkins datatyp

**Symptom**

Du kan se ett SQL-undantag som föreslår "ntext är inkompatibel med hierarchyid" under åtgärden "Fullständig datainläsning":

![exempel på hierarkifel](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Workaround**

Följ dessa steg.

1. Leta reda på de användartabeller som innehåller kolumner med hierarkins datatyp med frågan nedan.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Uteslut dessa tabeller från bladet **Konfigurera migreringsinställningar,** där du anger tabeller för migrering.

3. Kör migreringsaktiviteten igen.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Migreringsfel med olika integritetsöverträdelser med aktiva utlösare i schemat under "Fullständig datainläsning" eller "Inkrementell datasynkronisering"

**Workaround**

Följ dessa steg.

1. Leta reda på de utlösare som för närvarande är aktiva i källdatabasen med frågan nedan:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Inaktivera utlösare i källdatabasen med hjälp av stegen i artikeln [DISABLE TRIGGER (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)

3. Kör migreringsaktiviteten igen.

### <a name="support-for-lob-data-types"></a>Stöd för LOB-datatyper

**Symptom**

Om längden på kolumnen Stora objekt (LOB) är större än 32 kB kan data trunkeras vid målet. Du kan kontrollera längden på LOB-kolumnen med frågan nedan:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Workaround**

Om du har en LOB-kolumn som är större än 32 kB kontaktar du teknikteamet på [Fråga Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problem med tidsstämpelkolumner

**Symptom**

Azure Database Migration Service migrerar inte källtidsstämpeln. I stället genererar Azure Database Migration Service ett nytt tidsstämpelvärde i måltabellen.

**Workaround**

Om du behöver Azure Database Migration Service för att migrera det exakta tidsstämpelvärdet som lagras i källtabellen kontaktar du teknikteamet på [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Datamigreringsfel ger inte ytterligare information om databasblad för detaljerad status

**Symptom**

När du stöter på migreringsfel i statusvyn Databaserinformation kanske det inte finns ytterligare information om migreringsfelen om du väljer länken **Datamigreringsfel** i det övre menyfliksområdet.

![datamigreringsfel inga detaljerexempel](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Workaround**

Använd följande steg för att komma åt specifika felinformation.

1. Stäng databasblad för detaljerad status för att visa skärmen Migreringsaktivitet.

     ![skärmen migreringsaktivitet](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Välj **Visa felinformation** om du vill visa specifika felmeddelanden som hjälper dig att felsöka migreringsfel.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Geografidatatyp stöds inte i SQLDB-onlinemigrering

**Symptom**

Migreringen misslyckas med ett felmeddelande som innehåller följande text:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Workaround**

Azure Database Migration Service stöder datatypen Geografi för offlinemigreringar till Azure SQL Database, men för onlinemigreringar stöds inte geografidatatypen. Prova alternativa metoder för att ändra datatypen vid källan till en typ som stöds innan du försöker använda Azure Database Migration Service för en onlinemigrering av den här databasen.

### <a name="supported-editions"></a>Utgåvor som stöds

**Symptom**

Migreringen misslyckas med ett felmeddelande som innehåller följande text:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Workaround**

Stöd för onlinemigreringar till Azure SQL Database med hjälp av Azure Database Migration Service utökas endast till enterprise-, standard- och utvecklarversionerna. Se till att du använder en utgåva som stöds innan du påbörjar migreringsprocessen.
