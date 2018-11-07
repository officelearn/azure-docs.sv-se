---
title: Distribuera och hantera säkerhetskopiering för Resource Manager-distribuerade virtuella datorer med hjälp av PowerShell
description: Använd PowerShell för att distribuera och hantera säkerhetskopior i Azure för Resource Manager-distribuerade virtuella datorer
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 855a046425f051739f61c74b551d4ffea7b9120a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252368"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Använd PowerShell för att säkerhetskopiera och återställa virtuella datorer

Den här artikeln visar hur du använder Azure PowerShell-cmdletar för att säkerhetskopiera och återställa en Azure-dator (VM) från ett Recovery Services-valv. Ett Recovery Services-valv är en Azure Resource Manager-resurs som används för att skydda data och tillgångar i Azure Backup och Azure Site Recovery services. 

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln handlar om virtuella datorer som skapas med hjälp av Resource Manager-modellen.
>
>

Den här artikeln visar hur du använder PowerShell för att skydda en virtuell dator och återställa data från en återställningspunkt.

## <a name="concepts"></a>Begrepp
Om du inte är bekant med tjänsten Azure Backup, en översikt över tjänsten, finns i artikeln [vad är Azure Backup?](backup-introduction-to-azure-backup.md) Innan du börjar bör du se till att du beskriver kraven med Azure Backup och begränsningar i aktuell lösning för VM-säkerhetskopiering.

Om du vill använda PowerShell effektivt är det nödvändigt att förstå hierarkin med objekt och var du ska börja.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Om du vill visa AzureRm.RecoveryServices.Backup PowerShell cmdlet-referens, se den [Azure Backup - Recovery Services-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) i Azure-biblioteket.

## <a name="setup-and-registration"></a>Installation och registrering

Börja:

