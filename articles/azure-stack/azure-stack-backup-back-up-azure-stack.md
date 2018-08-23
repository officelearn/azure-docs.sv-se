---
title: Säkerhetskopiera Azure Stack | Microsoft Docs
description: Utföra en säkerhetskopiering på begäran på Azure Stack med säkerhetskopiering på plats.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42059535"
---
# <a name="back-up-azure-stack"></a>Säkerhetskopiera Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Utföra en säkerhetskopiering på begäran på Azure Stack med säkerhetskopiering på plats. Anvisningar om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure Stack ](azure-stack-powershell-install.md). Om du vill logga in på Azure Stack, se [med hjälp av administratörsportalen i Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Starta Säkerhetskopiering i Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Starta en ny säkerhetskopia utan jobbförlopp spårning
Använd Start AzSBackup för att starta en ny säkerhetskopia omedelbart med inga jobbförlopp spårning.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Starta Säkerhetskopiering i Azure Stack med jobbförlopp spårning
Använd Start-AzSBackup för att starta en ny säkerhetskopia med variabeln - AsJob för att spåra förloppet för jobbet.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>Bekräfta säkerhetskopieringen har slutförts

### <a name="confirm-backup-has-completed-using-powershell"></a>Bekräfta säkerhetskopieringen har slutförts med hjälp av PowerShell
Använd följande PowerShell-kommandon för att säkerställa att säkerhetskopieringen har slutförts:

```powershell
   Get-AzsBackup
```

Resultatet bör se ut som följande:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Bekräfta säkerhetskopieringen har slutförts i administrationsportalen
Använd Azure Stack-administrationsportalen för att kontrollera att säkerhetskopiering har slutförts genom att följa dessa steg:

1. Öppna den [Azure Stack-administrationsportalen](azure-stack-manage-portals.md).
2. Välj **fler tjänster** > **infrastruktur för säkerhetskopiering**. Välj **Configuration** i den **infrastruktur för säkerhetskopiering** bladet.
3. Hitta den **namn** och **datum slutförts** på säkerhetskopieringen på **tillgängliga säkerhetskopior** lista.
4. Kontrollera den **tillstånd** är **lyckades**.

## <a name="next-steps"></a>Nästa steg

Läs mer om arbetsflödet för att återställa från en dataförlust inträffat. Se [fort oåterkallelig dataförlust](azure-stack-backup-recover-data.md).
