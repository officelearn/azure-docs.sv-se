---
title: Konfigurera långsiktig kvarhållning av säkerhetskopior – Azure SQL Database | Microsoft Docs
description: Lär dig hur du lagrar automatiska säkerhetskopieringar i Azure Recovery Services-valvet och hur du återställer från Azure Recovery Services-valvet
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma,carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 5100ef307bc125b21e1c42c87856492a4a496065
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455650"
---
# <a name="configure-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurera långsiktig kvarhållning av säkerhetskopior med hjälp av Azure Recovery Services-valv

Du kan konfigurera Azure Recovery Services-valvet för att lagra säkerhetskopior av Azure SQL-databasen och sedan återställa en databas med hjälp av säkerhetskopior som finns kvar i valvet med hjälp av Azure portal eller PowerShell.

> [!NOTE]
> Som en del av den första versionen av förhandsversionen av långsiktig kvarhållning av säkerhetskopior i oktober 2016 lagrades säkerhetskopieringar i Azure-tjänster Recovery Service-valv. Uppdateringen tar bort det här beroendet, men den ursprungliga API stöds för bakåtkompatibilitet till den 31 maj 2018. Om du vill interagera med säkerhetskopior i Azure-tjänster Recovery-valvet kan se [långsiktig kvarhållning av säkerhetskopior med hjälp av Azure-tjänster Recovery Service-valv](sql-database-long-term-backup-retention-configure-vault.md).

## <a name="azure-portal"></a>Azure Portal

I följande avsnitt visas hur du använder Azure-portalen för att konfigurera Azure Recovery Services-valvet, visa säkerhetskopior i valv och återställning från valvet.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Konfigurera valvet, registrera servern och välj databaser

