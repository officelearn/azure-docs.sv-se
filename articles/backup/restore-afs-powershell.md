---
title: Återställa Azure-filer med PowerShell
description: I den här artikeln kan du läsa om hur du återställer Azure-filer med hjälp av Azure Backup-tjänsten och PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086821"
---
# <a name="restore-azure-files-with-powershell"></a>Återställa Azure-filer med PowerShell

I den här artikeln beskrivs hur du återställer en hel filresurs, eller specifika filer, från en återställningspunkt som skapats av [Azure Backup-tjänsten](backup-overview.md) med Azure Powershell.

Du kan återställa en hel filresurs eller specifika filer på resursen. Du kan återställa till den ursprungliga platsen eller till en annan plats.

> [!WARNING]
> Kontrollera att PS-versionen uppgraderas till den lägsta versionen för Az.RecoveryServices 2.6.0 för AFS-säkerhetskopior. Mer information finns [i avsnittet](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) om kravet på den här ändringen.

## <a name="fetch-recovery-points"></a>Hämta återställningspunkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) om du vill visa alla återställningspunkter för säkerhetskopierade objekt.

I följande skript:

* Variabeln **$rp** är en matris med återställningspunkter för det valda säkerhetskopieringsobjektet från de senaste sju dagarna.
* Matrisen sorteras i omvänd tidsordning med den senaste återställningspunkten vid index **0**.
* Använd standardindexering av PowerShell-matris för att välja återställningspunkten.
* I exemplet väljer **$rp[0]** den senaste återställningspunkten.

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

När den relevanta återställningspunkten har valts återställer du filresursen eller filen till den ursprungliga platsen eller till en annan plats.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Återställa en Azure-filresurs till en annan plats

Använd [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) för att återställa till den valda återställningspunkten. Ange dessa parametrar för att identifiera den alternativa platsen:

* **TargetStorageAccountName**: Lagringskontot som säkerhetskopierade innehållet återställs till. Mållagringskontot måste vara på samma plats som valvet.
* **TargetFileShareName**: Filen delar inom mållagringskontot som säkerhetskopieringsinnehållet återställs till.
* **TargetFolder**: Mappen under filresursen som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du värdena för målmappen som en tom sträng.
* **ResolveConflict**: Instruktion om det finns en konflikt med återställda data. Accepterar **Överskrivning** eller **Hoppa över**.

Kör cmdleten med parametrarna enligt följande:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Kommandot returnerar ett jobb med ett ID som ska spåras, vilket visas i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Återställa en Azure-fil till en annan plats

Använd [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) för att återställa till den valda återställningspunkten. Ange dessa parametrar för att identifiera den alternativa platsen och för att unikt identifiera filen som du vill återställa.

* **TargetStorageAccountName**: Lagringskontot som säkerhetskopierade innehållet återställs till. Mållagringskontot måste vara på samma plats som valvet.
* **TargetFileShareName**: Filen delar inom mållagringskontot som säkerhetskopieringsinnehållet återställs till.
* **TargetFolder**: Mappen under filresursen som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du värdena för målmappen som en tom sträng.
* **SourceFilePath**: Den absoluta sökvägen till filen, som ska återställas i filresursen, som en sträng. Den här sökvägen är samma sökväg som används i **Get-AzStorageFile** PowerShell-cmdleten.
* **SourceFileType**: Om en katalog eller en fil är markerad. Accepterar **katalog** eller **fil**.
* **ResolveConflict**: Instruktion om det finns en konflikt med återställda data. Accepterar **Överskrivning** eller **Hoppa över**.

De ytterligare parametrarna (SourceFilePath och SourceFileType) är endast relaterade till den enskilda fil som du vill återställa.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Det här kommandot returnerar ett jobb med ett ID som ska spåras, vilket visas i föregående avsnitt.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Återställa Azure-filresurser och filer till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange mål- och målrelaterade parametrar. Endast **ResolveConflict** måste tillhandahållas.

#### <a name="overwrite-an-azure-file-share"></a>Skriva över en Azure-filresurs

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Skriva över en Azure-fil

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Nästa steg

[Läs mer om](restore-afs.md) hur du återställer Azure-filer i Azure-portalen.
