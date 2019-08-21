---
title: Säkerhetskopiera och återställa Azure Files med Azure Backup och PowerShell
description: Säkerhetskopiera och återställa Azure Files med Azure Backup och PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: dacurwin
ms.reviewer: pullabhk
ms.openlocfilehash: f736d7f1dde8f268033d7c80322b91543672e68f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638532"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Säkerhetskopiera och återställa Azure Files med PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att säkerhetskopiera och återställa en Azure Files fil resurs med hjälp av ett [Azure Backup](backup-overview.md) Recovery Services-valv. 

I den här självstudien beskrivs hur du:

> [!div class="checklist"]
> * Konfigurera PowerShell och registrera Azure Recovery Services-providern.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhets kopiering för en Azure-filresurs.
> * Kör ett säkerhets kopierings jobb.
> * Återställa en säkerhets kopie rad Azure-filresurs eller en enskild fil från en resurs.
> * Övervaka säkerhets kopierings-och återställnings jobb.


## <a name="before-you-start"></a>Innan du börjar

- [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
- Läs om förhands gransknings funktionerna för att [säkerhetskopiera Azure](backup-azure-files.md)-filresurser.
- Granska PowerShell-objektcachen för Recovery Services.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektets hierarki

Objektets hierarki sammanfattas i följande diagram.

![Recovery Services objektets hierarki](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska [referens](/powershell/module/az.recoveryservices) referensen för cmdleten **AZ. RecoveryServices** i Azure-biblioteket.


## <a name="set-up-and-install"></a>Konfigurera och installera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera PowerShell på följande sätt:

1. [Ladda ned den senaste versionen av AZ PowerShell](/powershell/azure/install-az-ps). Den lägsta version som krävs är 1.0.0.

2. Hitta Azure Backup PowerShell-cmdlet: ar med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Granska alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services valvet visas. Här är ett exempel på vad du kan se. Det är inte en fullständig lista över cmdletar.

    ![Lista över Recovery Services-cmdletar](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Logga in på ditt Azure-konto med **Connect-AzAccount**.
4. På webb sidan som visas uppmanas du att ange dina autentiseringsuppgifter för kontot.

    - Alternativt kan du inkludera dina konto uppgifter som en parameter i cmdleten **Connect-AzAccount** med **-Credential**.
    - Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras tenantID eller primära domän namn. Ett exempel är **Connect-AzAccount-Tenant** fabrikam.com.

4. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången ska du använda cmdleten **register-AzResourceProvider** för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Verifiera att providern har registrerats:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. I kommandot utdata kontrollerar du att **RegistrationState** ändras till **registrerad**. Om den inte gör det kör du cmdleten **register-AzResourceProvider** igen.



## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följ dessa steg om du vill skapa ett Recovery Services-valv.

- Recovery Services valvet är en Resource Manager-resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp, eller så kan du skapa en resurs grupp med cmdleten **New-AzResourceGroup** . När du skapar en resurs grupp anger du namn och plats för resurs gruppen. 

1. Ett valv placeras i en resurs grupp. Om du inte har en befintlig resurs grupp skapar du en ny med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). I det här exemplet skapar vi en ny resurs grupp i regionen USA, västra.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Använd cmdleten [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) för att skapa valvet. Ange samma plats för valvet som användes för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av redundans som ska användas för valv lagringen.

   - Du kan använda [Lokalt Redundant lagring](../storage/common/storage-redundancy-lrs.md) eller [Geo-redundant lagring](../storage/common/storage-redundancy-grs.md).
   - I följande exempel anges alternativet **-BackupStorageRedundancy** för[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd för **testvault** som är inställt på interredundant.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata ser ut ungefär så här. Observera att den associerade resurs gruppen och platsen tillhandahålls.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valv kontext

Lagra Valve-objektet i en variabel och ange valv kontexten.

- Många Azure Backup-cmdletar kräver att Recovery Services Vault-objektet är inmatat, så det är praktiskt att lagra valvet i en variabel.
- Valvets sammanhang är typen av data som skyddas i valvet. Ange den med [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). När kontexten har angetts gäller den för alla efterföljande cmdletar.


I följande exempel anges valv kontexten för **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID: t

Vi planerar att föråldra inställningen för valv kontext i enlighet med Azure PowerShell rikt linjer. I stället kan du lagra eller hämta valv-ID: t och skicka det till relevanta kommandon enligt följande:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En säkerhets kopierings princip anger schemat för säkerhets kopior och hur länge återställnings punkter för säkerhets kopiering ska behållas:

- En säkerhets kopierings princip är associerad med minst en bevarande princip. En bevarande princip definierar hur länge en återställnings punkt ska sparas innan den tas bort.
- Visa standard kvarhållning av säkerhets kopierings princip med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Visa standard schema för säkerhets kopiering med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Du använder cmdleten [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) för att skapa en ny säkerhets kopierings princip. Du har angett schema-och bevarande princip objekt.

I följande exempel lagras schema principen och bevarande principen i variabler. Den använder sedan variablerna som parametrar för en ny princip (**NewAFSPolicy**). **NewAFSPolicy** tar en daglig säkerhets kopiering och behåller den i 30 dagar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata ser ut ungefär så här.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Aktivera säkerhets kopiering

När du har definierat säkerhets kopierings principen kan du aktivera skyddet för Azure-filresursen med hjälp av principen.

### <a name="retrieve-a-backup-policy"></a>Hämta en princip för säkerhets kopiering

Du hämtar relevant princip objekt med [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Använd denna cmdlet för att hämta en princip eller för att visa de principer som är kopplade till en arbets belastnings typ.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Hämta en princip för en arbets belastnings typ

I följande exempel hämtas principer för **migreringsåtgärden**för arbets belastnings typen.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Utdata ser ut ungefär så här.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> Tids zonen för fältet **BackupTime** i PowerShell är Universal Coordinated Time (UTC). När tiden för säkerhets kopieringen visas i Azure Portal justeras tiden till din lokala tidszon.

### <a name="retrieve-a-specific-policy"></a>Hämta en speciell princip

Följande princip hämtar säkerhets kopierings principen med namnet **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Aktivera säkerhets kopiering och tillämpa princip

Aktivera skydd med [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). När principen är associerad med valvet utlöses säkerhets kopieringarna enligt princip schemat.

I följande exempel aktive ras skyddet för Azure-filresurs- **testAzureFileShare** i Storage Account **testStorageAcct**, med principen **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Kommandot väntar tills det konfigurera skydds jobbet har avslut ATS och ger liknande utdata som visas.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Utlös en säkerhets kopiering på begäran

Använd [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) för att köra en säkerhets kopiering på begäran för en skyddad Azure-filresurs.

1. Hämta lagrings kontot och fil resursen från behållaren i valvet som innehåller dina säkerhets kopierings data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Om du vill starta ett säkerhets kopierings jobb får du information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Kör en säkerhets kopiering på begäran med[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Kör säkerhets kopiering på begäran på följande sätt:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Ögonblicks bilder av Azure-filresurser används när säkerhets kopieringen görs, så vanligt vis jobbet slutförs när kommandot returnerar utdata.

### <a name="using-on-demand-backups-to-extend-retention"></a>Använda säkerhets kopiering på begäran för att utöka kvarhållning

Säkerhets kopiering på begäran kan användas för att spara dina ögonblicks bilder i 10 år. Scheduler kan användas för att köra PowerShell-skript på begäran med vald kvarhållning och därmed ta ögonblicks bilder med jämna mellanrum varje vecka, månad eller år. När du använder vanliga ögonblicks bilder kan du se [begränsningarna för säkerhets kopiering på begäran](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share-) med Azure Backup.

Om du letar efter exempel skript kan du referera till exempel skriptet på GitHub (https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) med Azure Automation Runbook som gör att du kan schemalägga säkerhets kopieringar regelbundet och behålla dem jämnt upp till 10 år.

### <a name="modify-the-protection-policy"></a>Ändra skydds principen

Om du vill ändra principen som används för att säkerhetskopiera Azure-filresursen använder du [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Ange relevant säkerhets kopierings objekt och den nya säkerhets kopierings principen.

I följande exempel ändras skydds principen för **testAzureFS** från **dailyafs** till **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Återställa Azure-filresurser och filer

Du kan återställa en hel fil resurs eller vissa filer på resursen. Du kan återställa till den ursprungliga platsen eller till en annan plats. 

### <a name="fetch-recovery-points"></a>Hämta återställnings punkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) för att visa en lista över alla återställnings punkter för det säkerhetskopierade objektet.

I följande skript:

- Variabeln **$RP** är en matris av återställnings punkter för det valda säkerhets kopierings objektet från de senaste sju dagarna.
- Matrisen sorteras i omvänd ordning med den senaste återställnings punkten vid index **0**.
- Använd standard PowerShell-matrisering för att välja återställnings punkt.
- I exemplet väljer **$RP [0]** den senaste återställnings punkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Utdata ser ut ungefär så här.

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
När du har valt den relevanta återställnings punkten återställer du fil resursen eller filen till den ursprungliga platsen eller till en annan plats.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Återställa en Azure-filresurs till en annan plats

Använd Restore [-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) för att återställa till den valda återställnings punkten. Ange följande parametrar för att identifiera den alternativa platsen: 

- **TargetStorageAccountName**: Det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
- **TargetFileShareName**: De fil resurser i mål lagrings kontot som det säkerhetskopierade innehållet återställs till.
- **TargetFolder**: Mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp, ger du målmappens värden som en tom sträng.
- **ResolveConflict**: Instruktion om det finns en konflikt med återställda data. Accepterar överskrivning eller **Skip**.

Kör cmdleten med parametrarna på följande sätt:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Återställa en Azure-fil till en annan plats

Använd Restore [-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) för att återställa till den valda återställnings punkten. Ange de här parametrarna för att identifiera den alternativa platsen och för att unikt identifiera den fil som du vill återställa.

* **TargetStorageAccountName**: Det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **TargetFileShareName**: De fil resurser i mål lagrings kontot som det säkerhetskopierade innehållet återställs till.
* **TargetFolder**: Mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp, ger du målmappens värden som en tom sträng.
* **SourceFilePath**: Den absoluta sökvägen till filen som ska återställas inom fil resursen, som en sträng. Den här sökvägen är samma sökväg som används i PowerShell-cmdleten **Get-AzStorageFile** .
* **SourceFileType**: Om en katalog eller en fil har valts. Accepterar **katalog** eller **fil**.
* **ResolveConflict**: Instruktion om det finns en konflikt med återställda data. Accepterar överskrivning eller **Skip**.

De ytterligare parametrarna (SourceFilePath och SourceFileType) är bara relaterade till den enskilda filen som du vill återställa.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Det här kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i föregående avsnitt.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Återställa Azure-filresurser och filer till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange parametrar för mål och mål. Endast **ResolveConflict** måste anges.

#### <a name="overwrite-an-azure-file-share"></a>Skriv över en Azure-filresurs

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Skriv över en Azure-fil

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Spåra säkerhets kopierings-och återställnings jobb

Säkerhets kopierings-och återställnings åtgärder på begäran returnerar ett jobb tillsammans med ett ID, som visas när du [körde en säkerhets kopiering på begäran](#trigger-an-on-demand-backup). Använd cmdleten [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) för att spåra jobb förlopp och information.

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
[Lär dig mer om](backup-azure-files.md) att säkerhetskopiera Azure Files i Azure Portal.
