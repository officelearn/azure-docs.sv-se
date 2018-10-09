---
title: Replikering med Azure SQL Database Managed Instance | Microsoft Docs
description: Läs om hur du använder SQL Server-replikering med Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 25d13ba53eb5a8b411a557b5eaf05d278faa3733
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869320"
---
# <a name="replication-with-sql-database-managed-instance"></a>Replikering med SQL Database Managed Instance

Replikering är tillgänglig i offentlig förhandsversion på [Azure SQL Database Managed Instance](sql-database-managed-instance.md). En hanterad instans kan vara värd för utgivaren och distributören prenumerant databaser.

## <a name="common-configurations"></a>Vanliga konfigurationer

I allmänhet måste utgivaren och distributören båda vara antingen i molnet eller lokalt. Följande konfigurationer stöds:

- **Utgivaren med lokala distributören på hanterad instans**

   ![Replication-with-Azure-SQL-DB-Single-Managed-instance-Publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Utgivaren och distributören databaser kan konfigureras på en hanterad instans.

- **Utgivaren med fjärrdistributören på hanterad instans**

   ![Replication-with-Azure-SQL-DB-separate-Managed-instances-Publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   Utgivaren och distributören konfigureras på två hanterade instanser. I den här konfigurationen:

  - Både hanterade instanser är i samma virtuella nätverk.

  - Både hanterade instanser är på samma plats.

- **Utgivaren och distributören lokalt med prenumerant på hanterad instans**

   ![Replication-from-on-premises-to-Azure-SQL-DB-Subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   I den här konfigurationen är en Azure SQL database en prenumerant. Den här konfigurationen stöder migrering från en lokal plats till Azure. I rollen prenumerant kräver SQL database inte hanterad instans, men du kan använda en SQL Database Managed Instance som ett steg i migreringen från en lokal plats till Azure. Läs mer om Azure SQL Database-prenumeranter, [replikering till SQL Database](replication-to-sql-database.md).

## <a name="requirements"></a>Krav

Utgivaren och distributören på Azure SQL Database kräver:

- Azure SQL Database Managed Instance.

   >[!NOTE]
   >Azure SQL-databaser som inte är konfigurerade med Managed Instance kan bara vara prenumeranter.

- Alla instanser av SQL Server måste finnas i samma virtuella nätverk.

- Anslutningen använder SQL-autentisering mellan replikering deltagare.

- Ett Azure Storage-konto-resurs för arbetskatalogen för replikering.

## <a name="features"></a>Funktioner

Stöder:

- Transaktions- och replikering blandning av både lokala och Azure SQL Database Managed Instance instanser.

- Prenumeranter kan vara en lokal, enskilda databaser i Azure SQL Database eller databaser i pooler i elastiska pooler i Azure SQL Database.

- Enkelriktade eller dubbelriktade replikering

## <a name="configure-publishing-and-distribution-example"></a>Konfigurera publicering och distribution av exempel

1. [Skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md) i portalen.
2. [Skapa ett Azure Storage-konto](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbetskatalogen.

   Glöm inte att kopiera storage-nycklar. Se [visa och kopiera åtkomstnycklar för lagring](../storage/common/storage-account-manage.md#access-keys
).
3. Skapa en databas för utgivaren.

   I exemplet skripten nedan ersätter `<Publishing_DB>` med namnet på den här databasen.

4. Skapa en databasanvändare med SQL-autentisering för distributören. Se, [skapa databasanvändare](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Använd ett säkert lösenord.

   I exemplet skripten nedan använder `<SQL_USER>` och `<PASSWORD>` med det här kontot för SQL Server-databas användare och lösenord.

5. [Ansluta till SQL Database Managed Instance](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

6. Kör följande fråga för att lägga till distributören och distributionsdatabasen.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Om du vill konfigurera en utgivare för att använda en angiven distributionsplatsgrupp-databas, uppdatera och kör följande fråga.

   Ersätt `<SQL_USER>` och `<PASSWORD>` med SQL Server-konto och lösenord.

   Ersätt `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` med värdet för ditt lagringskonto.  

   Ersätt `<STORAGE_CONNECTION_STRING>` med anslutningssträngen från den **åtkomstnycklar** fliken för Microsoft Azure storage-kontot.

   När du har uppdaterat följande fråga kan du köra den.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Konfigurera utgivaren för replikering.

    I följande fråga ersätter `<Publishing_DB>` med namnet på publisher-databasen.

    Ersätt `<Publication_Name>` med namnet på publikationen.

    Ersätt `<SQL_USER>` och `<PASSWORD>` med SQL Server-konto och lösenord.

    När du uppdaterar frågan, kör du det för att skapa publikationen.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Lägg till den artikeln, prenumeration och push-Prenumerationsagent.

   Uppdatera följande skript för att lägga till dessa objekt.

   - Ersätt `<Object_Name>` med namnet på objektet publikationen.
   - Ersätt `<Object_Schema>` med namnet på källans schema.
   - Ersätt de andra parametrarna i vinkelparenteser `<>` att matcha värdena i de föregående skript.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>Begränsningar

Följande funktioner stöds inte:

- Uppdateringsbara prenumerationer

- Aktiv geo-replikering

## <a name="see-also"></a>Se även

- [Vad är en hanterad instans?](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
