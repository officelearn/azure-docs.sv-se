---
title: Skapa en hanterad disk från en VHD-fil i ett lagrings konto i ett prenumerations-PowerShell-exempel
description: Exempelskript för Azure PowerShell – Skapa en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323194"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Skapa en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration med PowerShell

Det här skriptet skapar en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration. Använd det här skriptet för att importera en specialiserad (inte generaliserad/syspreppad) VHD till en hanterad OS-disk för att skapa en virtuell dator. Du kan också använda det för att importera en data-VHD till en hanterad datadisk. 

Skapa inte flera identiska hanterade diskar från en VHD-fil under kort tid. När du skapar hanterade diskar från en VHD-fil, skapas en blobögonblicksbild av VHD-filen som sedan används för att skapa hanterade diskar. Endast en blobögonblicksbild kan skapas per minut, vilket medför fel när disken skapas på grund av nätverksbegränsningen. För att undvika denna begränsning kan du skapa en [hanterad ögonblicksbild från VHD-filen](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) och sedan använda den hanterade ögonblicksbilden till att skapa flera hanterade diskar på kort tid. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon till att skapa en hanterad disk från en VHD i en annan prenumeration. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Skapar diskkonfigurationen som används för att skapa en disk. Den innehåller lagrings typ, plats, resurs-ID för det lagrings konto där den överordnade virtuella hård disken lagras, VHD-URI för den överordnade virtuella hård disken. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Skapar en disk med diskkonfiguration, disknamn och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator genom att koppla en hanterad disk som OS-disk](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
