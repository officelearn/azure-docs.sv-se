---
title: Konfigurera replikering i en Azure SQL Database-hanterad databasinstans | Microsoft Docs
description: Läs om hur du konfigurerar Transaktionsreplikering i en Azure SQL Database-hanterad instans-databas
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
ms.date: 02/07/2019
ms.openlocfilehash: b20a119a69ac796bc9ea85083d335f0a7d2fdf2d
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417963"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurera replikering i en Azure SQL Database-hanterad databasinstans

Transaktionsreplikering kan du replikera data till en Azure SQL Database-hanterad instans-databas från en SQL Server-databas eller en annan instans-databas. Du kan också använda Transaktionsreplikering för att skicka ändringar som gjorts i en instans-databas i Azure SQL Database hanterad instans till en SQL Server-databas till en enskild databas i Azure SQL Database till en databas i pool i en Azure SQL Database-elastisk pool. Transaktionsreplikering finns i offentlig förhandsversion på [Azure SQL Database-hanterad instans](sql-database-managed-instance.md). En hanterad instans kan vara värd för utgivaren och distributören prenumerant databaser. Se [Transaktionsreplikering konfigurationer](sql-database-managed-instance-transactional-replication.md#common-configurations) för tillgängliga konfigurationer.

## <a name="requirements"></a>Krav

Konfigurera en hanterad instans för att fungera som en utgivare eller distributör kräver:

- Att den hanterade instansen inte för närvarande deltar i en geo-replikeringsrelation.

   >[!NOTE]
   >Enskilda databaser och databaser i en pool i Azure SQL Database kan bara vara prenumeranter.

- Alla hanterade instanser måste finnas på samma virtuella nätverk.

- Anslutningen använder SQL-autentisering mellan replikering deltagare.

- Ett Azure Storage-konto-resurs för arbetskatalogen för replikering.

- Port 445 (TCP utgående) måste vara öppna i säkerhetsregler för hanterad instans-undernätet för att få åtkomst till Azure-filresursen

## <a name="features"></a>Funktioner

Stöder:

- Transaktions- och replikering blandning av lokal SQL Server och hanterade instanser i Azure SQL Database.
- Prenumeranter kan vara i en lokal SQL Server-databaser, enskild databaser/hanterade instanser i Azure SQL Database eller databaser i pooler i elastiska pooler i Azure SQL Database.
- Enkelriktade eller dubbelriktade replikering.

Följande funktioner stöds inte i en hanterad instans i Azure SQL Database:

- Uppdateringsbara prenumerationer.
- [Aktiv georeplikering](sql-database-active-geo-replication.md) och [automatisk redundans grupper](sql-database-auto-failover-group.md) bör inte användas om Transaktionsreplikering har konfigurerats.

## <a name="configure-publishing-and-distribution-example"></a>Konfigurera publicering och distribution av exempel

1. [Skapa en hanterad Azure SQL Database-instans](sql-database-managed-instance-create-tutorial-portal.md) i portalen.
2. [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbetskatalogen.

   Glöm inte att kopiera storage-nycklar. Se [visa och kopiera åtkomstnycklar för lagring](../storage/common/storage-account-manage.md#access-keys
).
3. Skapa en databasinstans för utgivaren.

   I exemplet skripten nedan ersätter `<Publishing_DB>` med namnet på databasinstans.

4. Skapa en databasanvändare med SQL-autentisering för distributören. Använd ett säkert lösenord.

   I exemplet skripten nedan använder `<SQL_USER>` och `<PASSWORD>` med det här kontot för SQL Server-databas användare och lösenord.

5. [Ansluta till SQL Database Managed Instance](sql-database-connect-query-ssms.md).

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
   
## <a name="see-also"></a>Se även

- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md)
- [Vad är en hanterad instans?](sql-database-managed-instance.md)
