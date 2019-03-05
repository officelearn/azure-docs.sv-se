---
title: Hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior | Microsoft Docs
description: Lär dig hur du lagrar automatiska säkerhetskopieringar i SQL Azure-lagring och sedan återställa dem.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/07/2019
ms.openlocfilehash: 3594ff05e0f58671e8566b3b69972de32a58855a
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308456"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior

I Azure SQL Database kan du konfigurera en enda eller en databas i pool med en [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) princip (LTR) för att automatiskt behålla säkerhetskopior i Azure Blob storage för upp till 10 år. Du kan sedan återställa en databas med hjälp av dessa säkerhetskopior med hjälp av Azure portal eller PowerShell.

> [!IMPORTANT]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) stöder för närvarande inte långsiktig kvarhållning av säkerhetskopior.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Använda Azure portal för att konfigurera principer för långsiktig kvarhållning och återställa säkerhetskopior

I följande avsnitt visas hur du använder Azure-portalen för att konfigurera långsiktig kvarhållning, visa säkerhetskopior i långsiktig kvarhållning och återställa säkerhetskopia från långsiktig kvarhållning.

### <a name="configure-long-term-retention-policies"></a>Konfigurera principer för långsiktig kvarhållning

Du kan konfigurera SQL-databas till [automatiska säkerhetskopieringar bevaras](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivå. 

1. Välj din SQL-server i Azure-portalen och klicka sedan på **hantera säkerhetskopior**. På den **konfigurera principer för** fliken *markerar du kryssrutan för databasen som du vill ange eller ändra principer för långsiktig kvarhållning av säkerhetskopior*. Om kryssrutan bredvid databasen inte är markerad gäller inte ändringarna för principen för den databasen.  

   ![hantera säkerhetskopior länk](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. I den **konfigurera principer för** fönstret, Välj om vill behålla säkerhetskopior per månad eller år i varje vecka, och ange kvarhållningsperioden för var och en. 

   ![Konfigurera principer](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. När du är klar klickar du på **tillämpa**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visa säkerhetskopior och återställa från en säkerhetskopia med hjälp av Azure portal

Visa säkerhetskopior som finns kvar för en viss databas med LTR-princip och återställning från dessa säkerhetskopior. 

1. Välj din SQL-server i Azure-portalen och klicka sedan på **hantera säkerhetskopior**. På den **tillgängliga säkerhetskopior** väljer du en databas som du vill se tillgängliga säkerhetskopior.

   ![Välj databas](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. I den **tillgängliga säkerhetskopior** fönstret Granska tillgängliga säkerhetskopior. 

   ![Visa säkerhetskopior](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Välj den säkerhetskopia som du vill återställa och ange det nya databasnamnet.

   ![återställ](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klicka på **OK** att återställa databasen från säkerhetskopian i Azure SQL-lagring till den nya databasen.

6. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp för återställningsjobb](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När Återställningsjobbet har slutförts, öppna den **SQL-databaser** sidan för att se den nyligen återställda databasen.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Använd PowerShell för att konfigurera principer för långsiktig kvarhållning och återställa säkerhetskopior

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I följande avsnitt visas hur du använder PowerShell för att konfigurera den långsiktig kvarhållning av säkerhetskopior, visa säkerhetskopior i Azure SQL-lagring och återställning från en säkerhetskopia i Azure SQL-lagring.


### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-roller för att hantera långsiktig kvarhållning

Hantera säkerhetskopior av LTR ska du kunna vara 
- Prenumerationens ägare eller
- SQL Server-rollen deltagare i **prenumeration** omfång eller
- SQL-databas deltagarrollen i **prenumeration** omfång

Om det krävs mer detaljerad kontroll, kan du skapa anpassade RBAC-roller och tilldela dem i **prenumeration** omfång. 

För **Get-AzSqlDatabaseLongTermRetentionBackup** och **återställning AzSqlDatabase** rollen måste ha följande behörigheter:

Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/diskläsning
 
För **Remove-AzSqlDatabaseLongTermRetentionBackup** rollen måste du ha följande behörigheter:

Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


### <a name="create-an-ltr-policy"></a>Skapa en LTR-princip

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visa LTR-principer
Det här exemplet visar hur du listar LTR-principer i en server

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip
Det här exemplet visar hur du rensar en LTR-princip från en databas

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visa LTR säkerhetskopior

Det här exemplet visar hur du listar LTR-säkerhetskopior i en server. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Ta bort LTR säkerhetskopior

Det här exemplet visar hur du tar bort en LTR backup från listan över säkerhetskopieringar.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Tar bort LTR är backup icke-reversibelt. Du kan ställa in meddelanden varje tas bort i Azure Monitor genom att filtrera för åtgärden 'Tar bort en långsiktig kvarhållning av säkerhetskopia'. Aktivitetsloggen innehåller information om vem och när gjorde begäran. Se [skapa aviseringar för aktivitetsloggen](../azure-monitor/platform/alerts-activity-log.md) detaljerade anvisningar.
>

### <a name="restore-from-ltr-backups"></a>Återställa från säkerhetskopior för LTR
Det här exemplet visar hur du återställer från en LTR-säkerhetskopia. Observera att det här gränssnittet inte har ändrats men resource ID-parametern kräver nu LTR backup-resurs-ID: t. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Härifrån kan du ansluta till den återställda databasen med SQL Server Management Studio för att utföra nödvändiga åtgärder, exempelvis genom att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se [punkt återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
