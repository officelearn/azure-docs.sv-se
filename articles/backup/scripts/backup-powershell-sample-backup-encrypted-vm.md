---
title: Skriptexempel för Azure PowerShell – Säkerhetskopiera en virtuell Azure-dator | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Säkerhetskopiera en virtuell Azure-dator
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bc54832b300bf7a70d067f07b9eb7cc67404f2e7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496811"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Säkerhetskopiera en krypterad virtuell Azure-dator med PowerShell

Det här skriptet skapar ett Recovery Services-valv med geo-redundant lagring (GRS) för krypterade virtuella Azure-datorer. Standardskyddsprincipen tillämpas på valvet. Principen genererar en daglig säkerhetskopia för den virtuella datorn och behåller varje säkerhetskopia i 30 dagar. Skriptet utlöser även den första återställningspunkten för den virtuella datorn och behåller den återställningspunkten i 365 dagar.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.


| Kommando | Anteckningar | 
|---|---| 
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. | 
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Skapar ett Recovery Services-valv för lagring av säkerhetskopior. | 
| [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties) | Anger egenskaper för lagring av säkerhetskopior i Recovery Services-valvet. | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Skapar skyddsprincip med hjälp av schemaläggningsprincip och bevarandeprincip i Recovery Services-valvet. | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Anger behörigheter för Key Vault så att tjänstens huvudnamn får åtkomst till krypteringsnycklarna. | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Aktiverar säkerhetskopiering för ett objekt med en angiven skyddsprincip för säkerhetskopiering. | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Ändrar en befintlig skyddsprincip för säkerhetskopiering. | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Startar en säkerhetskopiering för ett skyddat Azure Backup-objekt som inte är kopplat till säkerhetskopieringsschemat. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Väntar på att ett Azure Backup-jobb ska slutföras. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

