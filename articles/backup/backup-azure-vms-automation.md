---
title: Distribuera och hantera säkerhetskopior för Resource Manager distribuerade virtuella datorer med hjälp av PowerShell | Microsoft Docs
description: Använda PowerShell för att distribuera och hantera säkerhetskopior i Azure för Resource Manager distribuerade virtuella datorer
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk;pullabhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3431db3844ca47ce6c2beafbd894a69f05e0311a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Använda AzureRM.RecoveryServices.Backup-cmdletar för att säkerhetskopiera virtuella datorer

Den här artikeln visar hur du använder Azure PowerShell-cmdlets för att säkerhetskopiera och återställa en Azure virtuell dator (VM) från en Recovery Services-valvet. Används för att skydda data och tillgångar i både Azure Backup och Azure Site Recovery services Recovery Services-valvet eftersom det är en Azure Resource Manager-resurs. Du kan använda en Recovery Services-valvet för att skydda Azure Service Manager-distribuerade virtuella datorer och Azure Resource Manager distribuerade virtuella datorer.

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln ska användas med virtuella datorer som skapats med hjälp av Resource Manager-modellen.
>
>

Den här artikeln vägleder dig genom att använda PowerShell för att skydda en virtuell dator och återställa data från en återställningspunkt.

## <a name="concepts"></a>Begrepp
Om du inte är bekant med Azure Backup-tjänsten för en översikt över tjänsten, kolla [vad är Azure Backup?](backup-introduction-to-azure-backup.md) Innan du börjar se till att du täcka essentials om kraven som krävs för att arbeta med Azure Backup och begränsningar i aktuell lösning för VM-säkerhetskopiering.

Om du vill använda PowerShell effektivt, är det nödvändigt att förstå hierarkin med objekt och från där du vill börja.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

AzureRm.RecoveryServices.Backup PowerShell cmdlet-referens finns i [Azure Backup - Recovery Services cmdletar](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) i Azure-biblioteket.

## <a name="setup-and-registration"></a>Installation och registrering
Börja:

