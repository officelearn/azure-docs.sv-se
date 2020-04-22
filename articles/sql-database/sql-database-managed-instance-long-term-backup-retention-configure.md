---
title: 'Hanterad instans: Lagring av säkerhetskopiering för långsiktig säkerhetskopiering (PowerShell)'
description: Lär dig hur du lagrar och återställer automatiska säkerhetskopior på separata Azure Blob-lagringsbehållare för en hanterad Azure SQL-databas-hanterad instans med PowerShell.
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
ms.date: 04/19/2020
ms.openlocfilehash: 24eacb555704593fe44bc2d949de44de163345bc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677106"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Hantera Azure SQL Database-hanterad instans för långsiktig lagring av säkerhetskopiering (PowerShell)

I Azure SQL Database-hanterad instans kan du konfigurera en [långsiktig bevarandeprincip](sql-database-long-term-retention.md#managed-instance-support) för säkerhetskopiering (LTR) som en begränsad offentlig förhandsversionsfunktion. På så sätt kan du automatiskt behålla säkerhetskopieringar av databaser i separata Azure Blob-lagringsbehållare i upp till 10 år. Du kan sedan återställa en databas med hjälp av dessa säkerhetskopior med PowerShell.

   > [!IMPORTANT]
   > LTR för hanterade instanser är för närvarande i begränsad förhandsversion och tillgänglig för EA- och CSP-prenumerationer från fall till fall. Om du vill begära registrering skapar du en [Azure-supportbiljett](https://azure.microsoft.com/support/create-ticket/) under supportavsnittet **Säkerhetskopiering, återställning och lagring av affärskontinuitet/långsiktig säkerhetskopiering**. 


Följande avsnitt visar hur du använder PowerShell för att konfigurera långsiktig lagring av säkerhetskopiering, visa säkerhetskopior i Azure SQL-lagring och återställa från en säkerhetskopia i Azure SQL-lagring.

## <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-roller för att hantera långsiktig kvarhållning

För **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** och **Restore-AzSqlInstanceDatabase**måste du ha en av följande roller:

- Rollen Prenumerationsägare eller
- Rollen Hanterad instansdeltagare eller
- Anpassad roll med följande behörigheter:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

För **Ta bort-AzSqlInstanceDatabaseLongTermRetentionBackup**måste du ha någon av följande roller:

- Rollen Prenumerationsägare eller
- Anpassad roll med följande behörighet:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Rollen Hanterad instansdeltagare har inte behörighet att ta bort LTR-säkerhetskopior.

RBAC-behörigheter kan beviljas i antingen *prenumerations-* eller *resursgruppsomfattning.* För att komma åt LTR-säkerhetskopior som tillhör en bortsläppt instans måste dock behörigheten beviljas i prenumerationsomfånget för den instansen. *subscription*

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Skapa en LTR-princip

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>Visa LTR-principer

Det här exemplet visar hur du listar LTR-principerna i en instans

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Rensa en LTR-princip

I det här exemplet visas hur du rensar en LTR-princip från en databas

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Visa LTR-säkerhetskopior

Det här exemplet visar hur du listar LTR-säkerhetskopior i en instans.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>Ta bort LTR-säkerhetskopior

Det här exemplet visar hur du tar bort en LTR-säkerhetskopia från listan över säkerhetskopior.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Det går inte att ta bort LTR-säkerhetskopieringen. Om du vill ta bort en LTR-säkerhetskopia när instansen har tagits bort måste du ha behörigheten Prenumerationsomfattning. Du kan ställa in meddelanden om varje borttagning i Azure Monitor genom att filtrera för åtgärden Tar bort en långsiktig bevarandesäkerhetskopiering. Aktivitetsloggen innehåller information om vem och när begäran görs. Se [Skapa aktivitetsloggaviseringar](../azure-monitor/platform/alerts-activity-log.md) för detaljerade instruktioner.

## <a name="restore-from-ltr-backups"></a>Återställa från LTR-säkerhetskopior

Det här exemplet visar hur du återställer från en LTR-säkerhetskopia. Det här gränssnittet ändrades inte, men resurs-ID-parametern kräver nu LTR-säkerhetskopieringsresurs-ID.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Om du vill återställa från en LTR-säkerhetskopia när instansen har tagits bort måste du ha behörigheter begränsade till instansens prenumeration och att prenumerationen måste vara aktiv. Du måste också utelämna parametern -ResourceGroupName som tillval.

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se [tidsåterställning](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
