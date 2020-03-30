---
title: Konfigurera replikering i en hanterad instansdatabas
description: Lär dig att konfigurera transaktionsreplikering mellan en Azure SQL Database-hanterad instansutgivare/distributör och hanterad instansprenumerant.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299081"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurera replikering i en hanterad instansdatabas för Azure SQL Database

Med transaktionsreplikering kan du replikera data till en hanterad instansdatabas för Azure SQL Database från en SQL Server-databas eller en annan instansdatabas. 

Den här artikeln visar hur du konfigurerar replikering mellan en hanterad instansutgivare/distributör och en hanterad instansprenumerant. 

![Replikera mellan två hanterade instanser](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Du kan också använda transaktionsreplikering för att skicka ändringar som gjorts i en instansdatabas i Azure SQL Database-hanterad instans till:

- En SQL Server-databas.
- En enda databas i Azure SQL Database.
- En poolad databas i en elastisk Azure SQL Database-pool.
 
Transaktionsreplikering är i offentlig förhandsversion på [Azure SQL Database-hanterad instans](sql-database-managed-instance.md). En hanterad instans kan vara värd för utgivare, distributör och prenumerantdatabaser. Se [transaktionsreplikeringskonfigurationer](sql-database-managed-instance-transactional-replication.md#common-configurations) för tillgängliga konfigurationer.

  > [!NOTE]
  > - Den här artikeln är avsedd att vägleda en användare i att konfigurera replikering med en Azure Database hanterad instans från början till, börjar med att skapa resursgruppen. Om du redan har distribuerat hanterade instanser går du vidare till [steg 4](#4---create-a-publisher-database) för att skapa utgivardatabasen eller [steg 6](#6---configure-distribution) om du redan har en utgivar- och prenumerantdatabas och är redo att börja konfigurera replikering.  
  > - I den här artikeln konfigureras utgivaren och distributören på samma hanterade instans. Om du vill placera distributören på en separat manged-instans läser du självstudien [Konfigurera replikering mellan en MI-utgivare och en MI-distributör](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Krav

För att kunna konfigurera en hanterad instans så att den fungerar som utgivare och/eller distributör krävs:

- Att den hanterade utgivaren finns i samma virtuella nätverk som distributören och abonnenten, eller [att vNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) har upprättats mellan de virtuella nätverken för alla tre entiteterna. 
- Anslutningen använder SQL-autentisering mellan replikeringsdeltagare.
- En Azure Storage-kontoresurs för replikeringsarbetskatalogen.
- Port 445 (TCP-utgående) är öppen i NSG:s säkerhetsregler för hanterade instanser för att komma åt Azure-filresursen.  Om du stöter på felet "misslyckades med att ansluta till azure storage \<storage account name> med os-fel 53", måste du lägga till en utgående regel till NSG för lämplig SQL-hanterad instans undernät.


 > [!NOTE]
 > Enskilda databaser och poolade databaser i Azure SQL Database kan bara vara prenumeranter. 


## <a name="features"></a>Funktioner

Stöder:

- Transaktions- och ögonblicksbildreplikeringsblandning av SQL Server lokalt och hanterat instanser i Azure SQL Database.
- Prenumeranter kan finnas i lokala SQL Server-databaser, enskilda databaser/hanterade instanser i Azure SQL Database eller poolade databaser i elastiska Azure SQL Database-pooler.
- Enkelriktad eller dubbelriktad replikering.

Följande funktioner stöds inte i en hanterad instans i Azure SQL Database:

- [Uppdateringsbara prenumerationer](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktiv geo-replikering](sql-database-active-geo-replication.md) med transaktionell replikering. I stället för aktiv geo-replikering använder du [Grupper för automatisk redundans](sql-database-auto-failover-group.md), men observera att publikationen måste [tas bort manuellt](sql-database-managed-instance-transact-sql-information.md#replication) från den primära hanterade instansen och återskapas på den sekundära hanterade instansen efter redundans.  
 
## <a name="1---create-a-resource-group"></a>1 - Skapa en resursgrupp

Använd [Azure-portalen](https://portal.azure.com) för att skapa `SQLMI-Repl`en resursgrupp med namnet .  

## <a name="2---create-managed-instances"></a>2 - Skapa hanterade instanser

Använd [Azure-portalen](https://portal.azure.com) för att skapa två [hanterade instanser](sql-database-managed-instance-create-tutorial-portal.md) i samma virtuella nätverk och undernät. Du kan till exempel namnge de två hanterade instanserna:

- `sql-mi-pub`(tillsammans med några tecken för randomisering)
- `sql-mi-sub`(tillsammans med några tecken för randomisering)

Du måste också [konfigurera en Virtuell Azure-dator för att ansluta](sql-database-managed-instance-configure-vm.md) till dina hanterade Azure SQL-databasinstanser. 

## <a name="3---create-azure-storage-account"></a>3 - Skapa Azure Storage-konto

[Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbetskatalogen och skapa sedan en [filresurs](../storage/files/storage-how-to-create-file-share.md) i lagringskontot. 

Kopiera sökvägen till filresursen i formatet:`\\storage-account-name.file.core.windows.net\file-share-name`

Exempel: `\\replstorage.file.core.windows.net\replshare`

Kopiera lagringsnycklarna i formatet:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exempel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Mer information finns i [Hantera åtkomstnycklar för lagringskonto](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Skapa en utgivardatabas

Anslut till `sql-mi-pub` din hanterade instans med SQL Server Management Studio och kör följande Transact-SQL-kod (T-SQL) för att skapa utgivardatabasen:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - Skapa en abonnentdatabas

Anslut till `sql-mi-sub` din hanterade instans med SQL Server Management Studio och kör följande T-SQL-kod för att skapa din tomma prenumerantdatabas:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - Konfigurera distribution

Anslut till `sql-mi-pub` din hanterade instans med SQL Server Management Studio och kör följande T-SQL-kod för att konfigurera distributionsdatabasen. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Konfigurera utgivaren att använda distributör 

I den hanterade `sql-mi-pub`utgivarinstansen ändrar du frågekörningen till [SQLCMD-läge](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) och kör följande kod för att registrera den nya distributören hos utgivaren. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Var noga med att bara`\`använda omvänt snedstreck ( ) för parametern file_storage. Om du använder`/`ett snedstreck ( ) kan det orsaka ett fel när du ansluter till filresursen. 

Det här skriptet konfigurerar en lokal utgivare på den hanterade instansen, lägger till en länkad server och skapar en uppsättning jobb för SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 - Skapa publikation och prenumerant

Med [SQLCMD-läge](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) kör du följande T-SQL-skript för att aktivera replikering för databasen och konfigurera replikering mellan utgivaren, distributören och prenumeranten. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Ändra agentparametrar

Azure SQL Database-hanterad instans har för närvarande vissa serverdproblem med anslutning till replikeringsagenter. Även om det här problemet åtgärdas, lösningen för att öka värdet för inloggningstid för replikeringsagenterna. 

Kör följande T-SQL-kommando på utgivaren för att öka tidsgränsen för inloggning: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Kör följande T-SQL-kommando igen för att ställa in tidsgränsen för inloggning till standardvärdet, om du skulle behöva göra det:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Starta om alla tre agenterna för att tillämpa dessa ändringar. 

## <a name="10---test-replication"></a>10 - Testreplikering

När replikeringen har konfigurerats kan du testa den genom att infoga nya objekt på utgivaren och titta på ändringarna som sprids till prenumeranten. 

Kör följande T-SQL-kodavsnitt om du vill visa raderna på prenumeranten:

```sql
select * from dbo.ReplTest
```

Kör följande T-SQL-kodavsnitt för att infoga ytterligare rader på utgivaren och kontrollera sedan raderna igen på prenumeranten. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill släppa publikationen kör du följande T-SQL-kommando:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Om du vill ta bort replikeringsalternativet från databasen kör du följande T-SQL-kommando:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Om du vill inaktivera publicering och distribution kör du följande T-SQL-kommando:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Du kan rensa dina Azure-resurser genom [att ta bort de hanterade instansresurserna från resursgruppen](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) och sedan ta bort resursgruppen `SQLMI-Repl`. 

   
## <a name="see-also"></a>Se även

- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md)
- [Självstudiekurs: Konfigurera transaktionsreplikering mellan en MI-utgivare och SQL Server-prenumerant](sql-database-managed-instance-configure-replication-tutorial.md)
- [Vad är en hanterad instans?](sql-database-managed-instance.md)
