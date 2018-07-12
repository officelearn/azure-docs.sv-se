---
title: Hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior | Microsoft Docs
description: Lär dig hur du lagrar automatiska säkerhetskopieringar i SQL Azure-lagring och sedan återställa dem.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f7125a18aa2496ebe8367443a67502a7a7dbac02
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969195"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior

Du kan konfigurera Azure SQL-databas med en [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) princip (LTR) för att automatiskt behålla säkerhetskopior i Azure blob storage för upp till 10 år. Du kan sedan återställa en databas med hjälp av dessa säkerhetskopior med hjälp av Azure portal eller PowerShell.

> [!NOTE]
> Som en del av den första versionen av förhandsversionen av den här funktionen i oktober 2016 lagrades säkerhetskopieringar i Azure-tjänster Recovery Service-valv. Uppdateringen tar bort det här beroendet, men den ursprungliga API stöds för bakåtkompatibilitet till den 31 maj 2018. Om du vill interagera med säkerhetskopior i Azure-tjänster Recovery-valvet kan se [långsiktig kvarhållning av säkerhetskopior med hjälp av Azure-tjänster Recovery Service-valv](sql-database-long-term-backup-retention-configure-vault.md). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Använda Azure portal för att konfigurera principer för långsiktig kvarhållning och återställa säkerhetskopior

I följande avsnitt visas hur du använder Azure-portalen för att konfigurera långsiktig kvarhållning, visa säkerhetskopior i långsiktig kvarhållning och återställa säkerhetskopia från långsiktig kvarhållning.

### <a name="configure-long-term-retention-policies"></a>Konfigurera principer för långsiktig kvarhållning

Du kan konfigurera SQL-databas till [automatiska säkerhetskopieringar bevaras](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivå. 

1. Välj din SQL-server i Azure-portalen och klicka sedan på **långsiktig kvarhållning av säkerhetskopior**.

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. På den **konfigurera principer för** väljer du en databas som du vill ange eller ändra principer för långsiktig kvarhållning av säkerhetskopior.

   ![Välj databas](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. I den **konfigurera principer för** fönstret, Välj om vill behålla säkerhetskopior per månad eller år i varje vecka, och ange kvarhållningsperioden för var och en. 

   ![Konfigurera principer](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. När du är klar klickar du på **tillämpa**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visa säkerhetskopior och återställa från en säkerhetskopia med hjälp av Azure portal

Visa säkerhetskopior som finns kvar för en viss databas med LTR-princip och återställning från dessa säkerhetskopior. 

1. Välj din SQL-server i Azure-portalen och klicka sedan på **långsiktig kvarhållning av säkerhetskopior**.

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. På den **tillgängliga säkerhetskopior** väljer du en databas som du vill se tillgängliga säkerhetskopior.

   ![Välj databas](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. I den **tillgängliga säkerhetskopior** fönstret Granska tillgängliga säkerhetskopior. 

   ![Visa säkerhetskopior](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Välj den säkerhetskopia som du vill återställa och ange det nya databasnamnet.

   ![återställ](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klicka på **OK** att återställa databasen från säkerhetskopian i Azure SQL-lagring till den nya databasen.

6. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp över återställningsjobb från valvet](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När Återställningsjobbet har slutförts, öppna den **SQL-databaser** sidan för att se den nyligen återställda databasen.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Använd PowerShell för att konfigurera principer för långsiktig kvarhållning och återställa säkerhetskopior

I följande avsnitt visas hur du använder PowerShell för att konfigurera den långsiktig kvarhållning av säkerhetskopior, visa säkerhetskopior i Azure SQL-lagring och återställning från en säkerhetskopia i Azure SQL-lagring.

> [!IMPORTANT]
> LTR V2 API stöds i följande versioner av PowerShell:
- [I AzureRM.Sql 4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) eller senare
- [AzureRM-6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) eller senare
> 

### <a name="create-an-ltr-policy"></a>Skapa en LTR-princip

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visa LTR-principer
Det här exemplet visar hur du listar LTR-principer i en server

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip
Det här exemplet visar hur du rensar en LTR-princip från en databas

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visa LTR säkerhetskopior

Det här exemplet visar hur du listar LTR-säkerhetskopior i en server. 

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

Det här exemplet visar hur du tar bort en LTR backup från listan över säkerhetskopieringar.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Återställa från säkerhetskopior för LTR
Det här exemplet visar hur du återställer från en LTR-säkerhetskopia. Observera att det här gränssnittet inte har ändrats men resource ID-parametern kräver nu LTR backup-resurs-ID: t. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Härifrån kan du ansluta till den återställda databasen med SQL Server Management Studio för att utföra nödvändiga åtgärder, exempelvis genom att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se [punkt återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
