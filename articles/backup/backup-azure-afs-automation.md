---
title: Distribuera och hantera säkerhetskopior av Azure-filresurser med hjälp av PowerShell
description: Använd PowerShell för att distribuera och hantera säkerhetskopior i Azure för Azure-filresurser
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell; Säkerhetskopiering av Azure files; Azure filer återställa;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 30fc36f29a7602e2bc3f192b445474bfc50e9434
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632643"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Använd PowerShell för att säkerhetskopiera och återställa Azure-filresurser

Den här artikeln visar hur du använder Azure PowerShell-cmdletar för att säkerhetskopiera och återställa en Azure-filresurs från Recovery Services-valvet. Ett Recovery Services-valv är en Azure Resource Manager-resurs som används för att skydda data och tillgångar i Azure Backup och Azure Site Recovery services.

## <a name="concepts"></a>Begrepp

Om du inte är bekant med tjänsten Azure Backup, en översikt över tjänsten, finns i artikeln [vad är Azure Backup?](backup-introduction-to-azure-backup.md). Innan du börjar, kontrollera att du anteckna förhandsvisningsfunktionerna att säkerhetskopiera Azure-filresurser dokumenterade [här](backup-azure-files.md).

Om du vill använda PowerShell effektivt är det nödvändigt att förstå hierarkin med objekt och var du ska börja.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Om du vill visa AzureRm.RecoveryServices.Backup PowerShell cmdlet-referens, se den [Azure Backup - Recovery Services-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) i Azure-biblioteket.

## <a name="setup-and-registration"></a>Installation och registrering

> [!NOTE]
> Förhandspriserna [här](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), stöd för nya funktioner i AzureRM-modulen ends i november 2018. Därför ger stöd för säkerhetskopiering av Azure-filresurser med den nya ”Az” PS-modulen nu i GA.

Börja:

