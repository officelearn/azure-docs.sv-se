---
title: Distribuera och hantera säkerhetskopiering för Resource Manager-distribuerade virtuella datorer med hjälp av PowerShell
description: Använd PowerShell för att distribuera och hantera säkerhetskopior i Azure för Resource Manager-distribuerade virtuella datorer
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/06/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d8e2d3e6a303009f5718a86772cdc3db8ed332a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523860"
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Använda AzureRM.RecoveryServices.Backup-cmdletar för att säkerhetskopiera virtuella datorer

Den här artikeln visar hur du använder Azure PowerShell-cmdletar för att säkerhetskopiera och återställa en Azure-dator (VM) från ett Recovery Services-valv. Ett Recovery Services-valv är en Azure Resource Manager-resurs och används för att skydda data och tillgångar i både Azure Backup och Azure Site Recovery-tjänster. Du kan använda Recovery Services-valvet för att skydda Azure Service Manager-distribuerade virtuella datorer och Azure Resource Manager-distribuerade virtuella datorer.

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln handlar om virtuella datorer som skapas med hjälp av Resource Manager-modellen.
>
>

Den här artikeln visar hur du använder PowerShell för att skydda en virtuell dator och återställa data från en återställningspunkt.

## <a name="concepts"></a>Begrepp
Om du inte är bekant med tjänsten Azure Backup, en översikt över tjänsten, kolla [vad är Azure Backup?](backup-introduction-to-azure-backup.md) Innan du börjar bör du se till att du täcker grunderna om kraven som krävs för att arbeta med Azure Backup och begränsningar i aktuell lösning för VM-säkerhetskopiering.

Om du vill använda PowerShell effektivt är det nödvändigt att förstå hierarkin med objekt och var du ska börja.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Om du vill visa AzureRm.RecoveryServices.Backup PowerShell cmdlet-referens, se den [Azure Backup - Recovery Services-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) i Azure-biblioteket.

## <a name="setup-and-registration"></a>Installation och registrering
Börja:

