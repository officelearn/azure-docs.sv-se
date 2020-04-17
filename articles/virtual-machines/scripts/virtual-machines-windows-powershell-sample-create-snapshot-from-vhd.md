---
title: Skapa en ögonblicksbild från en virtuell hårddisk för att skapa flera identiska hanterade diskar - PowerShell-exempel
description: Azure PowerShell-skriptexempel – Skapa en ögonblicksbild från en VHD för att skapa flera identiska hanterade diskar på kort tid
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 9da8adb786baebcb2e798c7ffb5998aca0f68265
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459261"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Skapa en ögonblicksbild från en VHD för att skapa flera identiska hanterade diskar på kort tid med PowerShell

Skriptet skapar en ögonblicksbild från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration. Använd skriptet för att importera en särskild (inte generaliserad/Sysprep-avbildad) VHD till en ögonblicksbild och använd sedan ögonblicksbilden för att skapa flera identiska hanterade diskar på kort tid. Du kan också använda det för att importera en data-VHD till en ögonblicksbild och sedan använda ögonblicksbilden för att skapa flera hanterade diskar på kort tid. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en ögonblicksbild](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Skapa en virtuell dator genom att koppla en hanterad disk som OS-disk](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
