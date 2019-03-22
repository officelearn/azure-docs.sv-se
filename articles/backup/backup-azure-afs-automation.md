---
title: Säkerhetskopiera och återställa Azure Files med Azure Backup och PowerShell
description: Säkerhetskopiera och återställa Azure Files med Azure Backup och PowerShell.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 83fe8d17699c19d442fd734d71d828eb9fd9d6ed
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258368"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Säkerhetskopiera och återställa Azure Files med PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att säkerhetskopiera och återställa en Azure Files filen resurs med en [Azure Backup](backup-overview.md) Recovery Services-valv. 

I den här självstudien beskrivs hur du:

> [!div class="checklist"]
> * Konfigurera PowerShell och registrera Azure Recovery Services-providern.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhetskopiering för en Azure-filresurs.
> * Köra ett säkerhetskopieringsjobb.
> * Återställa en säkerhetskopierad Azure-filresurs eller en enskild fil från en resurs.
> * Övervaka säkerhetskopierings- och återställningsjobb.


## <a name="before-you-start"></a>Innan du börjar

- [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
- Läs mer om förhandsvisningsfunktionerna för [säkerhetskopierar Azure-filresurser](backup-azure-files.md).
- Granska PowerShell objekthierarkin för Recovery Services.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services-objekthierarkin

Objekthierarkin summeras i följande diagram.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska den **Az.RecoveryServices** [cmdlet-referens för](/powershell/module/az.recoveryservices) referens i Azure-biblioteket.


## <a name="set-up-and-install"></a>Konfigurera och installera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera PowerShell på följande sätt:

1. [Hämta den senaste versionen av Az PowerShell](/powershell/azure/install-az-ps). Den lägsta versionen är 1.0.0.

2. Hitta Azure Backup PowerShell-cmdlets med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Granska alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services-valv visas. Här är ett exempel på vad som kan visas. Det är inte en fullständig lista över cmdlets.

    ![Lista över Recovery Services-cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Logga in på ditt Azure-konto med **Connect AzAccount**.
4. På webbsidan som visas, uppmanas du att ange autentiseringsuppgifterna för ditt konto.

    - Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect AzAccount** cmdlet med **-Credential**.
    - Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Ett exempel är **Connect AzAccount-klient** fabrikam.com.

4. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången kan du använda den **registrera AzResourceProvider** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Kontrollera att de har registrerats:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. I utdata från kommandot kontrollerar du att **RegistrationState** ändras till **registrerad**. Om det inte, kör den **registrera AzResourceProvider** cmdlet igen.



## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följ dessa steg om du vill skapa ett Recovery Services-valv.

- Recovery Services-valvet är en Resource Manager-resurs, så måste du placera det inom en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **New AzResourceGroup** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen. 

1. Ett valv placeras i en resursgrupp. Om du inte har en befintlig resurs, skapa en ny med den [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). I det här exemplet skapar vi en ny resursgrupp i regionen USA, västra.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Använd den [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet för att skapa valvet. Ange samma plats för valvet som har använts för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av redundans för lagring för valvet.

   - Du kan använda [lokalt redundant lagring](../storage/common/storage-redundancy-lrs.md) eller [geo-redundant lagring](../storage/common/storage-redundancy-grs.md).
   - I följande exempel anges den **- BackupStorageRedundancy** för den[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd för **testvault** inställd  **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Du kan visa alla valv i prenumerationen [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande. Observera att den associerade resursgrupp och plats har angetts.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valvets sammanhang

Store vault-objekt i en variabel och ange valvets sammanhang.

- Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata, så det är lämpligt att lagra valvobjekt i en variabel.
- Valvets sammanhang är typen av data som skyddas i valvet. Konfigurera den med [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). När du har angett kontexten gäller det alla efterkommande cmdletar.


I följande exempel anger valvkontexten för **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID

Planerar vi att avveckla valvkontexten inställningen i enlighet med riktlinjerna för Azure PowerShell. I stället kan du lagra eller hämta valv-ID och skicka dem till relevanta kommandon på följande sätt:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En princip för säkerhetskopiering anger schemat för säkerhetskopiering och hur länge säkerhetskopierade återställningspunkter ska finnas:

- En princip för säkerhetskopiering är associerad med minst en bevarandeprincip. En bevarandeprincip definierar hur länge en återställningspunkt sparas innan de tas bort.
- Visa standard säkerhetskopieringsprincip kvarhållning med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Visa standard säkerhetskopieringsprincip schema med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Du använder den [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet för att skapa en ny säkerhetskopieringsprincip. Du kan ange principobjekt schema och kvarhållning.

I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. Därefter använder dessa variabeln som parametrar för en ny princip (**NewAFSPolicy**). **NewAFSPolicy** tar en daglig säkerhetskopiering och bibehålls i 30 dagar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata liknar följande.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Aktivera säkerhetskopiering

När du har definierat principen för säkerhetskopiering kan du aktivera skyddet för den Azure-filresurs som använder principen.

### <a name="retrieve-a-backup-policy"></a>Hämta en princip för säkerhetskopiering

Du hämta det relevanta principobjektet med [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Använd denna cmdlet för att hämta en specifik princip eller visa principerna som associeras med en Arbetsbelastningstyp av.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Hämta en princip för en Arbetsbelastningstyp av

I följande exempel hämtar principer för typen av arbetsbelastningar **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Utdata liknar följande.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> Tidszonen för den **BackupTime** fältet i PowerShell är Universal Coordinated Time (UTC). När säkerhetskopierades visas i Azure-portalen, justeras tiden till din lokala tidszon.

### <a name="retrieve-a-specific-policy"></a>Hämta en specifik princip

Följande princip hämtar principen för säkerhetskopiering med namnet **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Aktivera säkerhetskopiering och tillämpa principen

Aktivera skydd med [aktivera AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). När principen är associerad med valvet, utlöses säkerhetskopior i enlighet med principschemat.

I följande exempel aktiverar skydd för Azure-filresursen **testAzureFileShare** i storage-konto **testStorageAcct**, med principen **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Kommandot väntar tills jobbet Konfigurera skydd har slutförts och ger en liknande utdata som visas.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Utlösa en säkerhetskopiering på begäran

Använd [Backup AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) att köra en säkerhetskopiering på begäran för en skyddad Azure-filresurs.

1. Hämta den storage-konto och filresurs från behållaren i valvet som innehåller dina säkerhetskopierade data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Om du vill starta ett säkerhetskopieringsjobb du få information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Köra en säkerhetskopiering på begäran med[Backup AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Kör säkerhetskopiering på begäran på följande sätt:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som visas i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Azure ögonblicksbilder av filresurser används medan säkerhetskopieringar tas därför vanligtvis jobbet slutförs när kommandot returnerar dessa utdata.

### <a name="modify-the-protection-policy"></a>Ändra protection-principen

Du kan ändra den princip som används för att säkerhetskopiera Azure-filresursen med [aktivera AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Ange de relevanta säkerhetskopieringsobjektet och den nya principen för säkerhetskopiering.

Följande exempel ändringar i **testAzureFS** skyddsprincip från **dailyafs** till **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Återställa Azure-filresurser och filer

Du kan återställa en hel filresurs eller specifika filer på resursen. Du kan återställa till den ursprungliga platsen eller till en annan plats. 

### <a name="fetch-recovery-points"></a>Hämta återställningspunkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) att lista alla återställningspunkter för säkerhetskopierade objekt.

I följande skript:

- Variabeln **$rp** är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna.
- Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid indexet **0**.
- Använd standard PowerShell matris indexering för att välja återställningspunkten.
- I det här exemplet **$rp [0]** väljer den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Utdata liknar följande.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
När den relevanta återställningspunkten är vald återställa du filresursen eller filen till den ursprungliga platsen eller till en annan plats.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Återställa en Azure-filresurs till en alternativ plats

Använd den [återställning AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) att återställa till den valda återställningspunkten. Ange dessa parametrar för att identifiera den alternativa platsen: 

- **TargetStorageAccountName**: Det lagringskonto dit det säkerhetskopierade innehållet har återställts. Mål-lagringskontot måste finnas på samma plats som valvet.
- **TargetFileShareName**: Filresurserna i mållagring kontot till det säkerhetskopierade innehållet har återställts.
- **TargetFolder**: Mappen under filresursen som har återställts. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ge målvärden för mappen som en tom sträng.
- **ResolveConflict**: Anvisningar om det finns en konflikt med dessa data. Accepterar **skriva över** eller **hoppa över**.

Kör cmdleten med parametrar på följande sätt:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som visas i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Återställa en Azure-fil till en alternativ plats

Använd den [återställning AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) att återställa till den valda återställningspunkten. Ange dessa parametrar för att identifiera den alternativa platsen och för att unikt identifiera fil du vill återställa.

* **TargetStorageAccountName**: Det lagringskonto dit det säkerhetskopierade innehållet har återställts. Mål-lagringskontot måste finnas på samma plats som valvet.
* **TargetFileShareName**: Filresurserna i mållagring kontot till det säkerhetskopierade innehållet har återställts.
* **TargetFolder**: Mappen under filresursen som har återställts. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ge målvärden för mappen som en tom sträng.
* **SourceFilePath**: Den absoluta sökvägen till filen som ska återställas i en filresurs som en sträng. Den här sökvägen är samma sökväg används i den **Get-AzStorageFile** PowerShell-cmdlet.
* **SourceFileType**: Om en katalog eller en har fil valts. Accepterar **Directory** eller **filen**.
* **ResolveConflict**: Anvisningar om det finns en konflikt med dessa data. Accepterar **skriva över** eller **hoppa över**.

Ytterligare parametrar (SourceFilePath och SourceFileType) är relaterade bara för den enskilda filen som du vill återställa.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Det här kommandot returnerar ett jobb med ett ID som ska spåras, som visas i föregående avsnitt.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Återställa Azure-filresurser och filer till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange mål - och mål-relaterade parametrar. Endast **ResolveConflict** måste anges.

#### <a name="overwrite-an-azure-file-share"></a>Skriv över en Azure-filresurs

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Skriv över en Azure-fil

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Spåra backup- och återställningsjobb

Åtgärder för säkerhetskopiering och återställning av på begäran returnerar ett jobb tillsammans med ett ID som visas när du [körde en säkerhetskopiering på begäran](#trigger-an-on-demand-backup). Använd den [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet för att spåra jobbförloppet och information.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>Nästa steg
[Lär dig mer om](backup-azure-files.md) säkerhetskopierar Azure Files i Azure-portalen.
