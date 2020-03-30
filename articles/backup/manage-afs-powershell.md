---
title: Hantera Azure-filresurssäkerhetskopior med PowerShell
description: Lär dig hur du använder PowerShell för att hantera och övervaka Azure-filresurser som backas upp av Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083178"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Hantera Azure-filresurssäkerhetskopior med PowerShell

I den här artikeln beskrivs hur du använder Azure PowerShell för att hantera och övervaka Azure-filresurser som backas upp av Azure Backup-tjänsten.

> [!WARNING]
> Kontrollera att PS-versionen uppgraderas till den lägsta versionen för Az.RecoveryServices 2.6.0 för AFS-säkerhetskopior. Mer information finns [i avsnittet](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) om kravet på den här ändringen.

## <a name="modify-the-protection-policy"></a>Ändra skyddsprincipen

Om du vill ändra principen som används för säkerhetskopiering av Azure-filresursen använder du [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Ange relevant säkerhetskopieringsobjekt och den nya säkerhetskopieringsprincipen.

I följande exempel ändras **testAzureFS-skyddsprincipen** från **dailyafs** till **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Spåra säkerhetskopiering och återställning av jobb

Säkerhetskopierings- och återställningsåtgärder på begäran returnerar ett jobb tillsammans med ett ID, vilket visas när du [kör en säkerhetskopiering på begäran](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Använd cmdleten [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) för att spåra jobbets förlopp och information.

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

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhetskopieringsjobb och *ta bort* alla återställningspunkter
* Stoppa alla framtida säkerhetskopieringsjobb men *lämna* återställningspunkterna

Det kan finnas en kostnad som är associerad med att lämna återställningspunkterna i lagring, eftersom de underliggande ögonblicksbilder som skapats av Azure Backup kommer att behållas. Fördelen med att lämna återställningspunkterna är dock att du kan återställa filresursen senare, om så önskas. Information om kostnaden för att lämna återställningspunkterna finns i [prisinformationen](https://azure.microsoft.com/pricing/details/storage/files/). Om du väljer att ta bort alla återställningspunkter kan du inte återställa filresursen.

## <a name="stop-protection-and-retain-recovery-points"></a>Stoppa skyddet och behåll återställningspunkter

Om du vill stoppa skyddet när du behåller data använder du cmdleten [Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

Följande exempel stoppar skyddet för filresursresursen *afsfileshare* men behåller alla återställningspunkter:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Attributet Job ID i utdata motsvarar jobb-ID:n för jobbet som skapas av säkerhetskopieringstjänsten för din "stop protection"-åtgärd. Om du vill spåra status för jobbet använder du cmdleten [Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Stoppa skyddet utan att behålla återställningspunkter

Om du vill stoppa skyddet utan att behålla återställningspunkter använder du cmdleten [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) och lägger till parametern **-RemoveRecoveryPoints.**

I följande exempel stoppas skyddet för filresursresursen *afsfileshare* utan att återställningspunkter behålls:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](manage-afs-backup.md) hur du hanterar Azure-filresurssäkerhetskopior i Azure-portalen.
