---
title: Konfigurera långsiktig lagring av säkerhetskopior. - Azure SQL Database | Microsoft Docs
description: Lär dig hur för automatiska säkerhetskopieringar i Azure Recovery Services-valvet och att återställa från Azure Recovery Services-valvet
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 05/08/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f26f9b6f1b0ddef348c39df5aa4badc5df93b725
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurera och återställa från Azure SQL Database långsiktig lagring av säkerhetskopior med hjälp av Azure Recovery Services-valv

Du kan konfigurera Azure Recovery Services-valvet för att lagra säkerhetskopiorna av Azure SQL-databasen och sedan återställa en databas med hjälp av säkerhetskopior som lagras i valvet med Azure-portalen eller PowerShell.

> [!NOTE]
> Som en del av den första versionen av förhandsversionen för långsiktig lagring av säkerhetskopior i oktober 2016 lagras säkerhetskopior i Azure Recovery Services-tjänsten-valvet. Den här uppdateringen tar bort den här beroende, men för bakåtkompatibilitet ursprungliga API stöds förrän den 31 maj 2018. Om du behöver interagera med säkerhetskopieringar i Azure Services Recovery-valvet finns [långsiktig lagring av säkerhetskopior med hjälp av Azure Recovery Services-tjänsten valvet](sql-database-long-term-backup-retention-configure-vault.md). 


## <a name="azure-portal"></a>Azure Portal

Följande avsnitt visar hur du använder Azure-portalen för att konfigurera Azure Recovery Services-valvet, visa säkerhetskopieringar i valvet och återställning från valvet.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Konfigurera valvet, registrera servern och välj databaser

