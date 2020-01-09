---
title: Konfigurera replikering i en hanterad instans databas
description: Lär dig hur du konfigurerar Transaktionsreplikering mellan en Azure SQL Database Hanterad instans utgivare/distributör och hanterad instans prenumerant.
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
ms.openlocfilehash: fd881142e0260d313e197d5e40ae25a2621646df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372488"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurera replikering i en Azure SQL Database Hanterad instans databas

Med Transaktionsreplikering kan du replikera data till en Azure SQL Database Hanterad instans databas från en SQL Server databas eller en annan instans databas. 

Den här artikeln visar hur du konfigurerar replikering mellan en hanterad instans utgivare/distributör och en hanterad instans prenumerant. 

![Replikera mellan två hanterade instanser](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Du kan också använda Transaktionsreplikering för att skicka ändringar som gjorts i en instans databas i Azure SQL Database hanterade instansen till:

- En SQL Server databas.
- En enda databas i Azure SQL Database.
- En databas i poolen i en Azure SQL Database elastisk pool.
 
Transaktionsreplikering är i offentlig för hands version på [Azure SQL Database hanterade instansen](sql-database-managed-instance.md). En hanterad instans kan vara värd för utgivare, distributör och prenumerant databaser. Se [konfigurationer för transaktionell replikering](sql-database-managed-instance-transactional-replication.md#common-configurations) för tillgängliga konfigurationer.

  > [!NOTE]
  > - Den här artikeln är avsedd att hjälpa användaren att konfigurera replikering med en hanterad Azure Database-instans från slut punkt till slut punkt, från och med att skapa resurs gruppen. Om du redan har distribuerat hanterade instanser kan du gå vidare till [steg 4](#4---create-a-publisher-database) för att skapa en utgivar databas, eller [steg 6](#6---configure-distribution) om du redan har en utgivare och prenumerations databas och är redo att börja konfigurera replikering.  
  > - Den här artikeln konfigurerar utgivaren och distributören på samma hanterade instans. Om du vill placera distributören på en separat hanterade-instans går du till självstudien [Konfigurera replikering mellan en mi-utgivare och en mi-distributör](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Krav

Att konfigurera en hanterad instans så att den fungerar som en utgivare och/eller en distributör kräver:

- Att den hanterade instansen inte deltar i en geo-replikeringsrelation-relation.
- Att utgivarens hanterade instans finns i samma virtuella nätverk som distributören och prenumeranten eller att [vNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) har upprättats mellan de virtuella nätverken i alla tre entiteter. 
- Anslutningen använder SQL-autentisering mellan replikeringsdeltagare.
- En Azure Storage konto resurs för replikeringens arbets katalog.
- Port 445 (TCP utgående) är öppen i säkerhets reglerna för NSG för de hanterade instanserna för åtkomst till Azure-filresursen.  Om felet "Det gick inte att ansluta till Azure Storage \<lagrings kontots namn > med OS-fel 53", måste du lägga till en utgående regel i NSG för rätt undernät för SQL-hanterad instans.


 > [!NOTE]
 > Enskilda databaser och databaser i pooler i Azure SQL Database kan bara vara prenumeranter. 


## <a name="features"></a>Funktioner

Uppfyller

- Replikering och ögonblicks bilder av replikering av SQL Server lokala och hanterade instanser i Azure SQL Database.
- Prenumeranter kan finnas i lokala SQL Server databaser, enskilda databaser/hanterade instanser i Azure SQL Database, eller i pooler databaser i Azure SQL Database elastiska pooler.
- Enkelriktad eller dubbelriktad replikering.

Följande funktioner stöds inte i en hanterad instans i Azure SQL Database:

- [Uppdaterings bara prenumerationer](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktiv geo-replikering](sql-database-active-geo-replication.md) med transaktionell replikering. I stället för aktiv geo-replikering använder du [grupper för automatisk redundans](sql-database-auto-failover-group.md), men Observera att publikationen måste [tas bort manuellt](sql-database-managed-instance-transact-sql-information.md#replication) från den primära hanterade instansen och återskapas på den sekundära hanterade instansen efter redundansväxlingen.  
 
## <a name="1---create-a-resource-group"></a>1 – Skapa en resurs grupp

Använd [Azure Portal](https://portal.azure.com) för att skapa en resurs grupp med namnet `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 – skapa hanterade instanser

Använd [Azure Portal](https://portal.azure.com) för att skapa två [hanterade instanser](sql-database-managed-instance-create-tutorial-portal.md) i samma virtuella nätverk och undernät. Namnge till exempel de två hanterade instanserna:

- `sql-mi-pub` (tillsammans med några tecken för slumpmässig het)
- `sql-mi-sub` (tillsammans med några tecken för slumpmässig het)

Du måste också [Konfigurera en virtuell Azure-dator för att ansluta](sql-database-managed-instance-configure-vm.md) till dina Azure SQL Database hanterade instanser. 

## <a name="3---create-azure-storage-account"></a>3 – Skapa Azure Storage konto

[Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbets katalogen och skapa sedan en [fil resurs](../storage/files/storage-how-to-create-file-share.md) i lagrings kontot. 

Kopiera sökvägen till fil resursen i formatet: `\\storage-account-name.file.core.windows.net\file-share-name`

Exempel: `\\replstorage.file.core.windows.net\replshare`

Kopiera lagrings åtkomst nycklarna i formatet: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exempel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 – skapa en utgivar databas

Anslut till din `sql-mi-pub` hanterade instans med SQL Server Management Studio och kör följande Transact-SQL-kod (T-SQL) för att skapa utgivar databasen:

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

## <a name="5---create-a-subscriber-database"></a>5 – skapa en prenumerations databas

Anslut till din `sql-mi-sub` hanterade instans med SQL Server Management Studio och kör följande T-SQL-kod för att skapa din tomma prenumerant databas:

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

## <a name="6---configure-distribution"></a>6 – Konfigurera distribution

Anslut till din `sql-mi-pub` hanterade instans med SQL Server Management Studio och kör följande T-SQL-kod för att konfigurera distributions databasen. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – Konfigurera utgivare att använda distributör 

På den hanterade instansen av Publisher `sql-mi-pub`ändrar du frågekörningen till [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) -läge och kör följande kod för att registrera den nya distributören hos utgivaren. 

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
   > Se till att endast använda omvänt snedstreck (`\`) för file_storage-parametern. Om du använder ett snedstreck (`/`) kan det orsaka ett fel när du ansluter till fil resursen. 

Det här skriptet konfigurerar en lokal utgivare på den hanterade instansen, lägger till en länkad server och skapar en uppsättning jobb för SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 – Skapa publikation och prenumerant

Använd [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) läge och kör följande T-SQL-skript för att aktivera replikering för din databas och konfigurera replikering mellan utgivaren, distributören och prenumeranten. 

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
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – ändra agent parametrar

Azure SQL Database hanterade instansen har för närvarande problem med en server del med anslutnings agenter. Även om det här problemet åtgärdas är lösningen att öka timeout-värdet för inloggning för-replik agenterna. 

Kör följande T-SQL-kommando på utgivaren för att öka inloggnings tids gränsen: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Kör följande T-SQL-kommando igen för att ange timeout-värdet för inloggning till standardvärdet, om du behöver göra det:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Starta om alla tre agenterna för att tillämpa ändringarna. 

## <a name="10---test-replication"></a>10-test-replikering

När replikeringen har kon figurer ATS kan du testa den genom att infoga nya objekt i utgivaren och titta på ändringarna Sprid till prenumeranten. 

Kör följande T-SQL-kodfragment för att visa raderna på prenumeranten:

```sql
select * from dbo.ReplTest
```

Kör följande T-SQL-kodfragment för att infoga ytterligare rader i utgivaren och kontrol lera sedan raderna igen på prenumeranten. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

Du tar bort publikationen genom att köra följande T-SQL-kommando:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Om du vill ta bort alternativet replikering från databasen kör du följande T-SQL-kommando:

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

Du kan rensa dina Azure-resurser genom [att ta bort de hanterade instans resurserna från resurs gruppen](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) och sedan ta bort resurs gruppen `SQLMI-Repl`. 

   
## <a name="see-also"></a>Se även

- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md)
- [Självstudie: Konfigurera Transaktionsreplikering mellan en MI-utgivare och SQL Server prenumerant](sql-database-managed-instance-configure-replication-tutorial.md)
- [Vad är en hanterad instans?](sql-database-managed-instance.md)