1. [Hämta den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (den lägsta versionen är: 1.4.0)

2. Hitta Azure Backup PowerShell-cmdletar tillgängliga genom att skriva följande kommando:
   
    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services-valv visas. Följande bild är ett exempel på vad som ska visas. Det är inte en fullständig lista över cmdletar.

    ![listan över Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Logga in på ditt Azure-konto med hjälp av **Connect-AzureRmAccount**. Denna cmdlet öppnar en webbsida uppmanar dig att autentiseringsuppgifterna för ditt konto:

    * Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect-AzureRmAccount** cmdlet, med hjälp av den **-Credential** parametern.
    * Om du är CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Till exempel: **Connect-AzureRmAccount-klient ”fabrikam.com”**

4. Koppla den prenumeration du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången, måste du använda den **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Du kan verifiera att Providers, registrerad med följande kommandon:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
    I utdata från kommandot den **RegistrationState** bör ändras till **registrerad**. Om inte, kör bara det **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet igen.

Följande aktiviteter kan automatiseras med PowerShell:

* [Skapa ett Recovery Services-valv](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-automation.md#back-up-azure-vms)
* [Utlösa ett säkerhetskopieringsjobb](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Övervaka ett säkerhetskopieringsjobb](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Återställa en Azure virtuell dator](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Recovery Services-valvet skiljer sig från ett säkerhetskopieringsvalv.

1. Recovery Services-valvet är en Resource Manager-resurs, så du måste placera den i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Använd den **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** cmdlet för att skapa Recovery Services-valvet. Glöm inte att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av lagringsredundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [geografiskt Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testvault anges till GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Du kan visa alla valv i prenumerationen  **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**:

```powershell
Get-AzureRmRecoveryServicesVault
```

Utdata liknar följande exempel kan du se den associerade ResourceGroupName och plats har angetts.

```
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

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Skapa en skyddsprincip

När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. Du kan använda standardprincipen för att snabbt skydda den virtuella datorn och redigera principen senare med annan information.

Använd **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** att visa vilka skyddsprinciper som är tillgängliga i valvet. Du kan använda denna cmdlet för att hämta en specifik princip eller visa principerna som associeras med en Arbetsbelastningstyp av. I följande exempel hämtar principer för Arbetsbelastningstyp, AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Utdata ser ut ungefär så här:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Tidszonen för fältet BackupTime i PowerShell är UTC. När säkerhetskopierades visas i Azure-portalen, men justeras tiden till din lokala tidszon.
>
>

En säkerhetskopiering protection-princip är associerad med minst en bevarandeprincip. Bevarandeprincipen anger hur lång tid en återställningspunkt sparas innan de tas bort. Använd **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** att visa standardkvarhållningsprincip.  På samma sätt du kan använda **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** att hämta schemat standardprincipen. Den **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet skapar ett PowerShell-objekt som innehåller information om principen för säkerhetskopiering. Principobjekt schema och kvarhållning används som indata till den **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet. I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. Exemplet använder dessa variabler för att definiera parametrar när du skapar en skyddsprincip *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata ser ut ungefär så här:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Aktivera skydd

När du har definierat protection-principen kan aktivera du fortfarande principen för ett objekt. Använd **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** att aktivera skydd. Aktiverar skydd kräver två objekt - objektet och principen. När principen har associerats med valvet, utlöses arbetsflöde för säkerhetskopiering på den tid som definierats i principschemat.

I följande exempel aktiverar skyddet för objektet, V2VM, använder principen NewPolicy. I exemplen som skiljer sig åt beroende på om den virtuella datorn är krypterad och vad som typ av kryptering. 

Att aktivera skydd på **icke-krypterade Resurshanterar-VM**:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Att aktivera skydd på **krypterade virtuella datorer (krypterad med BEK och KEK)**, måste du ge Azure Backup-tjänstbehörighet att läsa nycklar och hemligheter från nyckelvalvet.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Att aktivera skydd på **krypterade virtuella datorer (krypterad endast med BEK)**, måste du ge behörighet för Azure Backup-tjänsten ska kunna läsa hemligheter i key Vault.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Om du använder Azure Government-molnet kan sedan använda värdet ff281ffe-705c-4f53-9f37-a40e6f2c68f3 för parametern **- ServicePrincipalName** i [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet.
>
>

För att aktivera skydd på en klassisk virtuell dator:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Ändra en skyddsprincip

Använd för att ändra skyddsprincipen [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) ändra SchedulePolicy eller RetentionPolicy objekt.

I följande exempel ändras återställningspunkten och 365 dagar.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Utlösa en säkerhetskopia

Använd **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** att utlösa ett säkerhetskopieringsjobb. Om det är den första säkerhetskopieringen är det en fullständig säkerhetskopia. Efterföljande säkerhetskopieringar ta en inkrementell kopia. Se till att använda **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** till anger valvkontexten innan du utlöser säkerhetskopieringsjobbet. I följande exempel förutsätter att valvkontexten har redan angetts.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Utdata ser ut ungefär så här:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Tidszonen för StartTime och EndTime fälten i PowerShell är UTC. När tiden visas i Azure-portalen, men justeras tiden till din lokala tidszon.
>
>

## <a name="monitoring-a-backup-job"></a>Övervakning av ett säkerhetskopieringsjobb

Du kan övervaka långvariga åtgärder, till exempel säkerhetskopieringsjobb, utan att använda Azure-portalen. Hämta status för ett pågående jobb med den **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet. Denna cmdlet hämtar säkerhetskopieringsjobb för en specifik valvet och att valvet har angetts i valvets sammanhang. I följande exempel hämtar status för ett pågående jobb som en matris och lagrar status i variabeln $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Utdata ser ut ungefär så här:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

I stället för att avsöka dessa jobb för slutförande, som är onödiga ytterligare kod – Använd den **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet. Denna cmdlet pausar körningen tills jobbet har slutförts eller det angivna tidsgränsvärdet har nåtts.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Återställa en Azure virtuell dator

Det finns en viktig skillnad mellan återställer en virtuell dator med Azure portal och återställer en virtuell dator med hjälp av PowerShell. Återställningen har slutförts med PowerShell, när diskar och konfigurationsinformation från återställningspunkten har skapats. Återställningen skapa inte den virtuella datorn. Om du vill skapa en virtuell dator från disken, finns i avsnittet [skapa den virtuella datorn från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Om du inte vill återställa en hel virtuell dator men vill återställa eller återställa några filer från en virtuell Azure-säkerhetskopiering, referera till den [filen återställning](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Återställningen skapas inte den virtuella datorn.
>
>

Följande bild visar objekthierarkin från RecoveryServicesVault ned till BackupRecoveryPoint.

![Recovery Services-objekthierarkin som visar BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Om du vill återställa säkerhetskopierade data, identifiera säkerhetskopierade objekt och den återställningspunkt som innehåller point-in-time-data. Använd **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** att återställa data från valvet till ditt konto.

De grundläggande stegen för att återställa en Azure VM är:

* Välj VM.
* Välj en återställningspunkt.
* Återställa diskarna.
* Skapa den virtuella datorn från lagrade diskar.

### <a name="select-the-vm"></a>Välj den virtuella datorn

Starta från behållaren i valvet för att hämta PowerShell-objektet som identifierar objektet direkt säkerhetskopiering, och gå nedåt i objekthierarkin. För att markera den behållare som representerar den virtuella datorn använder den **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet och skicka vidare som att den **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt

Använd den **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. Välj återställningspunkt att återställa. Om du är osäker på vilken återställningspunkt som du använder är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkt i listan.

I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Utdata ser ut ungefär så här:

```
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

Använd den **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet för att återställa en säkerhetskopieringsobjekt data och konfiguration till en återställningspunkt. När du har identifierat en återställningspunkt kan använda det som värdet för den **- RecoveryPoint** parametern. I exemplet ovan **$rp [0]** var återställningspunkten som du använder. I följande exempelkod **$rp [0]** är återställningspunkten ska använda för att återställa disken.

Att återställa diskar och konfigurationsinformation:

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```
#### <a name="restore-managed-disks"></a>Återställa hanterade diskar

> [!NOTE]
> Om den säkerhetskopierade virtuella datorn har hanterade diskar och du vill återställa dem som hanterade diskar, har vi lagt funktionen från Azure Powershell v 6.7.0. och senare
>
>

Ange en extra parameter **TargetResourceGroupName** ange RG som hanterade diskar kommer att återställas.


```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Den **VMConfig.JSON** filen kommer att återställas till storage-kontot och de hanterade diskarna kommer att återställas till det angivna målet RG.


Utdata ser ut ungefär så här:
```
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Använd den **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet för att vänta på att återställningsjobbet är klart.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

När Återställningsjobbet har slutförts kan du använda den **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** cmdlet för att hämta information om återställningen. Egenskapen JobDetails har information som behövs för att återskapa den virtuella datorn.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

När du har återställt diskarna, går du till nästa avsnitt för att skapa den virtuella datorn.

## <a name="create-a-vm-from-restored-disks"></a>Skapa en virtuell dator från återställda diskar

När du har återställt diskarna kan du använda följande steg att skapa och konfigurera den virtuella datorn från disken.

> [!NOTE]
> Om du vill skapa krypterade virtuella datorer från återställda diskar, måste dina Azure-roll har behörighet att utföra åtgärden, **Microsoft.KeyVault/vaults/deploy/action**. Om din roll inte har den här behörigheten kan skapa en anpassad roll med den här åtgärden. Mer information finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

1. Fråga återställda disken egenskaperna för Jobbinformationen.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Konfigurera Azure storage-kontext och återställa JSON-konfigurationsfil.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Använda JSON-konfigurationsfil för att skapa VM-konfigurationen.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Koppla operativsystemdisken och datadiskar. Det här steget innehåller exempel på olika hanteras och krypterade VM-konfigurationer. Använd exemplet som passar din VM-konfiguration.

   * **Icke-hanterade och icke-krypterade virtuella datorer** -Använd följande exempel för icke-hanterad, icke-krypterade virtuella datorer.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Icke-hanterade och krypterade virtuella datorer (endast BEK)** -för icke-hanterad, krypterade virtuella datorer (krypterad endast med BEK), måste du återställa hemligheten till nyckelvalvet innan du kan lägga till diskar. Mer information finns i artikeln [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar OS och datadiskar för krypterade virtuella datorer. När du anger OS-disken, se till att nämna relevanta OS-typen.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```
      Använd följande kommando för att manuellt Aktivera kryptering för datadiskar.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
      ```

   * **Icke-hanterade krypterade virtuella datorer och (BEK och KEK)** – för icke-hanterad, krypterade virtuella datorer (krypterad med BEK och KEK), återställa nyckel och hemlighet till nyckelvalvet innan du kopplar diskar. Mer information finns i artikeln [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar OS och datadiskar för krypterade virtuella datorer.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
      ```

      Använd följande kommando för att manuellt Aktivera kryptering för datadiskar.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Hanterade och icke-krypterade virtuella datorer** – för hanterade icke-krypterade virtuella datorer, bifoga de återställda hanterade diskarna. Detaljerad information finns i artikeln [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Hanteras och krypterade virtuella datorer (endast BEK)** – för hanterade krypterade virtuella datorer (krypterad endast med BEK), bifoga de återställda hanterade diskarna. Detaljerad information finns i artikeln [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).
   
      Använd följande kommando för att manuellt Aktivera kryptering för datadiskar.

       ```powershell
       Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
       ```

   * **Hanteras och krypterade virtuella datorer (BEK och KEK)** – för hanterade krypterade virtuella datorer (krypterad med BEK och KEK), bifoga de återställda hanterade diskarna. Detaljerad information finns i artikeln [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md). 

      Använd följande kommando för att manuellt Aktivera kryptering för datadiskar.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

5. Ange nätverksinställningarna.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Skapa den virtuella datorn.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Återställa filer från en virtuell Azure-säkerhetskopiering

Du kan även återställa enskilda filer från en virtuell Azure-säkerhetskopiering utöver att återställa diskarna. Filer återställningsfunktionen ger åtkomst till alla filer i en återställningspunkt. Hantera filer via Utforskaren precis som för normala filer.

Grundläggande stegen för att återställa en fil från en virtuell Azure-säkerhetskopiering är:

* Välj den virtuella datorn
* Välj en återställningspunkt
* Montera diskarna återställningspunkt
* Kopiera de nödvändiga filerna
* Demontera disken


### <a name="select-the-vm"></a>Välj den virtuella datorn

Starta från behållaren i valvet för att hämta PowerShell-objektet som identifierar objektet direkt säkerhetskopiering, och gå nedåt i objekthierarkin. För att markera den behållare som representerar den virtuella datorn använder den **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet och skicka vidare som att den **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt

Använd den **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. Välj återställningspunkt att återställa. Om du är osäker på vilken återställningspunkt som du använder är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkt i listan.

I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Utdata ser ut ungefär så här:

```
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
> Diskarna monteras som iSCSI-anslutna diskar till den dator där skriptet körs. Montering sker omedelbart och du betala inte något.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Utdata ser ut ungefär så här:

```
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Kör skriptet på datorn där du vill återställa filerna. Du måste ange lösenordet som angavs för att köra skriptet. När diskarna är anslutna kan du använda Windows Utforskaren för att se vilka nya volymer och filer. Mer information finns i artikeln Backup [återställa filer från säkerhetskopiering av Azure virtuella datorer](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Demontera diskarna

När de nödvändiga filerna kopieras Använd **[inaktivera AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** att demontera diskarna. Glöm inte att demontera diskarna så att åtkomst till filer för återställningspunkten tas bort.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Nästa steg

Om du föredrar att använda PowerShell för att engagera wi th din Azure-resurser finns i PowerShell-artikeln [distribuera och hantera säkerhetskopiering för Windows Server](backup-client-automation.md). Om du hanterar DPM-säkerhetskopior som finns i artikeln [distribuera och hantera säkerhetskopiering för DPM](backup-dpm-automation.md). Båda de här artiklarna har en version för klassiska distributioner och Resource Manager-distributioner.  