1. [Hämta den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (den lägsta versionen är: 1.4.0)

2. Hitta Azure Backup PowerShell-cmdletar tillgängliga genom att skriva följande kommando:
    ```PS
    PS C:\> Get-Command *azurermrecoveryservices*
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
    Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
    Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
    Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    ```
3. Logga in på ditt Azure-konto med hjälp av **Connect-AzureRmAccount**. Denna cmdlet öppnar en webbsida uppmanar dig att autentiseringsuppgifterna för ditt konto: 
    - Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect-AzureRmAccount** cmdlet, med hjälp av den **-Credential** parametern.
    - Om du är CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Till exempel: **Connect-AzureRmAccount-klient ”fabrikam.com”**
4. Koppla den prenumeration du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången, måste du använda den **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Du kan verifiera att Providers, registrerad med följande kommandon:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
I utdata från kommandot den **RegistrationState** ska inställd på **registrerad**. Om inte, bara kör de **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet som anges ovan.

Följande aktiviteter kan automatiseras med PowerShell:

* [Skapa ett Recovery Services-valv](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-automation.md#back-up-azure-vms)
* [Utlösa ett säkerhetskopieringsjobb](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Övervaka ett säkerhetskopieringsjobb](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Återställa en Azure virtuell dator](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Recovery Services-valvet skiljer sig från ett säkerhetskopieringsvalv.

1. Recovery Services-valvet är en Resource Manager-resurs, så du måste placera den i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Använd den **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** cmdlet för att skapa Recovery Services-valvet. Glöm inte att ange samma plats för valvet som användes för resursgruppen.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av lagringsredundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [geografiskt Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testvault anges till GeoRedundant.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration
Använd **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** att visa en lista över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrollera att ett nytt valv har skapats eller för att se tillgängliga valv i prenumerationen.

Kör kommandot för Get-AzureRmRecoveryServicesVault att visa alla valv i prenumerationen. I följande exempel visar den information som visas för varje valv.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer
Använd Recovery Services-valvet för att skydda dina virtuella datorer. Innan du tillämpar skydd, anger du valvkontexten (typ av data som skyddas i valvet) och kontrollera protection-principen. Protection-principen är schemat som definierar när säkerhetskopieringsjobb ska köras och hur länge varje ögonblicksbild av säkerhetskopian behålls.

### <a name="set-vault-context"></a>Ställ in valvets sammanhang
Innan du aktiverar skydd på en virtuell dator använder **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** att ställa in valvets sammanhang. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. I följande exempel anger valvkontexten för valvet *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Skapa en skyddsprincip
När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. Du kan använda standardprincipen för att snabbt skydda den virtuella datorn och redigera principen senare med annan information.

Använd **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** att visa principer för appskydd i valvet. Du kan använda denna cmdlet för att hämta en specifik princip eller visa principerna som associeras med en Arbetsbelastningstyp av. I följande exempel hämtar principer för Arbetsbelastningstyp, AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Tidszonen för fältet BackupTime i PowerShell är UTC. När säkerhetskopierades visas i Azure-portalen, men justeras tiden till din lokala tidszon.
>
>

En säkerhetskopiering protection-princip är associerad med minst en bevarandeprincip. Bevarandeprincipen anger hur lång tid en återställningspunkt sparas innan de tas bort. Använd **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** att visa standardkvarhållningsprincip.  På samma sätt du kan använda **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** att hämta schemat standardprincipen. Den **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet skapar ett PowerShell-objekt som innehåller information om principen för säkerhetskopiering. Principobjekt schema och kvarhållning används som indata till den **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet. I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. Exemplet använder dessa variabler för att definiera parametrar när du skapar en skyddsprincip *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Aktivera skydd
När du har definierat backup protection-principen kan aktivera du fortfarande principen för ett objekt. Använd **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** att aktivera skydd. Aktiverar skydd kräver två objekt - objektet och principen. När principen har associerats med valvet, utlöses arbetsflöde för säkerhetskopiering på den tid som definierats i principschemat.

I följande exempel aktiverar skydd för objektet som V2VM, som använder principen NewPolicy. Att aktivera skydd på icke-krypterade Resurshanterar-VM

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Om du vill aktivera skydd på krypterade virtuella datorer (krypterad med BEK och KEK) som du behöver ge Azure Backup-tjänstbehörighet att läsa nycklar och hemligheter från nyckelvalvet.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Om du vill aktivera skydd på krypterade virtuella datorer (krypterad endast med BEK) måste du ge behörighet för Azure Backup-tjänsten ska kunna läsa hemligheter från nyckelvalvet.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Om du använder Azure Government-molnet kan sedan använda värdet ff281ffe-705c-4f53-9f37-a40e6f2c68f3 för parametern **- ServicePrincipalName** i [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet.
>
>

För klassiska virtuella datorer

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Ändra en skyddsprincip
Använd för att ändra skyddsprincipen [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) ändra SchedulePolicy eller RetentionPolicy objekt.

I följande exempel ändras återställningspunkten och 365 dagar.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Utlösa en säkerhetskopia
Du kan använda **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** att utlösa ett säkerhetskopieringsjobb. Om det är den första säkerhetskopieringen är det en fullständig säkerhetskopia. Efterföljande säkerhetskopieringar ta en inkrementell kopia. Se till att använda **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** till anger valvkontexten innan du utlöser säkerhetskopieringsjobbet. I följande exempel förutsätter att valvkontexten har angetts.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Tidszonen för StartTime och EndTime fälten i PowerShell är UTC. När tiden visas i Azure-portalen, men justeras tiden till din lokala tidszon.
>
>

## <a name="monitoring-a-backup-job"></a>Övervakning av ett säkerhetskopieringsjobb
Du kan övervaka långvariga åtgärder, till exempel säkerhetskopieringsjobb, utan att använda Azure-portalen. Hämta status för ett pågående jobb med den **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet. Denna cmdlet hämtar säkerhetskopieringsjobb för en specifik valvet och att valvet har angetts i valvets sammanhang. I följande exempel hämtar status för ett pågående jobb som en matris och lagrar status i variabeln $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

I stället för att avsöka dessa jobb för slutförande, som är onödiga ytterligare kod – Använd den **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet. Denna cmdlet pausar körningen tills jobbet har slutförts eller det angivna tidsgränsvärdet har nåtts.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Återställa en Azure virtuell dator
Det finns en viktig skillnad mellan återställer en virtuell dator med Azure portal och återställer en virtuell dator med hjälp av PowerShell. Återställningen har slutförts med PowerShell, när diskar och konfigurationsinformation från återställningspunkten har skapats. Om du vill att återställa filer från en virtuell Azure-säkerhetskopiering, referera till den [filen återställning](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> Återställningsåtgärden skapar inte en virtuell dator.
>
>

Om du vill skapa en virtuell dator från disken, finns i avsnittet [skapa den virtuella datorn från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). De grundläggande stegen för att återställa en Azure VM är:

* Välj den virtuella datorn
* Välj en återställningspunkt
* Återställ diskar
* Skapa den virtuella datorn från lagrade diskar

Följande bild visar objekthierarkin från RecoveryServicesVault ned till BackupRecoveryPoint.

![Recovery Services-objekthierarkin som visar BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Om du vill återställa säkerhetskopierade data, identifiera säkerhetskopierade objekt och den återställningspunkt som innehåller point-in-time-data. Använd den **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet för att återställa data från valvet till kundens konto.

### <a name="select-the-vm"></a>Välj den virtuella datorn
Starta från behållaren i valvet för att hämta PowerShell-objektet som identifierar objektet direkt säkerhetskopiering, och gå nedåt i objekthierarkin. För att markera den behållare som representerar den virtuella datorn använder den **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet och skicka vidare som att den **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt
Använd den **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. Välj återställningspunkt att återställa. Om du är osäker på vilken återställningspunkt som du använder är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkt i listan.

I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Återställ diskar
Använd den **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet för att återställa en säkerhetskopieringsobjekt data och konfiguration till en återställningspunkt. När du har identifierat en återställningspunkt kan använda det som värdet för den **- RecoveryPoint** parametern. I föregående exempelkoden **$rp [0]** var återställningspunkten som du använder. I följande exempelkod **$rp [0]** är återställningspunkten ska använda för att återställa disken.

Att återställa diskar och konfigurationsinformation:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Använd den **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet för att vänta på att återställningsjobbet är klart.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

När Återställningsjobbet har slutförts kan du använda den **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** cmdlet för att hämta information om återställningen. Egenskapen JobDetails har information som behövs för att återskapa den virtuella datorn.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

När du har återställt diskarna, går du till nästa avsnitt för att skapa den virtuella datorn.

## <a name="create-a-vm-from-restored-disks"></a>Skapa en virtuell dator från återställda diskar
När du har återställt diskarna, Följ dessa steg för att skapa och konfigurera den virtuella datorn från disken.

> [!NOTE]
> Om du vill skapa krypterade virtuella datorer från återställda diskar, måste dina Azure-roll har behörighet att utföra åtgärden, **Microsoft.KeyVault/vaults/deploy/action**. Om din roll inte har den här behörigheten kan skapa en anpassad roll med den här åtgärden. Mer information finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

1. Fråga återställda disken egenskaperna för Jobbinformationen.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $configBlobName = $properties["Config Blob Name"]
  ```

2. Konfigurera Azure storage-kontext och återställa JSON-konfigurationsfil.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Använda JSON-konfigurationsfil för att skapa VM-konfigurationen.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Koppla operativsystemdisken och datadiskar. Beroende på hur dina virtuella datorer, se avsnittet relevanta om du vill visa respektive cmdlet: ar:

    #### <a name="non-managed-non-encrypted-vms"></a>Icke-hanterade, icke-krypterade virtuella datorer

    Använd följande exempel för icke-hanterad, icke-krypterade virtuella datorer.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Icke-hanterade, krypterade virtuella datorer (endast BEK)

    För icke-hanterad, krypterade virtuella datorer (krypterad endast med BEK), måste du återställa hemligheten till nyckelvalvet innan du kan lägga till diskar. Mer information finns i artikeln [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar OS och datadiskar för krypterade virtuella datorer.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    ```
    
 När du anger OS-disken, kontrollera att relevanta OS-typen nämns   
    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```
    
Datakryptering för diskar måste vara aktiverat manuellt med följande kommando.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
    ```
    
   #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Icke-hanterade, krypterade virtuella datorer (BEK och KEK)

   För icke-hanterad, krypterade virtuella datorer (krypterad med BEK och KEK) som du behöver återställa nyckel och hemlighet till nyckelvalvet innan du kan lägga till diskar. Mer information finns i artikeln [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar OS och datadiskar för krypterade virtuella datorer.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

Datakryptering för diskar måste vara aktiverat manuellt med följande kommando.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
   #### <a name="managed-non-encrypted-vms"></a>Hanterade, icke-krypterade virtuella datorer

   För hanterade icke-krypterade virtuella datorer måste du skapa hanterade diskar från blob storage och sedan koppla diskar. Detaljerad information finns i artikeln [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). Följande exempelkod visar hur du bifoga datadiskarna för hanterade icke-krypterade virtuella datorer.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

   #### <a name="managed-encrypted-vms-bek-only"></a>Hanterade krypterade virtuella datorer (endast BEK)

   För hanterade krypterade virtuella datorer (krypterad endast med BEK), måste du skapa hanterade diskar från blob storage och sedan koppla diskar. Detaljerad information finns i artikeln [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). Följande exempelkod visar hur du ansluta datadiskar för hanterade krypterade virtuella datorer.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

Datakryptering för diskar måste vara aktiverat manuellt med följande kommando.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
   #### <a name="managed-encrypted-vms-bek-and-kek"></a>Hanterade krypterade virtuella datorer (BEK och KEK)

   För hanterade krypterade virtuella datorer (krypterad med BEK och KEK), måste du skapa hanterade diskar från blob storage och sedan koppla diskar. Detaljerad information finns i artikeln [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). Följande exempelkod visar hur du ansluta datadiskar för hanterade krypterade virtuella datorer.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```
Datakryptering för diskar måste vara aktiverat manuellt med följande kommando.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
5. Ange nätverksinställningarna.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    PS C:\> $virtualNetwork | Set-AzureRmVirtualNetwork
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $subnetindex=0
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Skapa den virtuella datorn.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Återställa filer från en virtuell Azure-säkerhetskopiering

Du kan även återställa enskilda filer från en virtuell Azure-säkerhetskopiering utöver att återställa diskarna. Filer återställningsfunktionen ger åtkomst till alla filer i en återställningspunkt och du kan hantera dem via Utforskaren, som du skulle göra med normala filer.

Grundläggande stegen för att återställa en fil från virtuell Azure-säkerhetskopiering är:

* Välj den virtuella datorn
* Välj en återställningspunkt
* Montera diskarna återställningspunkt
* Kopiera de nödvändiga filerna
* Demontera disken


### <a name="select-the-vm"></a>Välj den virtuella datorn
Starta från behållaren i valvet för att hämta PowerShell-objektet som identifierar objektet direkt säkerhetskopiering, och gå nedåt i objekthierarkin. För att markera den behållare som representerar den virtuella datorn använder den **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet och skicka vidare som att den **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt
Använd den **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. Välj återställningspunkt att återställa. Om du är osäker på vilken återställningspunkt som du använder är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkt i listan.

I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Montera diskarna återställningspunkt

Använd den **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** cmdlet för att hämta skript för att montera alla diskar för återställningspunkten.

> [!NOTE]
> Diskarna monteras som iSCSI-anslutna diskar till den dator där skriptet körs. Därför det är nästan omedelbar och leder inte till någon kostnad
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Kör skriptet på datorn där du vill återställa filerna. Du måste ange lösenordet som visas ovan för att köra skriptet. När diskarna är anslutna kan du använda Windows Utforskaren för att bläddra igenom nya volymerna och filerna. Mer information finns i den [filen dokumentation om recovery](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>Demontera diskarna
När de nödvändiga filerna har kopierats demontera diskarna med hjälp av den **[inaktivera AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** cmdlet. Detta rekommenderas eftersom det säkerställer att åtkomst har tagits bort filerna på återställningspunkten

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Nästa steg
Om du vill använda PowerShell för att interagera med dina Azure-resurser finns i PowerShell-artikeln [distribuera och hantera säkerhetskopiering för Windows Server](backup-client-automation.md). Om du hanterar DPM-säkerhetskopior som finns i artikeln [distribuera och hantera säkerhetskopiering för DPM](backup-dpm-automation.md). Båda de här artiklarna har en version för klassiska distributioner och Resource Manager-distributioner.  
