---
title: Azure PowerShell-skript Sample - kryptera Windows VM | Microsoft Docs
description: Azure PowerShell-skript Sample - kryptera Windows VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: iainfou
ms.openlocfilehash: f405cdaf61d6aaafa8568a9d7f21614071285c17
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Kryptera en virtuell dator i Windows med Azure PowerShell

Det här skriptet skapar en säker Azure Key Vault, krypteringsnycklar, Azure Active Directory-tjänstens huvudnamn och ett Windows-dator (VM). Den virtuella datorn krypteras sedan med hjälp av krypteringsnyckeln från Nyckelvalvet och tjänstens huvudnamn autentiseringsuppgifter.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

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
| [Ny AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) | Skapar ett Azure Key Vault för lagring av säkra data, till exempel krypteringsnycklar. |
| [Lägg till AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) | Skapar en krypteringsnyckel i Nyckelvalvet. |
| [Ny AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Skapar ett Azure Active Directory-tjänstens huvudnamn för att autentisera och kontrollera åtkomst till krypteringsnycklarna på ett säkert sätt. |
| [Ange AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Anger behörigheter på Nyckelvalvet att ge service principal åtkomst till krypteringsnycklarna. |
| [Ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Skapar den virtuella datorn och ansluter till nätverkskort, virtuella nätverk, undernät och nätverkssäkerhetsgruppen. Det här kommandot också öppnar port 80 och anger administratörsautentisering. |
| [Get-AzureRmKeyVault](/powershell/module/azurerm.keyvault/get-azurermkeyvault) | Hämtar nödvändig information för Nyckelvalvet |
| [Ange AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) | Aktiverar kryptering på en virtuell dator med hjälp av service principal autentiseringsuppgifter och krypteringsnyckeln. |
| [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) | Visar status för krypteringsprocessen VM. |
| [Ta bort AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
