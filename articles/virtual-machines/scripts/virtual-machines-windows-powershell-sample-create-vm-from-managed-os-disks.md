---
title: Azure PowerShell-skript Sample - skapa en virtuell dator genom att koppla en hanterade diskar som OS-disk | Microsoft Docs
description: Azure PowerShell-skript Sample - skapa en virtuell dator genom att koppla en hanterade diskar som OS-disk
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ec532811e94647c8a04b9faf9474f6749969f83e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Skapa en virtuell dator med en befintlig OS-disk hanterade med PowerShell

Det här skriptet skapar en virtuell dator genom att koppla en befintlig hanterade disk som OS-disken. Använd det här skriptet i föregående scenarier:
* Skapa en virtuell dator från en befintlig hanterade OS-disk som har kopierats från en hanterad disk i en annan prenumeration
* Skapa en virtuell dator från en befintlig hanterade disk som har skapats från en särskild VHD-fil 
* Skapa en virtuell dator från en befintlig hanterade OS-disk som har skapats från en ögonblicksbild 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att hämta egenskaper för hanterade diskar, ansluter hanterade diskar till en ny virtuell dator och skapa en virtuell dator. Varje objekt i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Hämtar diskobjektet baserat på namnet och resursgruppens namn på en disk. ID-egenskapen för objektet returnerade disk används för att ansluta disken till en ny virtuell dator |
| [Ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Skapar en VM-konfiguration. Den här konfigurationen omfattar information som VM-namn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används under Skapa en virtuell dator. |
| [Ange AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Bifogar en hanterade diskar med Id-egenskapen för disken som OS-disken till en ny virtuell dator |
| [Ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en offentlig IP-adress. |
| [Ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Skapar ett nätverksgränssnitt. |
| [Ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Skapa en virtuell dator. |
|[Ta bort AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).