---
title: Säkerhetskopiera och återställa virtuella Azure-datorer med Azure Backup med PowerShell
description: Beskriver hur du säkerhetskopiera och återställa virtuella Azure-datorer med Azure Backup med PowerShell
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 230c68b0b1de1ef452de51b7b0661a3c3786ea76
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521711"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Säkerhetskopiera och återställa virtuella datorer i Azure med PowerShell

Den här artikeln förklarar hur du säkerhetskopierar och återställer en Azure virtuell dator i en [Azure Backup](backup-overview.md) Recovery Services-valv med PowerShell-cmdletar. 

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv och ange valvets sammanhang.
> * definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlösa en säkerhetskopiering på begäran för de skyddade virtuella datorerna innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra de [krav](backup-azure-arm-vms-prepare.md) att förbereda miljön för att skydda dina virtuella datorer. 




## <a name="before-you-start"></a>Innan du börjar

- [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
- [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitekturen för virtuell Azure-säkerhetskopiering [Lär dig mer om](backup-azure-vms-introduction.md) säkerhetskopieringen och [granska](backup-support-matrix-iaas.md) support, begränsningar och krav.
- Granska PowerShell objekthierarkin för Recovery Services.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services-objekthierarkin

Objekthierarkin summeras i följande diagram.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska den **Az.RecoveryServices** [cmdlet-referens för](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) referens i Azure-biblioteket.



## <a name="set-up-and-register"></a>Konfigurera och registrera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Börja:

1. [Hämta den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Hitta Azure Backup PowerShell-cmdletar tillgängliga genom att skriva följande kommando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```   
 
    Alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services-valv visas. Följande bild är ett exempel på vad som ska visas. Det är inte en fullständig lista över cmdletar.

    ![listan över Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Logga in på ditt Azure-konto med hjälp av **Connect AzAccount**. Denna cmdlet öppnar en webbsida uppmanar dig att autentiseringsuppgifterna för ditt konto:

    * Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect AzAccount** cmdlet, med hjälp av den **-Credential** parametern.
    * Om du är CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Exempel: **Connect-AzAccount -Tenant "fabrikam.com"**

4. Koppla den prenumeration du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången, måste du använda den **[registrera AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Du kan verifiera att Providers, registrerad med följande kommandon:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    I utdata från kommandot den **RegistrationState** bör ändras till **registrerad**. Om inte, kör bara det **[registrera AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet igen.


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Recovery Services-valvet skiljer sig från ett säkerhetskopieringsvalv.

1. Recovery Services-valvet är en Resource Manager-resurs, så du måste placera den i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Använd den [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) cmdlet för att skapa Recovery Services-valvet. Glöm inte att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av lagringsredundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [geografiskt Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testvault anges till GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Du kan visa alla valv i prenumerationen [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
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

Innan du aktiverar skydd på en virtuell dator använder [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) att ställa in valvets sammanhang. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. I följande exempel anger valvkontexten för valvet *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Skapa en skyddsprincip

När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. Du kan använda standardprincipen för att snabbt skydda den virtuella datorn och redigera principen senare med annan information.

Använd **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) att visa vilka skyddsprinciper som är tillgängliga i valvet. Du kan använda denna cmdlet för att hämta en specifik princip eller visa principerna som associeras med en Arbetsbelastningstyp av. I följande exempel hämtar principer för Arbetsbelastningstyp, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
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

En säkerhetskopiering protection-princip är associerad med minst en bevarandeprincip. En bevarandeprincip definierar hur länge en återställningspunkt sparas innan de tas bort.

- Använd [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) att visa standardkvarhållningsprincip.
- På samma sätt du kan använda [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) att hämta schemat standardprincipen.
- Den [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cmdlet skapar ett PowerShell-objekt som innehåller information om principen för säkerhetskopiering.
- Principobjekt schema och kvarhållning används som indata till cmdleten New-AzRecoveryServicesBackupProtectionPolicy.

I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. Exemplet använder dessa variabler för att definiera parametrar när du skapar en skyddsprincip *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata ser ut ungefär så här:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Aktivera skydd

När du har definierat protection-principen kan aktivera du fortfarande principen för ett objekt. Använd [aktivera AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) att aktivera skydd. Aktiverar skydd kräver två objekt - objektet och principen. När principen har associerats med valvet, utlöses arbetsflöde för säkerhetskopiering på den tid som definierats i principschemat.

I följande exempel aktiverar skyddet för objektet, V2VM, använder principen NewPolicy. I exemplen som skiljer sig åt beroende på om den virtuella datorn är krypterad och vad som typ av kryptering.

Att aktivera skydd på **icke-krypterade Resurshanterar-VM**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Om du vill aktivera skydd på krypterade virtuella datorer (krypterad med BEK och KEK), måste du ge Azure Backup-tjänstbehörighet att läsa nycklar och hemligheter från nyckelvalvet.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Att aktivera skydd på **krypterade virtuella datorer (krypterad endast med BEK)**, måste du ge behörighet för Azure Backup-tjänsten ska kunna läsa hemligheter i key Vault.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Om du använder Azure Government-molnet kan sedan använda värdet ff281ffe-705c-4f53-9f37-a40e6f2c68f3 för parametern ServicePrincipalName i [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet.
>


### <a name="modify-a-protection-policy"></a>Ändra en skyddsprincip

Använd för att ändra skyddsprincipen [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) ändra SchedulePolicy eller RetentionPolicy objekt.

I följande exempel ändras återställningspunkten och 365 dagar.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Utlösa en säkerhetskopia

Använd [Backup AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) att utlösa ett säkerhetskopieringsjobb. Om det är den första säkerhetskopieringen är det en fullständig säkerhetskopia. Efterföljande säkerhetskopieringar ta en inkrementell kopia. Se till att använda **[Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)** till anger valvkontexten innan du utlöser säkerhetskopieringsjobbet. I följande exempel förutsätter att valvkontexten har redan angetts.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzRecoveryServicesBackupItem -Item $item
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

Du kan övervaka långvariga åtgärder, till exempel säkerhetskopieringsjobb, utan att använda Azure-portalen. Hämta status för ett pågående jobb med den [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet. Denna cmdlet hämtar säkerhetskopieringsjobb för en specifik valvet och att valvet har angetts i valvets sammanhang. I följande exempel hämtar status för ett pågående jobb som en matris och lagrar status i variabeln $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Utdata ser ut ungefär så här:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

I stället för att avsöka dessa jobb för slutförande, som är onödiga ytterligare kod – Använd den [vänta AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet. Denna cmdlet pausar körningen tills jobbet har slutförts eller det angivna tidsgränsvärdet har nåtts.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Återställa en Azure virtuell dator

Det finns en viktig skillnad mellan återställer en virtuell dator med Azure portal och återställer en virtuell dator med hjälp av PowerShell. Återställningen har slutförts med PowerShell, när diskar och konfigurationsinformation från återställningspunkten har skapats. Återställningen skapa inte den virtuella datorn. Om du vill skapa en virtuell dator från disken, finns i avsnittet [skapa den virtuella datorn från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Om du inte vill återställa en hel virtuell dator men vill återställa eller återställa några filer från en virtuell Azure-säkerhetskopiering, referera till den [filen återställning](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Återställningen skapas inte den virtuella datorn.
>
>

Följande bild visar objekthierarkin från RecoveryServicesVault ned till BackupRecoveryPoint.

![Recovery Services-objekthierarkin som visar BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Om du vill återställa säkerhetskopierade data, identifiera säkerhetskopierade objekt och den återställningspunkt som innehåller point-in-time-data. Använd [återställning AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) att återställa data från valvet till ditt konto.

De grundläggande stegen för att återställa en Azure VM är:

* Välj VM.
* Välj en återställningspunkt.
* Återställa diskarna.
* Skapa den virtuella datorn från lagrade diskar.

### <a name="select-the-vm"></a>Välj den virtuella datorn

Starta från behållaren i valvet för att hämta PowerShell-objektet som identifierar objektet direkt säkerhetskopiering, och gå nedåt i objekthierarkin. För att markera den behållare som representerar den virtuella datorn använder den [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet och skicka vidare som att den [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt

Använd den [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. Välj återställningspunkt att återställa. Om du är osäker på vilken återställningspunkt som du använder är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkt i listan.

I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Utdata ser ut ungefär så här:

```powershell
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

Använd den [återställning AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) cmdlet för att återställa en säkerhetskopieringsobjekt data och konfiguration till en återställningspunkt. När du har identifierat en återställningspunkt kan använda det som värdet för den **- RecoveryPoint** parametern. I exemplet ovan **$rp [0]** var återställningspunkten som du använder. I följande exempelkod **$rp [0]** är återställningspunkten ska använda för att återställa disken.

Att återställa diskar och konfigurationsinformation:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Återställa hanterade diskar

> [!NOTE]
> Om den säkerhetskopierade virtuella datorn har hanterade diskar och du vill återställa dem som hanterade diskar, har vi lagt funktionen från RM för Azure PowerShell-modulen v 6.7.0. och senare
>
>

Ange en extra parameter **TargetResourceGroupName** ange RG som hanterade diskar kommer att återställas. 

> [!NOTE]
> Vi rekommenderar starkt att använda den **TargetResourceGroupName** parametern för att återställa hanterade diskar eftersom det leder till betydande prestandaförbättringar. Dessutom från Az för Azure Powershell-modulen 1.0 och senare, den här parametern är obligatorisk vid en återställning med hanterade diskar
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Den **VMConfig.JSON** filen kommer att återställas till storage-kontot och de hanterade diskarna kommer att återställas till det angivna målet RG.

Utdata ser ut ungefär så här:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Använd den [vänta AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet för att vänta på att återställningsjobbet är klart.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

När Återställningsjobbet har slutförts kan du använda den [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet för att hämta information om återställningen. Egenskapen JobDetails har information som behövs för att återskapa den virtuella datorn.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

När du har återställt diskarna, går du till nästa avsnitt för att skapa den virtuella datorn.

## <a name="create-a-vm-from-restored-disks"></a>Skapa en virtuell dator från återställda diskar

När du har återställt diskarna kan du använda följande steg att skapa och konfigurera den virtuella datorn från disken.

> [!NOTE]
> Om du vill skapa krypterade virtuella datorer från återställda diskar, måste dina Azure-roll har behörighet att utföra åtgärden, **Microsoft.KeyVault/vaults/deploy/action**. Om din roll inte har den här behörigheten kan skapa en anpassad roll med den här åtgärden. Mer information finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Efter återställning av diskar, kan du nu få en Distributionsmall som du kan använda direkt för att skapa en ny virtuell dator. Inga fler olika PS-cmdletar för att skapa hanterade/ohanterade virtuella datorer som är krypterad/okrypterade.

De resulterande jobbinformation ger mall-URI som kan efterfrågas och distribueras.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Distribuera mallen för att skapa en ny virtuell dator som beskrivs bara [här](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

I följande avsnitt visas hur du skapar en virtuell dator med ”VMConfig”-fil.

> [!NOTE]
> Vi rekommenderar starkt att använda distributionsmallen som beskrivs ovan för att skapa en virtuell dator. Det här avsnittet (poäng 1 – 6) upphör att gälla snart.

1. Fråga återställda disken egenskaperna för Jobbinformationen.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Konfigurera Azure storage-kontext och återställa JSON-konfigurationsfil.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Använda JSON-konfigurationsfil för att skapa VM-konfigurationen.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Koppla operativsystemdisken och datadiskar. Det här steget innehåller exempel på olika hanteras och krypterade VM-konfigurationer. Använd exemplet som passar din VM-konfiguration.

   * **Icke-hanterade och icke-krypterade virtuella datorer** -Använd följande exempel för icke-hanterad, icke-krypterade virtuella datorer.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Icke-hanterade och krypterade virtuella datorer med Azure AD (endast BEK)** -för icke-hanterad, krypterade virtuella datorer med Azure AD (krypterad endast med BEK), måste du återställa hemligheten till nyckelvalvet innan du kan lägga till diskar. Mer information finns i den [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar OS och datadiskar för krypterade virtuella datorer. När du anger OS-disken, se till att nämna relevanta OS-typen.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Icke-hanterade och krypterade virtuella datorer med Azure AD (BEK och KEK)** – för icke-hanterad, krypterade virtuella datorer med Azure AD (krypterad med BEK och KEK), återställa nyckel och hemlighet till nyckelvalvet innan du kopplar diskar. Mer information finns i [återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). I följande exempel visas hur du kopplar OS och datadiskar för krypterade virtuella datorer.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Icke-hanterade krypterade virtuella datorer och utan Azure AD (endast BEK)** – för icke-hanterad, krypterade virtuella datorer utan Azure AD (krypterad endast med BEK), om källan **keyVault/hemligheten är inte tillgängliga** återställa hemligheterna till nyckelvalvet med hjälp av proceduren i [återställa en icke-krypterade virtuella datorer från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Kör följande skript för att ställa in Krypteringsinformation på den återställda OS-blob (det här steget inte krävs för datablob). $Dekurl kan hämtas från den återställda keyVault.<br>

   I skriptet nedan måste köras endast när källan keyVault/hemligheten inte är tillgänglig.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Efter den **hemligheter är tillgängliga** och Krypteringsinformation även är inställda på OS-Blob, koppla diskar med hjälp av skriptet som anges nedan.<br>

    Om källan keyVault/hemligheter finns redan, behöver skriptet ovan inte utföras.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Icke-hanterade krypterade virtuella datorer och utan Azure AD (BEK och KEK)** – för icke-hanterad, krypterade virtuella datorer utan Azure AD (krypterad med BEK och KEK), om källan **keyVault/nycklar/hemligheter är inte tillgängliga** återställa nyckeln och nyckeln till nyckelvalvet med hjälp av proceduren i [återställa en icke-krypterade virtuella datorer från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Kör följande skript för att ställa in Krypteringsinformation på den återställda OS-blob (det här steget inte krävs för datablob). $Dekurl och $kekurl kan hämtas från den återställda keyVault.

   I skriptet nedan måste köras endast när källan keyVault/nycklar/hemligheter inte är tillgänglig.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Efter den **nyckel/hemligheter som är tillgängliga** och Krypteringsinformation är inställda på OS-Blob, koppla diskar med hjälp av skriptet som anges nedan.

    Om källan keyVault/nyckel/hemligheter är tillgängliga, behöver skriptet ovan inte utföras.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Hanterade och icke-krypterade virtuella datorer** – för hanterade icke-krypterade virtuella datorer, bifoga de återställda hanterade diskarna. Detaljerad information finns i [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Hanteras och krypterade virtuella datorer med Azure AD (endast BEK)** – för hanterade krypterade virtuella datorer med Azure AD (krypterad endast med BEK), bifoga de återställda hanterade diskarna. Detaljerad information finns i [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Hanteras och krypterade virtuella datorer med Azure AD (BEK och KEK)** – för hanterade krypterade virtuella datorer med Azure AD (krypterad med BEK och KEK), bifoga de återställda hanterade diskarna. Detaljerad information finns i [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Hanteras och krypterade virtuella datorer utan Azure AD (endast BEK)** -för hanterade krypterade virtuella datorer utan Azure AD (krypterad endast med BEK), om källan **keyVault/hemligheten är inte tillgängliga** återställa hemligheterna till nyckelvalv med hjälp av den proceduren i [återställa en icke-krypterade virtuella datorer från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Kör följande skript för att ställa in Krypteringsinformation på den återställda OS-disken (det här steget inte krävs för datadisk). $Dekurl kan hämtas från den återställda keyVault.

     I skriptet nedan måste köras endast när källan keyVault/hemligheten inte är tillgänglig.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     När hemligheterna är tillgängliga och Krypteringsinformation är inställda på OS-disken, om du vill lägga till de återställda hanterade diskarna, se [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Hanteras och krypterade virtuella datorer utan Azure AD (BEK och KEK)** – för hanterade krypterade virtuella datorer utan Azure AD (krypterad med BEK och KEK), om källan **keyVault/nycklar/hemligheter är inte tillgängliga** återställa nyckeln och hemligheter till nyckel valvet med hjälp av proceduren i [återställa en icke-krypterade virtuella datorer från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Kör följande skript för att ställa in Krypteringsinformation på den återställda OS-disken (det här steget inte krävs för datadisk). $Dekurl och $kekurl kan hämtas från den återställda keyVault.

   I skriptet nedan måste köras endast när källan keyVault/nycklar/hemligheter inte är tillgänglig.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    När nyckeln/hemligheter är tillgängliga och Krypteringsinformation är inställda på OS-disken, om du vill lägga till de återställda hanterade diskarna, se [ansluter en datadisk till en Windows-VM med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Ange nätverksinställningarna.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Skapa den virtuella datorn.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Skicka ADE tillägget.

   * **För den virtuella datorn med Azure AD** -Använd följande kommando för att manuellt Aktivera kryptering för datadiskar  

     **BEK endast**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK och KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **För virtuell dator utan Azure AD** -Använd följande kommando för att manuellt Aktivera kryptering för datadiskar.

     Om schemanamn om under Kommandokörning uppmanas du att ange AADClientID, måste du uppdatera din Azure PowerShell.

     **BEK endast**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK och KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
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

Starta från behållaren i valvet för att hämta PowerShell-objektet som identifierar objektet direkt säkerhetskopiering, och gå nedåt i objekthierarkin. För att markera den behållare som representerar den virtuella datorn använder den [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet och skicka vidare som att den [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt

Använd den [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. Välj återställningspunkt att återställa. Om du är osäker på vilken återställningspunkt som du använder är det en bra idé att välja den senaste RecoveryPointType = AppConsistent punkt i listan.

I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

Använd den [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) cmdlet för att hämta skript för att montera alla diskar för återställningspunkten.

> [!NOTE]
> Diskarna monteras som iSCSI-anslutna diskar till den dator där skriptet körs. Montering sker omedelbart och du betala inte något.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Utdata ser ut ungefär så här:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Kör skriptet på datorn där du vill återställa filerna. Du måste ange lösenordet som angavs för att köra skriptet. När diskarna är anslutna kan du använda Windows Utforskaren för att se vilka nya volymer och filer. Mer information finns i artikeln Backup [återställa filer från säkerhetskopiering av Azure virtuella datorer](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Demontera diskarna

När de nödvändiga filerna kopieras Använd [inaktivera AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) att demontera diskarna. Glöm inte att demontera diskarna så att åtkomst till filer för återställningspunkten tas bort.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Nästa steg

Om du vill använda PowerShell för att interagera med dina Azure-resurser finns i PowerShell-artikeln [distribuera och hantera säkerhetskopiering för Windows Server](backup-client-automation.md). Om du hanterar DPM-säkerhetskopior som finns i artikeln [distribuera och hantera säkerhetskopiering för DPM](backup-dpm-automation.md). 
