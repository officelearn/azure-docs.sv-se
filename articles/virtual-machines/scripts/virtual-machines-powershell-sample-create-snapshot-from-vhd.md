---
title: VHD-ögonblicksbild för att göra många identiska Managed disks (Windows) – PowerShell
description: Azure PowerShell-skriptexempel – Skapa en ögonblicksbild från en VHD för att skapa flera identiska hanterade diskar på kort tid
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 47ade94ad2e0be0ce44d346336c6c7eea4fca78b
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323217"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>Skapa en ögonblicks bild från en virtuell hård disk för att skapa flera identiska hanterade diskar på kort tid med PowerShell (Windows)

Skriptet skapar en ögonblicksbild från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration. Använd skriptet för att importera en särskild (inte generaliserad/Sysprep-avbildad) VHD till en ögonblicksbild och använd sedan ögonblicksbilden för att skapa flera identiska hanterade diskar på kort tid. Du kan också använda det för att importera en data-VHD till en ögonblicksbild och sedan använda ögonblicksbilden för att skapa flera hanterade diskar på kort tid. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en ögonblicksbild](virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Skapa en virtuell dator genom att koppla en hanterad disk som OS-disk](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