Du konfigurerar ett Azure Recovery Services-valv till [automatiska säkerhetskopieringar bevaras](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivå.

1. Öppna den **SQL Server** för din server.

   ![SQL server-sida](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Klicka på **Long-term backup retention** (Långsiktig kvarhållning av säkerhetskopior).

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. På den **långsiktig kvarhållning av säkerhetskopior** för servern granskar och accepterar villkoren för förhandsversionen (om du har redan gjort det, eller den här funktionen är inte längre i förhandsversion).

   ![acceptera villkoren för förhandsversionen](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Konfigurera långsiktig kvarhållning av säkerhetskopior, välja databasen i rutnätet och klicka sedan på **konfigurera** i verktygsfältet.

   ![välj databas för långsiktig kvarhållning av säkerhetskopior](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. På den **konfigurera** klickar du på **konfigurera nödvändiga inställningar** under **Recovery service-valv**.

   ![länk för att konfigurera valv](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. På den **Recovery services-valv** väljer ett befintligt valv, om det finns några. Om inget Recovery Services-valv hittades för din prenumeration klickar du för att avbryta flödet och skapar ett Recovery Services-valv.

   ![Skapa valvet länk](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. På den **Recovery Services-valv** klickar du på **Lägg till**.

   ![Lägg till valvet länk](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)

8. På den **Recovery Services-valv** anger du ett giltigt namn för Recovery Services-valvet.

   ![namn på nytt valv](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Välj din prenumeration och resursgrupp och välj sedan platsen för valvet. När du är klar klickar du på **Skapa**.

   ![Skapa valv](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Valvet måste finnas i samma region som SQL Database-servern och måste använda samma resursgrupp som SQL Database-servern.

10. När det nya valvet har skapats följer du stegen för att återgå till den **Recovery services-valv** sidan.

11. På den **Recovery services-valv** klickar du på valvet och klicka sedan på **Välj**.

   ![välj befintligt valv](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. På den **konfigurera** sidan, ange ett giltigt namn för den nya kvarhållningsprincipen, ändrar principen efter behov och klicka sedan på **OK**.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

   > [!NOTE]
   > Namnen för kvarhållning av säkerhetskopior principen tillåter inte vissa tecken inklusive blanksteg.

13. På den **långsiktig kvarhållning av säkerhetskopior** för din databas och klicka på **spara** och klicka sedan på **OK** att tillämpa principen för långsiktig kvarhållning av säkerhetskopior på alla valda databaser.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Klicka på **Spara** för att aktivera långsiktig kvarhållning av säkerhetskopior med den här nya principen för Azure Recovery Services-valvet som du har konfigurerat.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> När de har konfigurerats visas säkerhetskopiorna i valvet inom sju dagar. Fortsätt inte den här självstudiekursen förrän säkerhetskopiorna visas i valvet.

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Visa säkerhetskopior i långsiktig kvarhållning av säkerhetskopior med Azure-portalen

Visa information om databassäkerhetskopiorna i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

1. Öppna Azure Recovery Services-valvet av dina databassäkerhetskopior i Azure-portalen (Gå till **alla resurser** och markera den i listan över resurser för din prenumeration) att visa mängden lagringsutrymme som används av din databassäkerhetskopior i den valvet.

   ![visa Recovery Services-valvet med säkerhetskopior](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Öppna den **SQL-databas** sidan för din databas.

   ![nya exempel db-sidan](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Klicka på **Återställ** i verktygsfältet.

   ![verktygsfältet Återställ](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. På sidan återställning **långsiktig**.

5. Under Azure vault backups (Säkerhetskopior i Azure-valv) klickar du på **Välj en säkerhetskopia** för att visa de tillgängliga databassäkerhetskopiorna i långsiktig kvarhållning av säkerhetskopior.

   ![säkerhetskopior i valv](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Återställa en databas från en säkerhetskopia i långsiktig kvarhållning av säkerhetskopior med Azure portal

Du kan återställa databasen till en ny databas från en säkerhetskopia i Azure Recovery Services-valvet.

1. På den **Azure-valvssäkerhetskopior** klickar du på säkerhetskopian för att återställa och klicka sedan på **Välj**.

   ![välj säkerhetskopia i valv](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. I textrutan **Databasnamn** anger du namnet på den återställda databasen.

   ![nytt databasnamn](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Klicka på **OK** för att återställa databasen från säkerhetskopian i valvet till den nya databasen.

4. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp över återställningsjobb från valvet](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När Återställningsjobbet har slutförts, öppna den **SQL-databaser** sidan för att se den nyligen återställda databasen.

   ![återställd databas från valvet](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

I följande avsnitt visas hur du använder PowerShell för att konfigurera Azure Recovery Services-valvet, visa säkerhetskopior i valv och återställning från valvet.

### <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Använd den [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) att skapa ett recovery services-valv.

> [!IMPORTANT]
> Valvet måste finnas i samma region som SQL Database-servern och måste använda samma resursgrupp som SQL Database-servern.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Ange din server recovery-valvet för sina långsiktiga säkerhetskopieringar

Använd den [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) cmdlet för att associera tidigare skapade recovery services-valvet med en specifik Azure SQL-server.

```PowerShell
# Set your server to use the vault to for long-term backup retention

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Skapa en kvarhållningsprincip

I en kvarhållningsprincip anger du hur länge en säkerhetskopia av databasen ska bevaras. Använd den [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject) cmdlet för att hämta den standardkvarhållningsprincip att använda som mall för att skapa principer. I den här mallen anges kvarhållningsperioden i 2 år. Kör den [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) att slutligen skapa principen.

> [!NOTE]
> Vissa cmdlets kräver att du anger valvkontexten innan du kör ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)) så att du ser denna cmdlet i några relaterade fragment. Du anger kontexten eftersom principen är en del av valvet. Du kan skapa flera kvarhållningsprinciper för varje valv och sedan använda önskad princip för specifika databaser.

```PowerShell
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

Använd den [Set AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) cmdlet för att tillämpa den nya principen till en viss databas.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Visa information om säkerhetskopiering och säkerhetskopieringar i långsiktig kvarhållning

Visa information om databassäkerhetskopiorna i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

Använd följande cmdlets för att visa information om säkerhetskopiering:

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
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

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Återställa en databas från en säkerhetskopia i långsiktig kvarhållning av säkerhetskopior

Återställa från långsiktig kvarhållning av säkerhetskopior använder den [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) cmdlet.

```PowerShell
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
> Härifrån kan du ansluta till den återställda databasen med SQL Server Management Studio för att utföra nödvändiga åtgärder, exempelvis genom att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet. Se [punkt återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Hur du rensa säkerhetskopior i Recovery Services-valv

Från och med 1 juli 2018 LTR V1 API: et är inaktuellt och alla befintliga säkerhetskopior i Recovery Service-valv har migrerats till LTR-storage-behållare som hanteras av SQL-databas. För att säkerställa att du inte längre debiteras för de ursprungliga säkerhetskopiorna, har de tagits bort från valv efter migreringen. Om det finns ett lås i ditt valv förblir dock säkerhetskopieringar där. För att undvika onödiga kostnader kan du manuellt ta bort de gamla säkerhetskopiorna från Recovery Service-valv med hjälp av följande skript.

```PowerShell
<#
.EXAMPLE
    .\Drop-LtrV1Backup.ps1 -SubscriptionId “{vault_sub_id}” -ResourceGroup “{vault_resource_group}” -VaultName “{vault_name}”
#>
[CmdletBinding()]
Param (
    [Parameter(Mandatory = $true, HelpMessage="The vault subscription ID")]
    $SubscriptionId,

    [Parameter(Mandatory = $true, HelpMessage="The vault resource group name")]
    $ResourceGroup,

    [Parameter(Mandatory = $true, HelpMessage="The vault name")]
    $VaultName
)

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$vaults = Get-AzureRmRecoveryServicesVault
$vault = $vaults | where { $_.Name -eq $VaultName }

Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL

ForEach ($container in $containers)
{
   $canDeleteContainer = $true
   $ItemCount = 0
   Write-Host "Working on container" $container.Name
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase
   ForEach ($item in $items)
   {
    write-host "Deleting item" $item.name
    Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints -item $item -Force
   }

   Write-Host "Deleting container" $container.Name
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
```

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
- Mer information om hur du återställer från säkerhetskopior finns i avsnittet om hur du [återställer från säkerhetskopior](sql-database-recovery-using-backups.md)
