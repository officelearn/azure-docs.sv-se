---
title: Säkerhetskopiera Azure Stack | Microsoft Docs
description: Utföra en säkerhetskopiering på begäran på Azure-stacken med backup på plats.
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
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>Säkerhetskopiera Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Utföra en säkerhetskopiering på begäran på Azure-stacken med backup på plats. Om du behöver aktivera tjänsten infrastruktur säkerhetskopiering finns [Aktivera säkerhetskopiering för Azure-stacken från administrationsportalen](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Instruktioner om hur du konfigurerar PowerShell-miljö finns i [installera PowerShell för Azure-stacken ](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Starta Azure Stack-säkerhetskopiering

Öppna Windows PowerShell med en förhöjd behörighet i operatorn management-miljö och kör följande kommandon:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Bekräfta slutfördes på administrationsportalen för Windows Azure

1. Öppna Azure Stack-administrationsportalen på [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Välj **fler tjänster** > **infrastruktur säkerhetskopiering**. Välj **Configuration** i den **infrastruktur säkerhetskopiering** bladet.
3. Hitta de **namn** och **datum slutförd** på säkerhetskopieringen på **tillgängliga säkerhetskopieringar** lista.
4. Kontrollera den **tillstånd** är **lyckades**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Nästa steg

- Läs mer om arbetsflödet för att återställa från en dataförlust inträffat. Se [återställa från oåterkallelig dataförlust](azure-stack-backup-recover-data.md).
