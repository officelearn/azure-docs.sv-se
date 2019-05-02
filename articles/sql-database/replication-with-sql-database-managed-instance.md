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
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926203"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurera replikering i en Azure SQL Database-hanterad databasinstans

Transaktionsreplikering kan du replikera data till en Azure SQL Database-hanterad instans-databas från en SQL Server-databas eller en annan instans-databas. 

Du kan också använda Transaktionsreplikering för att skicka ändringar som gjorts i en instans-databas i Azure SQL Database-hanterad instans till:

- En SQL Server-databas.
- En enskild databas i Azure SQL Database.
- En databas i pool i en Azure SQL Database-elastisk pool.
 
Transaktionsreplikering finns i offentlig förhandsversion på [Azure SQL Database-hanterad instans](sql-database-managed-instance.md). En hanterad instans kan vara värd för utgivaren och distributören prenumerant databaser. Se [Transaktionsreplikering konfigurationer](sql-database-managed-instance-transactional-replication.md#common-configurations) för tillgängliga konfigurationer.

  > [!NOTE]
  > Den här artikeln är avsedd att hjälpa en användare i Konfigurera replikering med en Azure Database-hanterad instans från slutpunkt till slutpunkt, från och med processen när resursgruppen skapas. Om du har redan hanterade instanser som distribuerats, gå vidare till [steg 4](#4---create-a-publisher-database) att skapa din publiceringsdatabas eller [steg 6](#6---configure-distribution) om du redan har en utgivare och prenumerant databas och är redo att börja Konfigurera replikering.  

## <a name="requirements"></a>Krav

Konfigurera en hanterad instans för att fungera som en utgivare och/eller en distributör kräver:

- Att den hanterade instansen inte för närvarande deltar i en geo-replikeringsrelation.
- Att utgivaren hanterad instans är i samma virtuella nätverk som utgivaren och prenumeranten, eller [vNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) har upprättats mellan virtuella nätverk över alla tre entiteter. 
- Anslutningen använder SQL-autentisering mellan replikering deltagare.
- Ett Azure Storage-konto-resurs för arbetskatalogen för replikering.
- Port 445 (TCP utgående) är öppen i säkerhetsregler för NSG för hanterade instanser för att få åtkomst till Azure-filresursen. 


 > [!NOTE]
 > Enskilda databaser och databaser i en pool i Azure SQL Database kan bara vara prenumeranter. 


## <a name="features"></a>Funktioner

Stöder:

- Transaktions- och replikering blandning av lokal SQL Server och hanterade instanser i Azure SQL Database.
- Prenumeranter kan vara i en lokal SQL Server-databaser, enskild databaser/hanterade instanser i Azure SQL Database eller databaser i pooler i elastiska pooler i Azure SQL Database.
- Enkelriktade eller dubbelriktade replikering.

Följande funktioner stöds inte i en hanterad instans i Azure SQL Database:

- [Uppdateringsbara prenumerationer](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktiv georeplikering](sql-database-active-geo-replication.md) och [automatisk redundans grupper](sql-database-auto-failover-group.md) bör inte användas om Transaktionsreplikering har konfigurerats.
 
## <a name="1---create-a-resource-group"></a>1 – skapa en resursgrupp

Använd den [Azure-portalen](https://portal.azure.com) att skapa en resursgrupp med namnet `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 – Skapa hanterade instanser

Använd den [Azure-portalen](https://portal.azure.com) skapa två [hanterade instanser](sql-database-managed-instance-create-tutorial-portal.md) på samma virtuella nätverk och undernät. Vara bör namnet två hanterade instanser:

- `sql-mi-pub`
- `sql-mi-sub`

Du måste också [konfigurera en virtuell Azure-dator att ansluta](sql-database-managed-instance-configure-vm.md) till din Azure SQL Database-hanterade instanser. 

## <a name="3---create-azure-storage-account"></a>3 – skapa Azure Storage-konto

[Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbetskatalogen, och skapa sedan en [filresurs](../storage/files/storage-how-to-create-file-share.md) i lagringskontot. 

Kopiera sökvägen till filresursen i formatet: `\\storage-account-name.file.core.windows.net\file-share-name`

Kopiera åtkomstnycklar för lagring i formatet: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Mer information finns i [Visa och kopiera åtkomstnycklar för lagring](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 – skapa en publiceringsdatabas

Ansluta till din `sql-mi-pub` hanterad instans med hjälp av SQL Server Management Studio och kör följande kod för Transact-SQL (T-SQL) för att skapa publisher-databasen:

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

## <a name="5---create-a-subscriber-database"></a>5 – skapa en prenumerantdatabas

Ansluta till din `sql-mi-sub` hanterad instans med hjälp av SQL Server Management Studio och kör följande T-SQL-kod för att skapa en tom prenumerantdatabas:

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

## <a name="6---configure-distribution"></a>6 – konfigurera distribution

Ansluta till din `sql-mi-pub` hanterad instans med hjälp av SQL Server Management Studio och kör följande T-SQL-kod för att konfigurera din distributionsdatabas. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – konfigurera utgivare för att använda distributören 

Publisher-hanterad instans `sql-mi-pub`, ändra körningen av frågan till [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) läge och kör följande kod för att registrera nya distributören med publisher. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

Det här skriptet konfigurerar en lokal utgivare på den hanterade instansen lägger till en länkad server och skapar en uppsättning jobb för SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 – Skapa publikation och prenumeranten

Med hjälp av [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) läge, kör följande T-SQL-skript för att aktivera replikering för din databas och konfigurera replikering mellan dina utgivaren och distributören prenumerant. 

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

## <a name="9---modify-agent-parameters"></a>9 – ändra agenten parametrar

Azure SQL Database-hanterad instans har för närvarande vissa backend-problem med anslutningen med replikeringsagenter. Även om det här problemet är åtgärdas håller på att åtgärdas, lösning för att öka inloggning timeoutvärdet för replikeringsagenter. 

Kör följande T-SQL-kommando på utgivaren för att öka tidsgränsen för inloggning: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Kör följande T-SQL-kommandot igen för att ange tidsgränsen för inloggning till standardvärdet om du behöver göra det:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Starta om alla tre agenter för att tillämpa ändringarna. 

## <a name="10---test-replication"></a>10 – testa replikering

När replikering har konfigurerats kan testa du den genom att lägga till nya objekt på utgivaren och tittar på ändringarna som sprids till prenumeranten. 

Kör följande T-SQL-fragment om du vill visa rader på prenumeranten:

```sql
select * from dbo.ReplTest
```

Kör följande T-SQL-kodfragment för att infoga ytterligare rader på utgivaren och kontrollera sedan raderna igen på prenumeranten. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort publikationen, kör du följande T-SQL-kommando:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Om du vill ta bort replikeringsalternativ från databasen, kör du följande T-SQL-kommando:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Om du vill inaktivera publicering och distribution, kör du följande T-SQL-kommando:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Du kan rensa dina Azure-resurser genom att [tar bort hanterad instans-resurser från resursgruppen](../azure-resource-manager/manage-resources-portal.md#delete-resources) och ta bort resursgruppen `SQLMI-Repl`. 

   
## <a name="see-also"></a>Se även

- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md)
- [Vad är en hanterad instans?](sql-database-managed-instance.md)
