---
title: Skriptexempel för Azure PowerShell – Skapa en virtuell dator genom att ansluta en hanterad disk och använda den som operativsystemdisk | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Skapa en virtuell dator genom att ansluta en hanterad disk och använda den som operativsystemdisk
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 8bee1c3b5863cfe8b4b2e50a927b0eb15576f02f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249689"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Skapa en virtuell dator med hjälp av en befintlig hanterad operativsystemdisk med PowerShell

Det här skriptet skapar en virtuell dator genom att ansluta en befintlig hanterad disk och använda den som operativsystemsdisk. Använd skriptet i dessa scenarion:
* Skapa en virtuell dator utifrån en befintlig hanterad operativsystemsdisk som har kopierats från en hanterad disk i en annan prenumeration
* Skapa en virtuell dator utifrån en befintlig hanterad disk som har skapats med en särskild VHD-fil 
* Skapa en virtuell dator utifrån en befintlig hanterad operativsystemsdisk som har skapats med en ögonblicksbild 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta egenskaper för hanterade diskar, ansluta en hanterad disk till en ny virtuell dator och skapa en virtuell dator. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | Hämtar diskobjektet baserat på namnet och resursgruppen för en disk. ID-egenskapen för det returnerade diskobjektet används för att koppla disken till en ny virtuell dator |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | Lägger till en hanterad disk och använder ID-egenskapen för disken som operativsystemdisk för en ny virtuell dator |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Skapar ett nätverksgränssnitt. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Skapa en virtuell dator. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

För Marketplace-avbildningar använder du [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan?view=azurermps-6.7.0) för att konfigurera planinformationen.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
