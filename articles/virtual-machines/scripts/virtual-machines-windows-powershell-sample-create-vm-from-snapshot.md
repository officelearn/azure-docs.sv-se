---
title: Skapa virtuell dator från ögonblicks bild (Windows) – PowerShell-exempel
description: Azure PowerShell-skriptexempel – Skapa en virtuell dator från en ögonblicksbild
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 8ec7b8655a499057e91f138411529a5f271ea511
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082366"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-windows"></a>Skapa en virtuell dator från en ögonblicks bild med PowerShell (Windows)

Det här skriptet skapar en virtuell dator från en ögonblicksbild av en OS-disk. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta egenskaper för en ögonblicksbild, för att skapa en hanterad disk från en ögonblicksbild och för att skapa en virtuell dator. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Hämtar en ögonblicksbild med namnet på ögonblicksbilden. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Skapar en diskkonfiguration. Den här konfigurationen används vid diskgenereringen. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Skapar en hanterad disk. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Kopplar den hanterade disken som operativsystemsdisken till den virtuella datorn |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar ett nätverksgränssnitt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapar en virtuell dator. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
