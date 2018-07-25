---
title: Säkerhetskopiera Azure Stack | Microsoft Docs
description: Utföra en säkerhetskopiering på begäran på Azure Stack med säkerhetskopiering på plats.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242894"
---
# <a name="back-up-azure-stack"></a>Säkerhetskopiera Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Utföra en säkerhetskopiering på begäran på Azure Stack med säkerhetskopiering på plats. Anvisningar om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure Stack ](azure-stack-powershell-install.md). Om du vill logga in på Azure Stack, se [konfigurera operator-miljö och logga in på Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Starta Säkerhetskopiering i Azure Stack

Använd Start-AzSBackup för att starta en ny säkerhetskopia med - AsJob variabel att spåra förloppet. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Bekräfta säkerhetskopieringen slutfördes med PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- Resultatet bör se ut som följande:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Bekräfta säkerhetskopieringen slutfördes i administrationsportalen

1. Öppna Azure Stack-administrationsportalen på [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Välj **fler tjänster** > **infrastruktur för säkerhetskopiering**. Välj **Configuration** i den **infrastruktur för säkerhetskopiering** bladet.
3. Hitta den **namn** och **datum slutförts** på säkerhetskopieringen på **tillgängliga säkerhetskopior** lista.
4. Kontrollera den **tillstånd** är **lyckades**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om arbetsflödet för att återställa från en dataförlust inträffat. Se [fort oåterkallelig dataförlust](azure-stack-backup-recover-data.md).