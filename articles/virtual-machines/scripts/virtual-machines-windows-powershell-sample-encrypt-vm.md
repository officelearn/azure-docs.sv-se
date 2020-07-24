---
title: Azure PowerShell-skriptexempel – kryptera en virtuell Windows-dator
description: Azure PowerShell-skriptexempel – kryptera en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: 395adb0e4e9a01283a8b5bb47fa9cdd4ec55f66f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058894"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Kryptera en virtuell Windows-dator med Azure PowerShell

Det här skriptet skapar ett säkert Azure Key Vault, krypteringsnycklar, Azure Active Directory-tjänstens huvudnamn och en virtuell Windows-dator (VM). Den virtuella datorn krypteras sedan med hjälp av krypteringsnyckeln från Key Vault och autentiseringsuppgifterna för tjänstens huvudnamn.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

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
| [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) | Skapar ett Azure Key Vault för lagring av säkra data, till exempel krypteringsnycklar. |
| [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) | Skapar en krypteringsnyckel i Key Vault. |
| [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) | Skapar ett huvudnamn för Azure Active Directory-tjänsten för att autentisera och kontrollera åtkomst till krypteringsnycklarna på ett säkert sätt. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Anger behörigheter för Key Vault så att tjänstens huvudnamn får åtkomst till krypteringsnycklarna. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en nätverkssäkerhetsgrupp. Det här kommandot öppnar också port 80 och anger administrativa autentiseringsuppgifter. |
| [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) | Hämtar nödvändig information för nyckelvalvet |
| [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) | Aktiverar kryptering på en virtuell dator med hjälp av autentiseringsuppgifterna för tjänstens huvudnamn och krypteringsnyckeln. |
| [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Visar status för krypteringsprocessen för den virtuella datorn. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
