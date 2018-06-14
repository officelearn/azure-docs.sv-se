---
title: Exempel på Azure PowerShell-skript - säkerhetskopiering av en virtuell Azure-dator | Microsoft Docs
description: Exempel på Azure PowerShell-skript - säkerhetskopiering av en virtuell Azure-dator
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4376add4a2e51806bd5db228ad2fe2afcf2e4f57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842652"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Säkerhetskopiera en krypterad virtuella Azure-datorn med PowerShell

Det här skriptet skapar ett Recovery Services-valv med Geo-redundant lagring (GRS) för en krypterad virtuella Azure-datorn. Standardprincipen för skydd tillämpas på valvet. Principen genererar en daglig säkerhetskopiering för den virtuella datorn och behåller varje säkerhetskopiering i 30 dagar. Skriptet även utlöser den första återställningspunkten för den virtuella datorn och behåller den återställningspunkten 365 dagar. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa distributionen. Varje objekt i tabellen länkar till kommandot viss dokumentation.


| Kommando | Anteckningar | 
|---|---| 
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp som är lagrade i alla resurser. | 
| [Ny AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Skapar en återställningstjänstvalvet för att lagra säkerhetskopior. | 
| [Ange AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Anger säkerhetskopiera lagringsegenskaper på Recovery Services-valvet. | 
| [Ny AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Skapar protection-principen med hjälp av schema princip och bevarandeprincip i Recovery Services-valvet. | 
| [Ange AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Anger behörigheter på Nyckelvalvet att ge service principal åtkomst till krypteringsnycklarna. | 
| [Aktivera AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Säkerhetskopierar för ett objekt med en angiven protection-princip för säkerhetskopiering. | 
| [Ange AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Ändrar en befintlig princip för säkerhetskopiering skydd. | 
| [Säkerhetskopiering AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Startar en säkerhetskopia för en skyddad Azure Backup objekt som inte är kopplad till schemat för säkerhetskopiering. |
| [Vänta AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Väntar på ett Azure Backup-jobb ska slutföras. | 
| [Ta bort AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

