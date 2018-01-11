---
title: "Säkerhetskopiera Azure Stack | Microsoft Docs"
description: "Utföra en säkerhetskopiering på begäran på Azure-stacken med backup på plats."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: df1f4c6fadd08b17a1a1eb8bbe41ab71ae4729fc
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="back-up-azure-stack"></a>Säkerhetskopiera Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Utföra en säkerhetskopiering på begäran på Azure-stacken med backup på plats. Om du behöver aktivera tjänsten infrastruktur säkerhetskopiering finns [Aktivera säkerhetskopiering för Azure-stacken från administrationsportalen](azure-stack-backup-enable-backup-console.md).

## <a name="start-azure-stack-backup"></a>Starta Azure Stack-säkerhetskopiering

Öppna Windows PowerShell med en förhöjd behörighet och kör följande kommandon:

   ```powershell
   Start-AzSBackup -Location $location
   ```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Bekräfta slutfördes på administrationsportalen för Windows Azure

1. Öppna Azure Stack-administrationsportalen på [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Välj **fler tjänster** > **infrastruktur säkerhetskopiering**. Välj **Configuration** i den **infrastruktur säkerhetskopiering** bladet.
3. Hitta de **namn** och **datum slutförd** på säkerhetskopieringen på **tillgängliga säkerhetskopieringar** lista.
4. Kontrollera den **tillstånd** är **lyckades**.

Du kan också bekräfta säkerhetskopieringen slutförts från administrationsportalen. Gå till`\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

## <a name="next-steps"></a>Nästa steg

- Läs mer om arbetsflödet för att återställa från en dataförlust inträffat. Se [återställa från oåterkallelig dataförlust](azure-stack-backup-recover-data.md).