Du konfigurerar Azure Recovery Services-valv som [behålla automatiska säkerhetskopieringar](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivån. 

1. Öppna den **SQL Server** för servern.

   ![SQL server-sida](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Klicka på **Long-term backup retention** (Långsiktig kvarhållning av säkerhetskopior).

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. På den **långsiktig lagring av säkerhetskopior** för servern, granska och Godkänn förhandsversionen villkor (om du redan har gjort det - eller den här funktionen är inte längre i preview).

   ![acceptera villkoren för förhandsversionen](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Om du vill konfigurera långsiktig lagring av säkerhetskopior Välj databasen i rutnätet och klicka sedan på **konfigurera** i verktygsfältet.

   ![välj databas för långsiktig kvarhållning av säkerhetskopior](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. På den **konfigurera** klickar du på **konfigurera nödvändiga inställningar** under **Recovery service-valvet**.

   ![länk för att konfigurera valv](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. På den **återställningstjänstvalvet** väljer en befintlig valvet, om sådana finns. Om inget Recovery Services-valv hittades för din prenumeration klickar du för att avbryta flödet och skapar ett Recovery Services-valv.

   ![Skapa valv länk](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. På den **Recovery Services-valv** klickar du på **Lägg till**.

   ![Lägg till valvet länk](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. På den **Recovery Services-valvet** anger ett giltigt namn för Recovery Services-valvet.

   ![namn på nytt valv](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Välj din prenumeration och resursgrupp och välj sedan platsen för valvet. När du är klar klickar du på **Skapa**.

   ![Skapa valv](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Valvet måste finnas i samma region som den logiska Azure SQL-servern och måste använda samma resursgrupp som den logiska servern.
   >

10. När det nya valvet har skapats kan köra de nödvändiga stegen för att återgå till den **återställningstjänstvalvet** sidan.

11. På den **återställningstjänstvalvet** klickar du på valvet och klicka sedan på **Välj**.

   ![välj befintligt valv](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. På den **konfigurera** sidan, ange ett giltigt namn för den nya bevarandeprincipen, ändra standardprincipen för kvarhållning efter behov och klicka sedan på **OK**.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)
   
   >[!NOTE]
   >Kvarhållning principens namn kan inte vissa tecken inklusive blanksteg.

13. På den **långsiktig lagring av säkerhetskopior** för din databas, klickar du på **spara** och klicka sedan på **OK** vill tillämpa principen för långsiktig lagring av säkerhetskopior för alla valda databaser.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Klicka på **Spara** för att aktivera långsiktig kvarhållning av säkerhetskopior med den här nya principen för Azure Recovery Services-valvet som du har konfigurerat.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> När de har konfigurerats visas säkerhetskopiorna i valvet inom sju dagar. Fortsätt inte den här självstudiekursen förrän säkerhetskopiorna visas i valvet.
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Visa säkerhetskopior i långsiktig kvarhållning med hjälp av Azure portal

Visa information om din databas säkerhetskopior i [långsiktig lagring av säkerhetskopior](sql-database-long-term-retention.md). 

1. Öppna Azure Recovery Services-valv för databassäkerhetskopiorna i Azure-portalen (Gå till **alla resurser** och markera den i listan över resurser för din prenumeration) att visa hur mycket lagringsutrymme som används av databassäkerhetskopiorna i den valvet.

   ![visa Recovery Services-valvet med säkerhetskopior](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Öppna den **SQL-databas** för din databas.

   ![nya exempelsida db](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Klicka på **Återställ** i verktygsfältet.

   ![verktygsfältet Återställ](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. På sidan återställning **långsiktiga**.

5. Under Azure vault backups (Säkerhetskopior i Azure-valv) klickar du på **Välj en säkerhetskopia** för att visa de tillgängliga databassäkerhetskopiorna i långsiktig kvarhållning av säkerhetskopior.

   ![säkerhetskopior i valv](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Återställa en databas från en säkerhetskopia i långsiktig lagring av säkerhetskopior med Azure-portalen

Du kan återställa databasen till en ny databas från en säkerhetskopia i Azure Recovery Services-valvet.

1. På den **Azure valvet säkerhetskopieringar** klickar du på säkerhetskopian för att återställa och klicka sedan på **Välj**.

   ![välj säkerhetskopia i valv](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. I textrutan **Databasnamn** anger du namnet på den återställda databasen.

   ![nytt databasnamn](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Klicka på **OK** för att återställa databasen från säkerhetskopian i valvet till den nya databasen.

4. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp över återställningsjobb från valvet](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När Återställningsjobbet har slutförts, öppna den **SQL-databaser** sidan kan du visa den nyligen återställda databasen.

   ![återställd databas från valvet](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

Följande avsnitt visar hur du använder PowerShell för att konfigurera Azure Recovery Services-valvet, visa säkerhetskopieringar i valvet och återställning från valvet.

### <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Använd den [ny AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) att skapa ett recovery services-valv.

> [!IMPORTANT]
> Valvet måste finnas i samma region som den logiska Azure SQL-servern och måste använda samma resursgrupp som den logiska servern.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Ange din server recovery-valvet för dess långsiktig kvarhållning säkerhetskopieringar

Använd den [Set AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) för att associera en tidigare skapad recovery services-ventilen med en specifik Azure SQL-server.

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Skapa en kvarhållningsprincip

I en kvarhållningsprincip anger du hur länge en säkerhetskopia av databasen ska bevaras. Använd den [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) för att hämta den standard bevarandeprincip som ska användas som mall för att skapa principer. I den här mallen anges kvarhållningsperioden i två år. Kör den [ny AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) slutligen skapa principen. 

> [!NOTE]
> Vissa cmdletar kräver att du ställer in valvet kontexten innan du kör ([Set AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)) så att du ser denna cmdlet i några relaterade kodavsnitt. Du kan ange kontexten eftersom principen ingår i valvet. Du kan skapa flera kvarhållningsprinciper för varje valv och sedan använda önskad princip för specifika databaser. 


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

Använd den [Set AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) för att använda den nya principen till en viss databas.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Visa information om säkerhetskopiering och säkerhetskopieringar i långsiktig kvarhållning

Visa information om din databas säkerhetskopior i [långsiktig lagring av säkerhetskopior](sql-database-long-term-retention.md). 

Använd följande cmdletar för att visa information om säkerhetskopiering:

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

Återställa från långsiktig lagring av säkerhetskopior använder den [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) cmdlet.

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
> Härifrån kan du kan ansluta till den återställda databasen använder SQL Server Management Studio för att utföra uppgifter som krävs, exempelvis genom att extrahera en bit data från den återställda databasen ska kopieras till den befintliga databasen eller ta bort den befintliga databasen och Byt namn på den återställda databasen till det befintliga databasnamnet. Se [punkt tidpunkt för återställning](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
- Mer information om hur du återställer från säkerhetskopior finns i avsnittet om hur du [återställer från säkerhetskopior](sql-database-recovery-using-backups.md)