1. [Hämta den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (den lägsta versionen som krävs är: 1.4.0)

2. Hitta av Azure Backup PowerShell-cmdlets som är tillgängliga genom att skriva följande kommando:
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
3. Logga in på ditt Azure-konto med hjälp av **Connect-AzureRmAccount**. Denna cmdlet öppnar en webbsida uppmanar dig att dina autentiseringsuppgifter: 
    - Alternativt kan du inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect-AzureRmAccount** cmdlet, med hjälp av den **-autentiseringsuppgifter** parameter.
    - Om du är CSP-partner som arbetar för en klient måste du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller klienten. Till exempel: **Connect-AzureRmAccount-klient ”fabrikam.com”**
4. Koppla den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången, måste du använda den **[registrera AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** för att registrera providern Azure Recovery-tjänsten med din prenumeration.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ```

6. Du kan kontrollera att de registrerats, med följande kommandon:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ``` 
Kommandots utdata den **RegistrationState** ställas in på **registrerade**. Om inte, bara kör den **[registrera AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet som visas ovan.

Följande aktiviteter kan automatiseras med PowerShell:

* [Skapa ett Recovery Services-valv](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-automation.md#back-up-azure-vms)
* [Utlösa ett säkerhetskopieringsjobb](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Övervaka ett säkerhetskopieringsjobb](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Återställa en virtuell dator i Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Följande steg leder dig genom att skapa ett Recovery Services-valvet. Recovery Services-valvet skiljer sig en Backup-valvet.

1. Recovery Services-valvet är en Resource Manager-resurs, så du måste placera det inom en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Använd den **[ny AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** för att skapa Recovery Services-valvet. Se till att ange samma plats för valvet som användes för resursgruppen.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av lagring redundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [Geo-Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testvault anges till GeoRedundant.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration
Använd **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** att visa listan över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrollera att ett nytt valv skapades eller för att se tillgängliga valv i prenumerationen.

Kör kommandot Get-AzureRmRecoveryServicesVault att visa alla valv i prenumerationen. I följande exempel visas den information som visas för varje valvet.

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
Skydda dina virtuella datorer med hjälp av Recovery Services-valvet. Innan du installerar skyddet ange valvet kontext (vilken typ av data som skyddas i valvet) och kontrollera protection-principen. Protection-principen är schemat när säkerhetskopieringsjobben kör och hur länge varje ögonblicksbild av säkerhetskopian ska sparas.

### <a name="set-vault-context"></a>Ange valvet kontext
Innan du aktiverar skyddet på en virtuell dator använda **[Set AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** ange kontexten valvet. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. I följande exempel anger valvet kontext för valvet, *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Skapa en skyddsprincip för
När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. Du kan använda standardprincipen för att snabbt skydda den virtuella datorn och redigerar principen senare med annan information.

Använd **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** visa protection-principer i valvet. Du kan använda denna cmdlet för att hämta en specifik princip eller visa principerna som associeras med en Arbetsbelastningstyp. I följande exempel hämtar principer för Arbetsbelastningstyp, AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Tidszonen i fältet BackupTime i PowerShell är UTC. När säkerhetskopieringstiden visas i Azure-portalen, justeras tiden till din lokala tidszon.
>
>

En säkerhetskopiering protection-principen är associerad med minst en bevarandeprincip. Bevarandeprincip definierar hur länge en återställningspunkt sparas innan den tas bort. Använd **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** att visa standard bevarandeprincip.  På liknande sätt kan du använda **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** att hämta schema standardprincipen. Den **[ny AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet skapar ett PowerShell-objekt som innehåller information om princip för säkerhetskopiering. Principobjekt schema och förvaring används som indata för den **[ny AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet. I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. I exemplet används dessa variabler för att definiera parametrar när du skapar en skyddsprincip *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Aktivera skydd
När du har definierat säkerhetskopiering protection-principen kan aktivera du fortfarande principen för ett objekt. Använd **[aktivera AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** att aktivera skyddet. Aktiverar skydd kräver två objekt - objektet och principen. När principen har associerats med valvet, utlöses säkerhetskopiering arbetsflödet vid den tidpunkt som definierats i policyschemat.

I följande exempel aktiverar skyddet för objektet, V2VM, med principer, NewPolicy. Att aktivera skydd på icke-krypterade hanteraren för virtuella datorer

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Om du vill aktivera skydd på krypterade virtuella datorer (krypterad med BEK och KEK) som du behöver ge Azure Backup service behörighet att läsa nycklar och hemligheter från nyckelvalvet.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Krypteras för att aktivera skydd på virtuella datorer (krypterade endast med BEK), måste du ge Azure Backup service behörighet att läsa hemligheter från nyckelvalvet.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Om du använder Azure Government-molnet kan sedan använda värdet ff281ffe-705c-4f53-9f37-a40e6f2c68f3 för parametern **- ServicePrincipalName** i [Set AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet.
>
>

För klassiska virtuella datorer

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Ändra en protection-princip
Om du vill ändra protection-principen Använd [Set AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) ändra SchedulePolicy eller RetentionPolicy objekt.

I följande exempel ändras kvarhållningstid för återställningspunkten till 365 dagar.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Utlös en säkerhetskopiering
Du kan använda **[säkerhetskopiering AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** att utlösa ett säkerhetskopieringsjobb. Om det är den första säkerhetskopian är en fullständig säkerhetskopia. Efterföljande säkerhetskopieringar ta en inkrementell kopia. Se till att använda **[Set AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** ange valvet kontexten innan säkerhetskopieringsjobbet. I följande exempel förutsätter valvet kontext angavs.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Tidszonen StartTime och EndTime fält i PowerShell är UTC. När tiden visas i Azure-portalen, justeras tiden till din lokala tidszon.
>
>

## <a name="monitoring-a-backup-job"></a>Övervakning av ett säkerhetskopieringsjobb
Du kan övervaka långvariga åtgärder, till exempel säkerhetskopieringsjobb, utan att använda Azure-portalen. Status för ett pågående jobb får använda den **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet. Denna cmdlet hämtar säkerhetskopieringsjobb för en specifik valvet och att valvet har angetts i kontexten för valvet. I följande exempel hämtar status för ett pågående jobb som en matris och lagrar status i variabeln $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

I stället för avsökning av dessa jobb för slutförande - som är onödiga ytterligare kod – Använd den **[vänta AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet. Denna cmdlet pausar körningen tills jobbet har slutförts eller det angivna tidsgränsvärdet har nåtts.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Återställa en virtuell dator i Azure
Det finns en viktigaste skillnaden mellan att återställa en virtuell dator med hjälp av Azure portal och återställa en virtuell dator med hjälp av PowerShell. Återställningen har slutförts med PowerShell när diskar och konfigurationsinformation från återställningspunkten skapas. Om du vill återställa eller återställa filer från en virtuell dator i Azure-säkerhetskopiering, referera till den [filen återställning](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> Återställningen skapar inte en virtuell dator.
>
>

Om du vill skapa en virtuell dator från disken, finns i avsnittet [skapa den virtuella datorn från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Grundläggande steg för att återställa en Azure VM är:

* Välj den virtuella datorn
* Välj en återställningspunkt
* Återställa diskar
* Skapa den virtuella datorn från lagrade diskar

Följande bild visar objekthierarkin från RecoveryServicesVault nedåt i BackupRecoveryPoint.

![Recovery Services objekthierarkin visar BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Identifiera säkerhetskopierade objekt och den återställningspunkt som innehåller data i tidpunkt om du vill återställa säkerhetskopierade data. Använd den **[återställning AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet för att återställa data från valvet på kundens konto.

### <a name="select-the-vm"></a>Välj den virtuella datorn
Starta från behållare i valvet för att få PowerShell-objektet som identifierar just säkerhetskopiering artikeln och gå nedåt i objekthierarkin. Markera den behållare som representerar den virtuella datorn med hjälp av **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet och skicka som att den **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt
Använd den **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** för att visa en lista med alla återställningspunkter för säkerhetskopiering objektet. Välj återställningspunkt att återställa. Om du inte vet vilken återställningspunkt som ska användas, är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkten i listan.

I följande skript variabeln, **$rp**, är en matris med återställningspunkter för det markerade säkerhetskopiering objektet från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning för den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

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



### <a name="restore-the-disks"></a>Återställa diskar
Använd den **[återställning AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** för att återställa en säkerhetskopiering objektets data och konfigurationsinformation till en återställningspunkt. När du har identifierat en återställningspunkt kan använda det som värdet för den **- RecoveryPoint** parameter. I föregående exempelkod **$rp [0]** var återställningspunkten som ska använda. I följande exempelkod **$rp [0]** är återställningspunkten som ska använda för att återställa disken.

Att återställa diskar och konfigurationsinformation:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Använd den **[vänta AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** för att vänta återställningsjobbet ska slutföras.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

När Återställningsjobbet har slutförts, använda den **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** för att hämta information om återställningen. Egenskapen JobDetails har information som behövs för att återskapa den virtuella datorn.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

När du har återställt diskarna, gå till nästa avsnitt för att skapa den virtuella datorn.

## <a name="create-a-vm-from-restored-disks"></a>Skapa en virtuell dator från återställda diskar
När du har återställt diskarna, Följ dessa steg för att skapa och konfigurera den virtuella datorn från disken.

> [!NOTE]
> Om du vill skapa krypterade virtuella datorer från återställda diskar din Azure roll måste ha behörighet att utföra åtgärden, **Microsoft.KeyVault/vaults/deploy/action**. Om din roll inte har denna behörighet kan du skapa en anpassad roll med den här åtgärden. Mer information finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

1. Fråga om återställda disk egenskaperna för Jobbinformationen.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Konfigurera Azure storage-kontexten och återställa JSON-konfigurationsfil.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Använd JSON-konfigurationsfil för att skapa VM-konfiguration.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Koppla operativsystemdisken och datadiskar. Beroende på konfigurationen av din virtuella dator finns relevant avsnitt för att visa respektive cmdlets:

    #### <a name="non-managed-non-encrypted-vms"></a>Ej hanterade, icke-krypterade virtuella datorer

    Använd följande exempel för icke-hanterad, icke-krypterade virtuella datorer.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Ej hanterade, krypterad virtuella datorer (endast BEK)

    För icke-hanterad och krypterad virtuella datorer (krypterade endast med BEK), måste du återställa hemligheten nyckelvalvet innan du kan koppla diskar. Mer information finns i artikeln [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar Operativsystemet och datadiskarna för krypterade virtuella datorer.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Ej hanterade, krypterad virtuella datorer (BEK och KEK)

    För icke-hanterad och krypterad virtuella datorer (krypterad med BEK och KEK), måste du återställa nyckeln och hemlighet nyckelvalvet innan du kan koppla diskar. Mer information finns i artikeln [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar Operativsystemet och datadiskarna för krypterade virtuella datorer.

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

    #### <a name="managed-non-encrypted-vms"></a>Hanterad, icke-krypterade virtuella datorer

    För hanterade icke-krypterade VMs måste du skapa hanterade diskar från blob storage och Anslut diskarna. Detaljerad information finns i artikeln [ansluta en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). Följande exempelkod visar hur du kopplar datadiskar för hanterade icke-krypterade virtuella datorer.

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

    #### <a name="managed-encrypted-vms-bek-only"></a>Hanteras krypterade virtuella datorer (endast BEK)

    För hanterade krypterade virtuella datorer (krypterade endast med BEK), måste du skapa hanterade diskar från blob storage och Anslut diskarna. Detaljerad information finns i artikeln [ansluta en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). Följande exempelkod visar hur du kopplar datadiskar för hanterade krypterade virtuella datorer.

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

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>Hanteras krypterade virtuella datorer (BEK och KEK)

    För hanterade krypterade virtuella datorer (krypterad med BEK och KEK), måste du skapa hanterade diskar från blob storage och Anslut diskarna. Detaljerad information finns i artikeln [ansluta en datadisk till en virtuell Windows-dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). Följande exempelkod visar hur du kopplar datadiskar för hanterade krypterade virtuella datorer.

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

5. Ange nätverksinställningarna.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Skapa den virtuella datorn.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Återställa filer från en virtuell dator i Azure-säkerhetskopiering

Du kan även återställa enskilda filer från en virtuell dator i Azure-säkerhetskopiering utöver återställning av diskar. Återställ filer funktioner ger tillgång till alla filer i en återställningspunkt och du kan hantera dem via Utforskaren, som du skulle göra med normala filer.

Grundläggande steg för att återställa en fil från Virtuella Azure-säkerhetskopiering är:

* Välj den virtuella datorn
* Välj en återställningspunkt
* Montera diskar återställningspunkt
* Kopiera de nödvändiga filerna
* Demontera disken


### <a name="select-the-vm"></a>Välj den virtuella datorn
Starta från behållare i valvet för att få PowerShell-objektet som identifierar just säkerhetskopiering artikeln och gå nedåt i objekthierarkin. Markera den behållare som representerar den virtuella datorn med hjälp av **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet och skicka som att den **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt
Använd den **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** för att visa en lista med alla återställningspunkter för säkerhetskopiering objektet. Välj återställningspunkt att återställa. Om du inte vet vilken återställningspunkt som ska användas, är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkten i listan.

I följande skript variabeln, **$rp**, är en matris med återställningspunkter för det markerade säkerhetskopiering objektet från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning för den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

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

### <a name="mount-the-disks-of-recovery-point"></a>Montera diskar återställningspunkt

Använd den **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** för att hämta skriptet för att montera alla diskar för återställningspunkten.

> [!NOTE]
> Diskarna monteras som iSCSI-anslutna diskar till den dator där skriptet körs. Därför är nästan omedelbar och till inte en avgift
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Kör skriptet på datorn där du vill återställa filer. Du måste ange lösenordet som visas ovan för att köra skriptet. När diskarna är kopplade använda Utforskaren i Windows och bläddra igenom nya volymer och filer. Mer information finns i den [filen recovery dokumentation](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>Demontera diskar
När de nödvändiga filerna har kopierats demontera diskarna med hjälp av den **[inaktivera AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** cmdlet. Detta rekommenderas eftersom det säkerställer att längre tillgång till filerna för återställningspunkten

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Nästa steg
Om du föredrar att använda PowerShell för att interagera med dina Azure-resurser finns i PowerShell-artikeln [distribuera och hantera säkerhetskopiering för Windows Server](backup-client-automation.md). Om du hanterar DPM-säkerhetskopiering finns i artikeln [distribuera och hantera säkerhetskopiering för DPM](backup-dpm-automation.md). Båda dessa artiklar har en version för klassiska distributioner och Resource Manager distributioner.  