1. [Hämta den senaste versionen av PowerShell ”Az”](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (den lägsta versionen är: 1.0.0)

2. Hitta Azure Backup PowerShell-cmdletar tillgängliga genom att skriva följande kommando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services-valv visas. Följande bild är ett exempel på vad som ska visas. Det är inte en fullständig lista över cmdletar.

    ![listan över Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Logga in på ditt Azure-konto med hjälp av **Connect AzAccount**. Denna cmdlet öppnar en webbsida uppmanar dig att autentiseringsuppgifterna för ditt konto:

    * Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect AzAccount** cmdlet, med hjälp av den **-Credential** parametern.
    * Om du är CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Exempel: **Ansluta AzAccount-klient ”fabrikam.com”**

4. Koppla den prenumeration du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången, måste du använda den **registrera AzResourceProvider** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Du kan verifiera att Providers, registrerad med följande kommandon:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    I utdata från kommandot den **RegistrationState** bör ändras till **registrerad**. Om inte, kör bara det **registrera AzResourceProvider** cmdlet igen.

Följande aktiviteter kan automatiseras med PowerShell:

* skapar ett Recovery Services-valv
* Konfigurera säkerhetskopiering för Azure-filresurser
* Utlösa ett säkerhetskopieringsjobb
* Övervaka ett säkerhetskopieringsjobb
* Återställa en Azure-filresurs
* Återställa en enskild Azure-fil från en Azure-filresurs

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv.

1. Recovery Services-valvet är en Resource Manager-resurs, så du måste placera den i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **New AzResourceGroup** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Använd den **New AzRecoveryServicesVault** cmdlet för att skapa Recovery Services-valvet. Glöm inte att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Ange vilken typ av lagringsredundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [geografiskt Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testvault anges till GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Du kan visa alla valv i prenumerationen **Get-AzRecoveryServicesVault**:

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande exempel kan du se den associerade ResourceGroupName och plats har angetts.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata.

Använd **Set-AzRecoveryServicesVaultContext** att ställa in valvets sammanhang. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. I följande exempel anger valvkontexten för valvet *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Vi planerar att inaktualisera inställningen för valvkontexten enligt riktlinjerna för Azure PowerShell. Vi rekommenderar i stället användare att skicka valv-ID som anges nedan

Du kan också du kan store/hämta ID för det valv som du vill utföra en åtgärd med PowerShell och skicka dem till relevanta kommandot.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Konfigurera säkerhetskopiering för en Azure-filresurs

### <a name="create-protection-policy"></a>Skapa skyddsprincip

En säkerhetskopiering protection-princip är associerad med minst en bevarandeprincip. Bevarandeprincipen anger hur lång tid en återställningspunkt sparas innan de tas bort. Använd **Get-AzRecoveryServicesBackupRetentionPolicyObject** att visa standardkvarhållningsprincip.  På samma sätt du kan använda **Get-AzRecoveryServicesBackupSchedulePolicyObject** att hämta schemat standardprincipen. Den **New AzRecoveryServicesBackupProtectionPolicy** cmdlet skapar ett PowerShell-objekt som innehåller information om principen för säkerhetskopiering. Principobjekt schema och kvarhållning används som indata till den **New AzRecoveryServicesBackupProtectionPolicy** cmdlet. I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. Exemplet använder dessa variabler för att definiera parametrar när du skapar en skyddsprincip *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata ser ut ungefär så här:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

NewAFSPolicy tar en daglig säkerhetskopiering och bibehålls i 30 dagar.

### <a name="enable-protection"></a>Aktivera skydd

När du har definierat protection-principen, kan du aktivera skydd för Azure-filresursen med den här principen.

Först hämta principobjektet relevant med de **Get-AzRecoveryServicesBackupProtectionPolicy** cmdlet. Du kan använda denna cmdlet för att hämta en specifik princip eller visa principerna som associeras med en Arbetsbelastningstyp av.

I följande exempel hämtar principer för Arbetsbelastningstyp, AzureFiles.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Utdata ser ut ungefär så här:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Tidszonen för fältet BackupTime i PowerShell är UTC. När säkerhetskopierades visas i Azure-portalen, men justeras tiden till din lokala tidszon.
>
>

Följande princip hämtar principen för säkerhetskopiering med samma namn som ”dailyafs”

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Använd **aktivera AzRecoveryServicesBackupProtection** aktivera skyddet för objektet med den angivna principen. När principen har associerats med valvet, utlöses arbetsflöde för säkerhetskopiering på den tid som definierats i principschemat.

I följande exempel aktiverar skydd för Azure-filresursen ”testAzureFileShare”, under den storage-konto ”testStorageAcct”, med principen dailyafs.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Kommandot väntar tills jobbet Konfigurera skydd har slutförts och ger liknande utdata som visas nedan.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Utlösa en säkerhetskopiering på begäran

Använd **Backup AzRecoveryServicesBackupItem** att utlösa ett säkerhetskopieringsjobb för en skyddad Azure-filresurs. Hämta storage-konto och filresursen i den med hjälp av följande kommandon och utlösa en säkerhetskopiering på begäran.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Kommandot returnerar ett jobb som ska spåras med ett ID som i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Vi använder sig av Azure File-ögonblicksbilder vid säkerhetskopiering och kan därför jobbet slutförs vanligtvis när kommandot returnerar dessa utdata

### <a name="modify-protection-policy"></a>Ändra skyddsprincip

Om du vill ändra principen med vilket Azure-filresursen är skyddad kan du använda den **aktivera AzRecoveryServicesBackupProtection** med relevanta säkerhetskopieringsobjekt och den nya skyddsprincipen för.

I följande exempel ändras skyddsprincip för ”testAzureFS” från ”dailyafs” till ”monthlyafs”

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Återställa Azure file-resurs/er / Azure filer

Du kan återställa en hel filresurs till dess ursprungliga eller en annan plats. På samma sätt kan enskilda filer från filresursen återställas samt.

### <a name="fetching-recovery-points"></a>Hämta återställningspunkter

Använd den **Get-AzRecoveryServicesBackupRecoveryPoint** cmdlet för att lista alla återställningspunkter för säkerhetskopieringsobjektet. I följande skript, variabeln, **$rp**, är en matris med återställningspunkter för den valda säkerhetskopieringsobjekt från de senaste sju dagarna. Matrisen är sorterad i omvänd ordning tid med den senaste återställningspunkten vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. I det här exemplet väljer $rp [0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Utdata ser ut ungefär så här:

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

När den relevanta återställningspunkten är vald fortsätta med att återställa filresursen/filen till en alternativ/ursprungliga plats som beskrivs nedan.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Återställ Azure-filresurser till en alternativ plats

#### <a name="restoring-an-azure-file-share"></a>Återställa en Azure-filresurs

Identifiera den alternativa platsen genom att ange följande information:

* ***TargetStorageAccountName***: Storage-konto dit det säkerhetskopierade innehållet har återställts. Mål-lagringskontot ska vara på samma plats som valvet.
* ***TargetFileShareName***: Filresurserna i mållagringskontot dit det säkerhetskopierade innehållet har återställts
* ***TargetFolder***: Mappen under filresursen som har återställts. Om det säkerhetskopierade innehållet ska återställas till rotmappen, ge målvärden för mappen som tom sträng
* ***ResolveConflict***: Anvisningar om en konflikt uppstår med dessa data. Accepterar ”skriva” eller ”hoppa över”

Ange dessa parametrar för att kommandot restore för att återställa en säkerhetskopia filresurs till en annan plats.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

Kommandot returnerar ett jobb med ett ID som ska spåras enligt följande exempel.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Återställa en Azure-fil

I om du vill återställa en enskild fil i stället för en hel filresurs bör den enskilda filen identifieras unikt genom att ange följande parametrar.

* ***TargetStorageAccountName***: Storage-konto dit det säkerhetskopierade innehållet har återställts. Mål-lagringskontot ska vara på samma plats som valvet.
* ***TargetFileShareName***: Filresurserna i mållagringskontot dit det säkerhetskopierade innehållet har återställts
* ***TargetFolder***: Mappen under filresursen som har återställts. Om det säkerhetskopierade innehållet ska återställas till rotmappen, ge målvärden för mappen som tom sträng
* ***SourceFilePath***: Den absoluta sökvägen till filen som ska återställas i en filresurs som en sträng. Det här är samma sökväg används i ```Get-AzStorageFile``` PS-cmdlet.
* ***SourceFileType***: Om en katalog eller en har fil valts. Accepterar ”Directory” eller ”fil”
* ***ResolveConflict***: Anvisningar om en konflikt uppstår med dessa data. Accepterar ”skriva” eller ”hoppa över”

Som du kan se relaterad ytterligare parametrar bara till den fil som ska återställas.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Detta kommer också att returnera ett jobb som ska spåras med ID enligt ovan.

### <a name="restore-azure-file-shares-to-original-location"></a>Återställ Azure-filresurser till den ursprungliga platsen

Vid den ursprungliga platsen återställning relaterade alla målet parametrar inte behöver anges. Endast ```ResolveConflict``` måste anges

#### <a name="overwrite-an-azure-file-share"></a>Skriv över en Azure-filresurs

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Skriv över en Azure-fil

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Spåra backup- och återställningsjobb

Åtgärder för säkerhetskopiering och återställning av på begäran returnerar ett jobb tillsammans med ett ID enligt [ovan](#trigger-an-on-demand-backup). Använd den ```Get-AzRecoveryServicesBackupJobDetails``` för att spåra förloppet för jobbet och hämta mer information.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
