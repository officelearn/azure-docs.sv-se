---
title: Exempel på PowerShell-skript – säkerhetskopiera en virtuell Azure-dator
description: I den här artikeln får du lära dig hur du säkerhetskopierar en virtuell Azure-dator med hjälp av ett Azure PowerShell skript exempel.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: c45ee26bef0f9afbb0b9d03f1dcafee21a894f8a
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757565"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Säkerhetskopiera en krypterad virtuell Azure-dator med PowerShell

Det här skriptet skapar ett Recovery Services-valv med Geo-redundant lagring (GRS) för en krypterad virtuell Azure-dator. Standardskyddsprincipen tillämpas på valvet. Principen genererar en daglig säkerhetskopia för den virtuella datorn och behåller varje säkerhetskopia i 30 dagar. Skriptet utlöser även den första återställningspunkten för den virtuella datorn och behåller den återställningspunkten i 365 dagar.

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

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Skapar ett Recovery Services valv för att lagra säkerhets kopior. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Anger egenskaper för lagring av säkerhetskopior i Recovery Services-valvet. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Skapar skyddsprincip med hjälp av schemaläggningsprincip och bevarandeprincip i Recovery Services-valvet. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Anger behörigheter för Key Vault så att tjänstens huvudnamn får åtkomst till krypteringsnycklarna. |
| [Aktivera – AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Aktiverar säkerhetskopiering för ett objekt med en angiven skyddsprincip för säkerhetskopiering. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Ändrar en befintlig skyddsprincip för säkerhetskopiering. |
| [Säkerhetskopiera – AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Startar en säkerhetskopiering för ett skyddat Azure Backup-objekt som inte är kopplat till säkerhetskopieringsschemat. |
| [Vänta-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Väntar på att ett Azure Backup-jobb ska slutföras. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/new-azureps-module-az).
