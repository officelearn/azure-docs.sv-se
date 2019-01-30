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
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 0fed6751d326c5da4431e953f7ded9c12688871f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250052"
---
# <a name="back-up-azure-stack"></a>Säkerhetskopiera Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Utföra en säkerhetskopiering på begäran på Azure Stack med säkerhetskopiering på plats. Anvisningar om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure Stack ](azure-stack-powershell-install.md). Om du vill logga in på Azure Stack, se [med hjälp av administratörsportalen i Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Starta Säkerhetskopiering i Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Starta en ny säkerhetskopia utan jobbförlopp spårning
Använd Start AzSBackup för att starta en ny säkerhetskopia omedelbart med inga jobbförlopp spårning.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Starta Säkerhetskopiering i Azure Stack med jobbförlopp spårning
Använd Start-AzSBackup för att starta en ny säkerhetskopia med den **- AsJob** parametern och spara det som en variabel som ska följa förloppet för jobbet.

> [!NOTE]
> Säkerhetskopieringsjobbet visas som har slutförts i portalen ungefär 10 – 15 minuter innan jobbet slutförs.
>
> Den aktuella statusen observeras därför bättre via koden nedan.

> [!IMPORTANT]
> Första 1 millisekund fördröjningen introduceras eftersom koden är för snabb registreras jobbet korrekt och kommer tillbaka utan **PSBeginTime** och i sin tur utan **tillstånd** för jobbet.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
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
2. Välj **alla tjänster**, och sedan under den **ADMINISTRATION** kategori väljer > **infrastruktur för säkerhetskopiering**. Välj **Configuration** i den **infrastruktur för säkerhetskopiering** bladet.
3. Hitta den **namn** och **datum slutförts** på säkerhetskopieringen på **tillgängliga säkerhetskopior** lista.
4. Kontrollera den **tillstånd** är **lyckades**.

## <a name="next-steps"></a>Nästa steg

Läs mer om arbetsflödet för att återställa från en dataförlust inträffat. Se [fort oåterkallelig dataförlust](azure-stack-backup-recover-data.md).
