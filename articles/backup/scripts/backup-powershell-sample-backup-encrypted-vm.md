---
title: Skriptexempel för Azure PowerShell – Säkerhetskopiera en virtuell Azure-dator | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Säkerhetskopiera en virtuell Azure-dator
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 915c5f6c8e8de1b5a7a7590ba41125cbff7b8f36
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497646"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Säkerhetskopiera en krypterad virtuell Azure-dator med PowerShell

Det här skriptet skapar ett Recovery Services-valv med geo-redundant lagring (GRS) för en krypterad virtuell Azure-dator. Standardskyddsprincipen tillämpas på valvet. Principen genererar en daglig säkerhetskopia för den virtuella datorn och behåller varje säkerhetskopia i 30 dagar. Skriptet utlöser även den första återställningspunkten för den virtuella datorn och behåller den återställningspunkten i 365 dagar. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.


| Kommando | Anteckningar | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. | 
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Skapar ett Recovery Services-valv för lagring av säkerhetskopior. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Anger egenskaper för lagring av säkerhetskopior i Recovery Services-valvet. | 
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Skapar skyddsprincip med hjälp av schemaläggningsprincip och bevarandeprincip i Recovery Services-valvet. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Anger behörigheter för Key Vault så att tjänstens huvudnamn får åtkomst till krypteringsnycklarna. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Aktiverar säkerhetskopiering för ett objekt med en angiven skyddsprincip för säkerhetskopiering. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Ändrar en befintlig skyddsprincip för säkerhetskopiering. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Startar en säkerhetskopiering för ett skyddat Azure Backup-objekt som inte är kopplat till säkerhetskopieringsschemat. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Väntar på att ett Azure Backup-jobb ska slutföras. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

