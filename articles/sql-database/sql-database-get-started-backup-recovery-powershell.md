---
title: "Powershell: Säkerhetskopiera och återställ med Azure SQL Database | Microsoft-dokument"
description: "Den här självstudiekursen förklarar hur du återställer data från automatiska säkerhetskopieringar till en tidigare tidpunkt, hur du lagrar automatiska säkerhetskopieringar i Azure Recovery Services-valvet och hur du återställer från Azure Recovery Services-valvet med Azure PowerShell"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 93efe1a08149e7c027830b03a9e426ac5a05b27b
ms.openlocfilehash: 8a3ede8af471e656e830e38e0cf2f3a909fdaadb


---


# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-powershell"></a>Självstudie: Säkerhetskopiera och återställ en Azure SQL-databas med hjälp av PowerShell

I den här självstudien kommer du att lära dig att använda Azure PowerShell för att göra följande:

- Visa befintliga säkerhetskopieringar av en databas
- Återställa en databas till en tidigare tidpunkt
- Konfigurera långsiktig kvarhållning av en säkerhetskopia av en databas i Azure Recovery Services-valvet
- Återställa en databas från Azure Recovery Services-valvet

**Uppskattad tidsåtgång**: Den här kursen tar cirka 30 minuter att slutföra (förutsatt att du redan har uppfyllt kraven).


## <a name="prerequisites"></a>Krav

* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Du måste ansluta till Azure med ett konto som är medlem i rollen som prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

* Du behöver ha den senaste Azure PowerShell installerad och igång. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

* Du har slutfört [Komma igång med Azure SQL Database-servrar, databaser och brandväggsregler med hjälp av Azure Portal och SQL Server Management Studio](sql-database-get-started.md) eller motsvarande [PowerShell-version](sql-database-get-started-powershell.md). Om du inte har gjort det går du kursen eller kör PowerShell-skriptet i slutet av [PowerShell-versionen](sql-database-get-started-powershell.md) innan du fortsätter.

> [!TIP]
> Du kan utföra samma uppgifter i en ”komma igång”-självstudiekurs med hjälp av [Azure Portal](sql-database-get-started-backup-recovery-portal.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Visa den äldsta återställningspunkten från de tjänstgenererade säkerhetskopiorna av en databas

I det här avsnittet av självstudiekursen visar du information om den senaste återställningspunkten från de [tjänstgenererade automatiska säkerhetskopiorna](sql-database-automated-backups.md) av din databas. 

Du kan återställa en databas till en tidpunkt mellan den tidigaste återställningspunkten och den senaste tillgängliga säkerhetskopieringen (6 minuter före den aktuella tiden). 

I följande kodfragment används cmdlet [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) för att hämta den tidigaste återställningspunkten för den databas du vill återställa. Tiden returneras som UTC, men följande kodfragment visar hur du arbetar i lokal tid. Den senaste tillgängliga återställningspunkten på en live-databas är vanligtvis för ungefär sex minuter sedan, så den senaste återställningspunkten anges bara till den aktuella tiden minus sex minuter. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt

I det här avsnittet av självstudiekursen ska du återställa databasen till en ny databas från en viss tidigare tidpunkt. 

>[!NOTE]
>Du kan inte kan ändra den server till vilken du återställer till en specifik tidpunkt. Om du vill återställa till en annan server använder du [geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Observera även att du kan återställa till en [Elastic Database-pool](sql-database-elastic-jobs-overview.md) eller till en annan prisnivå. 

I följande kodfragment används cmdlet [Restore AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) för att återställa $databaseToRestore som vi angav i föregående fragment. Parametern **-PointInTime** kräver ett UTC-formaterat tidsvärde som liknar: *12/09/2016 20:00:00*. I kodfragmentet konverteras den lokala tiden för dig.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) för att utföra nödvändiga åtgärder, till exempel för att [extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Konfigurera långsiktig kvarhållning av automatiska säkerhetskopieringar i ett Azure Recovery Services-valv 

I det här avsnittet av självstudiekursen ska du [konfigurera ett Azure Recovery Services-valv så att automatiska säkerhetskopieringar bevaras](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivå (upp till 10 år). 


> [!TIP]
> Mer information om borttagning av säkerhetskopior i långsiktig kvarhållning finns i [Hantera långsiktig kvarhållning av säkerhetskopior med PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).


### <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

> [!IMPORTANT]
> Valvet måste finnas i samma region som den logiska Azure SQL-servern och måste använda samma resursgrupp som den logiska servern.

I följande kodfragment används cmdlet [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) och [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) för att skapa ett nytt Recovery Services-valv och ange redundansnivån för säkerhetskopieringar i valvet. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>Ange att din server använder det recovery-valv som du precis har skapat för sina långsiktiga säkerhetskopieringar

I följande kodfragment används cmdlet [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) för att associera tidigare skapade recovery services-valv med en specifik Azure SQL-server.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Skapa en kvarhållningsprincip

I en kvarhållningsprincip anger du hur länge en säkerhetskopia av databasen ska bevaras. 

I följande kodfragment används [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) för att hämta den standardkvarhållningsprincip som vi använder som mall för att skapa nya principer. Vi använder våra mall för att behålla vår säkerhetskopiering i 2 år och kör sedan [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy) för att slutligen skapa vår nya princip. 

Observera att vissa cmdlets kräver att du anger valvkontexten innan du kör ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)) så du ser denna cmdlet i några relaterade fragment. Vi anger kontexten eftersom principen är en del av valvet. Du kan skapa flera kvarhållningsprinciper för varje valv och sedan använda önskad princip för specifika databaser. 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Konfigurera en databas om du vill använda den tidigare definierade kvarhållningsprincipen

I följande kodfragment används cmdlet [Set AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) för att använda våra nya princip till en viss databas.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> När de har konfigurerats visas säkerhetskopiorna i valvet inom sju dagar. Fortsätt den här självstudiekursen efter att säkerhetskopiorna visas i valvet.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>Visa information om säkerhetskopiering och säkerhetskopieringar i långsiktig kvarhållning

I det här avsnittet av självstudiekursen ska du visa information om databassäkerhetskopiorna i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md). 

Följande kodfragment frågar om information från valvet med hjälp av cmdlet:arna [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) och [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint).

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Återställa en databas från en säkerhetskopia i långsiktig kvarhållning av säkerhetskopior

I det här avsnittet av självstudiekursen ska du återställa databasen till en ny databas från en säkerhetskopia i Azure Recovery Services-valvet.

Precis som fragment som baseras på tidpunkt tidigare i den här handledningen, används vid återställning från säkerhetskopia för långsiktig kvarhållning cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase), förutom att den här gången används parametern **- FromLongTermRetentionBackup** (och inte parametern **- FromPointInTimeBackup** som använts tidigare).

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>Slutför Azure PowerShell-skript för att återställa från en tidigare tidpunkt, och för att konfigurera och återställa från säkerhetskopiering för långsiktig kvarhållning med PowerShell

> [!NOTE]
> När du försöker återställa den senaste säkerhetskopian i slutet av det här skriptet får du undantaget *Kan inte indexeras till en null-matris* om det inte finns någon säkerhetskopia i valvet.

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
- Mer information om hur du återställer från säkerhetskopior finns i avsnittet om hur du [återställer från säkerhetskopior](sql-database-recovery-using-backups.md)



<!--HONumber=Feb17_HO3-->


