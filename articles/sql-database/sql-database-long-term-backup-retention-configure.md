---
title: 'Enkel databas: Hantera långsiktig lagring av säkerhetskopiering'
description: Lär dig hur du lagrar och återställer automatiska säkerhetskopior för en enkel eller poolad Azure SQL-databas i Azure-lagring (i upp till 10 år) med hjälp av Azure Portal och PowerShell
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
ms.date: 04/14/2020
ms.openlocfilehash: 2564fd0ffd980dae4ca1835f4211fee0a0cf040c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380925"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Hantera Azure SQL Database långtidslagring av säkerhetskopiering

I Azure SQL Database kan du konfigurera en enda eller en poolad databas med en [långsiktig bevarandeprincip](sql-database-long-term-retention.md) för säkerhetskopiering (LTR) för att automatiskt behålla databassäkerhetskopior i separata Azure Blob-lagringsbehållare i upp till 10 år. Du kan sedan återställa en databas med hjälp av dessa säkerhetskopior med Azure-portalen eller PowerShell.

> [!IMPORTANT]
> [Azure SQL-databashanterad instans](sql-database-managed-instance.md) stöder för närvarande inte långsiktig lagring av säkerhetskopiering.

## <a name="using-azure-portal"></a>Använda Azure Portal

Följande avsnitt visar hur du använder Azure-portalen för att konfigurera långsiktig kvarhållning, visa säkerhetskopior i långsiktig kvarhållning och återställa säkerhetskopiering från långsiktig kvarhållning.

### <a name="configure-long-term-retention-policies"></a>Konfigurera principer för långtidslagring

Du kan konfigurera SQL Database så att [automatiska säkerhetskopior behålls](sql-database-long-term-retention.md) under en längre period än kvarhållningsperioden för tjänstnivån.

1. I Azure-portalen väljer du din SQL-server och klickar sedan på **Hantera säkerhetskopior**. Markera kryssrutan för den databas där du vill ange eller ändra långsiktiga principer för lagring av säkerhetskopiering på fliken **Konfigurera principer.** Om kryssrutan bredvid databasen inte är markerad gäller inte ändringarna för principen för databasen.  

   ![hantera länk för säkerhetskopior](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. I fönstret **Konfigurera principer** väljer du om du vill behålla veckovisa, månatliga eller årliga säkerhetskopior och ange kvarhållningsperioden för varje.

   ![konfigurera principer](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. När du är klar klickar du på **Använd**.

> [!IMPORTANT]
> När du aktiverar en långsiktig princip för lagring av säkerhetskopiering kan det ta upp till 7 dagar innan den första säkerhetskopian blir synlig och tillgänglig för återställning. Mer information om LTR-säkerhetskopieringskadancen finns i [långsiktig kvarhållning av säkerhetskopiering](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Visa säkerhetskopior och återställa från en säkerhetskopia

Visa de säkerhetskopior som behålls för en viss databas med en LTR-princip och återställa från dessa säkerhetskopior.

1. I Azure-portalen väljer du din SQL-server och klickar sedan på **Hantera säkerhetskopior**. På fliken **Tillgängliga säkerhetskopior** väljer du den databas som du vill visa tillgängliga säkerhetskopior för.

   ![välj databas](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. Granska tillgängliga säkerhetskopior i fönstret **Tillgängliga säkerhetskopior.**

   ![visa säkerhetskopior](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Välj den säkerhetskopia som du vill återställa och ange sedan det nya databasnamnet.

   ![återställ](./media/sql-database-long-term-retention/ltr-restore.png)

1. Klicka på **OK** om du vill återställa databasen från säkerhetskopian i Azure SQL-lagring till den nya databasen.

1. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp för återställningsjobb](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. När återställningsjobbet är slutfört öppnar du sidan **SQL-databaser** för att visa den nyligen återställda databasen.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Använda PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Följande avsnitt visar hur du använder PowerShell för att konfigurera långsiktig lagring av säkerhetskopiering, visa säkerhetskopior i Azure SQL-lagring och återställa från en säkerhetskopia i Azure SQL-lagring.

### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-roller för att hantera långsiktig kvarhållning

För **Get-AzSqlDatabaseLongTermRetentionBackup** och **Restore-AzSqlDatabase**måste du ha en av följande roller:

- Rollen Prenumerationsägare eller
- SQL Server-deltagarroll eller
- Anpassad roll med följande behörigheter:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentiones/longTermRetentiones/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentiones/longTermRetentionEs/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentiones/longTermRetentionEs/longTermRetentionBackups/longTermRetentionBackups/longTermRetentionDatabass/longTermRetentiones/longTermRetentionEs/longTermRetentionRetionEs/longTermRetentionDatabas/longTermRetentionBackups/read Microsoft.Sql/locations/longTerm

För **Ta bort-AzSqlDatabaseLongTermRetentionBackup**måste du ha någon av följande roller:

- Rollen Prenumerationsägare eller
- Anpassad roll med följande behörighet:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Rollen SQL Server Contributor har inte behörighet att ta bort LTR-säkerhetskopior.

RBAC-behörigheter kan beviljas i antingen *prenumerations-* eller *resursgruppsomfattning.* För att komma åt LTR-säkerhetskopior som tillhör en bortsläppt server måste behörigheten beviljas i *serverns* prenumerationsomfång.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Skapa en LTR-princip

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visa LTR-principer

I det här exemplet visas hur du listar LTR-principerna på en server

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip

I det här exemplet visas hur du rensar en LTR-princip från en databas

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visa LTR-säkerhetskopior

I det här exemplet visas hur du listar LTR-säkerhetskopior i en server.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Ta bort LTR-säkerhetskopior

Det här exemplet visar hur du tar bort en LTR-säkerhetskopia från listan över säkerhetskopior.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Det går inte att ta bort LTR-säkerhetskopieringen. Om du vill ta bort en LTR-säkerhetskopia när servern har tagits bort måste du ha behörigheten Prenumerationsomfattning. Du kan ställa in meddelanden om varje borttagning i Azure Monitor genom att filtrera för åtgärden Tar bort en långsiktig bevarandesäkerhetskopiering. Aktivitetsloggen innehåller information om vem och när begäran görs. Se [Skapa aktivitetsloggaviseringar](../azure-monitor/platform/alerts-activity-log.md) för detaljerade instruktioner.

### <a name="restore-from-ltr-backups"></a>Återställa från LTR-säkerhetskopior

Det här exemplet visar hur du återställer från en LTR-säkerhetskopia. Det här gränssnittet ändrades inte, men resurs-ID-parametern kräver nu LTR-säkerhetskopieringsresurs-ID.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Om du vill återställa från en LTR-säkerhetskopia när servern har tagits bort måste du ha behörigheter begränsade till serverns prenumeration och den prenumerationen måste vara aktiv. Du måste också utelämna parametern -ResourceGroupName som tillval.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se [tidsåterställning](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
