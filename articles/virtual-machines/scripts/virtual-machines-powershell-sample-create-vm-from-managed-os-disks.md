---
title: Skapa virtuell dator genom att koppla hanterad disk som OS-disk – PowerShell
description: Skriptexempel för Azure PowerShell – Skapa en virtuell dator genom att ansluta en hanterad disk och använda den som operativsystemdisk
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 038ec433a4f27eec5ace537ff9b08e5ff6c2f6a9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323214"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Skapa en virtuell dator med hjälp av en befintlig hanterad operativsystemdisk med PowerShell 

Det här skriptet skapar en virtuell dator genom att ansluta en befintlig hanterad disk och använda den som operativsystemsdisk. Använd skriptet i dessa scenarion:
* Skapa en virtuell dator utifrån en befintlig hanterad operativsystemsdisk som har kopierats från en hanterad disk i en annan prenumeration
* Skapa en virtuell dator utifrån en befintlig hanterad disk som har skapats med en särskild VHD-fil 
* Skapa en virtuell dator utifrån en befintlig hanterad operativsystemsdisk som har skapats med en ögonblicksbild 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta egenskaper för hanterade diskar, ansluta en hanterad disk till en ny virtuell dator och skapa en virtuell dator. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | Hämtar diskobjektet baserat på namnet och resursgruppen för en disk. ID-egenskapen för det returnerade diskobjektet används för att koppla disken till en ny virtuell dator |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Lägger till en hanterad disk och använder ID-egenskapen för disken som operativsystemdisk för en ny virtuell dator |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar ett nätverksgränssnitt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapa en virtuell dator. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

För Marketplace-avbildningar använder du [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) för att konfigurera planinformationen.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
