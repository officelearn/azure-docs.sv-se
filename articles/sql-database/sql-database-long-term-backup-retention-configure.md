---
title: Hantera Azure SQL Database långsiktig kvarhållning av säkerhets kopior | Microsoft Docs
description: Lär dig hur du lagrar automatiska säkerhets kopieringar i SQL Azure lagring och sedan återställer dem
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
ms.date: 08/21/2019
ms.openlocfilehash: b90e364442e46269fc949ef4aecd9a756cff5595
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904628"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Hantera Azure SQL Database långsiktig kvarhållning av säkerhets kopior

I Azure SQL Database kan du konfigurera en enskild databas eller en pool med en [långsiktig säkerhets kopierings](sql-database-long-term-retention.md) princip (LTR) för att automatiskt behålla databas säkerhets kopiorna i separata Azure Blob Storage-behållare i upp till 10 år. Du kan sedan återställa en databas med hjälp av de här säkerhets kopiorna med hjälp av Azure Portal eller PowerShell.

> [!IMPORTANT]
> [Azure SQL Database hanterade](sql-database-managed-instance.md) instansen stöder för närvarande inte långsiktig kvarhållning av säkerhets kopior.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>Använd Azure Portal för att hantera långsiktiga säkerhets kopior

I följande avsnitt visar vi hur du använder Azure Portal för att konfigurera långsiktig kvarhållning, Visa säkerhets kopior i långsiktig kvarhållning och återställa säkerhets kopian från långsiktig kvarhållning.

### <a name="configure-long-term-retention-policies"></a>Konfigurera principer för långsiktig kvarhållning

Du kan konfigurera SQL Database att [behålla automatiserade säkerhets kopieringar](sql-database-long-term-retention.md) under en längre tid än kvarhållningsperioden för din tjänst nivå. 

1. I Azure Portal väljer du din SQL-Server och klickar sedan på **hantera säkerhets kopieringar**. På fliken **Konfigurera principer** markerar du *kryss rutan för den databas där du vill ange eller ändra bevarande principer för långsiktig säkerhets kopiering*. Om kryss rutan bredvid databasen inte är markerad gäller inte ändringarna för principen för den databasen.  

   ![hantera säkerhets kopierings länk](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. I fönstret **Konfigurera principer** väljer du om du vill behålla en veckovis, månatlig eller årlig säkerhets kopiering och ange kvarhållningsperioden för varje. 

   ![konfigurera principer](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. När du är klar klickar du på **Använd**.

> [!IMPORTANT]
> När du aktiverar en långsiktig bevarande princip för säkerhets kopiering kan det ta upp till sju dagar innan den första säkerhets kopieringen blir synlig och tillgänglig för återställning. Mer information om säkerhets kopierings cadance finns i [långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visa säkerhets kopior och återställning från en säkerhets kopia med Azure Portal

Visa säkerhets kopiorna som bevaras för en speciell databas med en LTR-princip och Återställ från dessa säkerhets kopior. 

1. I Azure Portal väljer du din SQL-Server och klickar sedan på **hantera säkerhets kopieringar**. På fliken **tillgängliga säkerhets kopior** väljer du den databas som du vill se tillgängliga säkerhets kopior för.

   ![Välj databas](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Granska tillgängliga säkerhets kopior i fönstret **tillgängliga säkerhets kopior** . 

   ![Visa säkerhets kopior](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Välj den säkerhets kopia som du vill återställa från och ange sedan det nya databas namnet.

   ![återställ](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klicka på **OK** för att återställa databasen från säkerhets kopian i Azure SQL Storage till den nya databasen.

6. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp för återställningsjobb](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När återställnings jobbet har slutförts öppnar du sidan **SQL-databaser** för att visa den nyligen återställda databasen.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-manage-long-term-backups"></a>Använd PowerShell för att hantera långsiktiga säkerhets kopieringar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Följande avsnitt visar hur du använder PowerShell för att konfigurera långsiktig kvarhållning av säkerhets kopior, Visa säkerhets kopior i Azure SQL-lagring och återställa från en säkerhets kopia i Azure SQL Storage.


### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-roller för att hantera långsiktig kvarhållning

För **Get-AzSqlDatabaseLongTermRetentionBackup** och **restore-AzSqlDatabase**måste du ha en av följande roller:

- Prenumerations ägarens roll eller
- SQL Server deltagar roll eller
- Anpassad roll med följande behörigheter:

   Microsoft. SQL/locations/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/läsa
 
För **Remove-AzSqlDatabaseLongTermRetentionBackup**måste du ha en av följande roller:

- Prenumerations ägarens roll eller
- Anpassad roll med följande behörighet:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


> [!NOTE]
> Rollen SQL Server Contributor har inte behörighet att ta bort LTR-säkerhetskopieringar.

RBAC-behörigheter kan beviljas i antingen *prenumerations* -eller *resurs grupps* omfång. Men för att få åtkomst till LTR-säkerhets kopieringar som tillhör en tappad Server måste behörigheten beviljas i prenumerations omfånget för den servern.


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
Det här exemplet visar hur du visar en lista över LTR-principer inom en server

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip
Det här exemplet visar hur du tar bort en LTR-princip från en databas

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visa LTR-säkerhetskopieringar

I det här exemplet visas hur du visar en lista över säkerhets kopior på en server. 

```powershell
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Ta bort LTR-säkerhetskopieringar

Det här exemplet visar hur du tar bort en LTR-säkerhetskopiering från listan över säkerhets kopior.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Borttagning av LTR-säkerhetskopiering går inte att ångra. Om du vill ta bort en LTR-säkerhetskopiering efter att servern har tagits bort måste du ha behörighet för prenumerations omfång. Du kan ställa in aviseringar om varje borttagning i Azure Monitor genom filtrering för åtgärd, tar bort en säkerhets kopia av långsiktig kvarhållning. Aktivitets loggen innehåller information om vem och när du har gjort begäran. Mer information finns i [skapa aktivitets logg aviseringar](../azure-monitor/platform/alerts-activity-log.md) .
>

### <a name="restore-from-ltr-backups"></a>Återställa från LTR-säkerhetskopieringar
Det här exemplet visar hur du återställer från en LTR-säkerhetskopiering. Obs! det här gränssnittet ändrades inte, men parametern resurs-ID kräver nu resurs-ID: t för säkerhets kopiering. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Om du vill återställa från en LTR-säkerhetskopiering när servern har tagits bort, måste du ha behörighet som är begränsad till serverns prenumeration och prenumerationen måste vara aktiv. Du måste också utelämna den valfria-ResourceGroupName-parametern.  
>

> [!NOTE]
> Härifrån kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel för att extrahera en bit data från den återställda databasen för att kopiera till den befintliga databasen eller ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databas namnet. Se tidpunkt [för återställning](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
