---
title: Långsiktig lagring av säkerhetskopior & ARS valvet - Azure SQL Database | Microsoft Docs
description: Lär dig hur du lagrar automatisk säkerhetskopiering i SQL Azure-lagring och återställa dem
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/10/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 80dd58a9c0267975c9e4df74c77d60ac861a1fdb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="configure-and-restore-backups-from-azure-sql-database-long-term-backup-retention-using-azure-sql-storage"></a>Konfigurera och återställa säkerhetskopior från Azure SQL Database långsiktig lagring av säkerhetskopior använder SQL Azure-lagring

Du kan konfigurera Azure SQL database med en [långsiktig lagring av säkerhetskopior](sql-database-long-term-retention.md) princip (LTR) automatiskt säkerhetskopiorna ska behållas i Azure blob storage för upp till 10 år. Du kan sedan återställa en databas med dessa säkerhetskopior med hjälp av Azure-portalen eller PowerShell.

> [!NOTE]
> Som en del av den första versionen av förhandsversionen av den här funktionen i oktober 2016 lagras säkerhetskopior i Azure Recovery Services-tjänsten-valvet. Den här uppdateringen tar bort den här beroende, men för bakåtkompatibilitet ursprungliga API stöds förrän den 31 maj 2018. Om du behöver interagera med säkerhetskopieringar i Azure Services Recovery-valvet finns [långsiktig lagring av säkerhetskopior med hjälp av Azure Recovery Services-tjänsten valvet](sql-database-long-term-backup-retention-configure-vault.md). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Använda Azure portal för att konfigurera principer för långsiktig kvarhållning och återställa säkerhetskopior

Följande avsnitt visar hur du använder Azure-portalen för att konfigurera långsiktig kvarhållning, visa säkerhetskopieringar i långsiktig kvarhållning och återställa säkerhetskopia från långsiktig kvarhållning.

### <a name="configure-long-term-retention-policies"></a>Konfigurera principer för långsiktig kvarhållning

Du kan konfigurera SQL-databas till [behålla automatiska säkerhetskopieringar](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivån. 

1. Välj SQL-server i Azure-portalen och klicka sedan på **långsiktig lagring av säkerhetskopior**.

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. På den **konfigurera principer** fliken, markera den databas som du vill ange eller ändra långsiktig säkerhetskopiering bevarandeprinciper.

   ![Välj databas](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. I den **konfigurera principer** rutan, Välj om vill behålla varje vecka, månad eller år säkerhetskopieringar och ange kvarhållningsperiod för varje. 

   ![Konfigurera principer](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. När du är klar klickar du på **tillämpa**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visa säkerhetskopior och återställa från en säkerhetskopia med hjälp av Azure portal

Visa säkerhetskopieringar som finns kvar för en viss databas med en princip för LTR och återställning från dessa säkerhetskopior. 

1. Välj SQL-server i Azure-portalen och klicka sedan på **långsiktig lagring av säkerhetskopior**.

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. På den **tillgängliga säkerhetskopieringar** Markera databasen som du vill se tillgängliga säkerhetskopior.

   ![Välj databas](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. I den **tillgängliga säkerhetskopieringar** fönstret granska de tillgängliga säkerhetskopiorna. 

   ![Visa säkerhetskopior](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Välj den säkerhetskopia som du vill återställa och ange sedan det nya databasnamnet.

   ![återställ](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klicka på **OK** att återställa databasen från en säkerhetskopia i Azure SQL storage till den nya databasen.

6. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp över återställningsjobb från valvet](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När Återställningsjobbet har slutförts, öppna den **SQL-databaser** sidan kan du visa den nyligen återställda databasen.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Använda PowerShell för att konfigurera principer för långsiktig kvarhållning och återställa säkerhetskopior

Följande avsnitt visar hur du använder PowerShell för att konfigurera långsiktig säkerhetskopiering kvarhållning, visa säkerhetskopieringar i Azure SQL-lagring och återställning från en säkerhetskopia i SQL Azure storage.

### <a name="create-an-ltr-policy"></a>Skapa en princip för LTR

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visa LTR principer
Det här exemplet visar hur du listar LTR principer på en server

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```

### <a name="view-ltr-backups"></a>Visa LTR säkerhetskopior

Det här exemplet visar hur du listar LTR säkerhetskopieringar på en server. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Ta bort LTR säkerhetskopior

Det här exemplet visar hur du tar bort en LTR säkerhetskopiering från listan över säkerhetskopior.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Återställa från LTR säkerhetskopior
Det här exemplet visas hur du återställer från en säkerhetskopia LTR. Observera att det här gränssnittet inte har ändrats men resurs-id-parametern kräver nu LTR säkerhetskopiering resurs-id. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Härifrån kan du kan ansluta till den återställda databasen använder SQL Server Management Studio för att utföra uppgifter som krävs, exempelvis genom att extrahera en bit data från den återställda databasen ska kopieras till den befintliga databasen eller ta bort den befintliga databasen och Byt namn på den återställda databasen till det befintliga databasnamnet. Se [punkt tidpunkt för återställning](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
