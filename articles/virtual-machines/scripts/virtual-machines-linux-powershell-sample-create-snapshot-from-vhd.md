---
title: VHD-ögonblicksbild för att göra många identiska hanterade diskar (Linux) – PowerShell
description: Azure PowerShell-skriptexempel – Skapa en ögonblicksbild från en VHD för att skapa flera identiska hanterade diskar på kort tid
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 8a80056ed3368f6a2a5c4ad7fa31424524e824b2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079935"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-linux"></a>Skapa en ögonblicks bild från en virtuell hård disk för att skapa flera identiska hanterade diskar på kort tid med PowerShell (Linux)

Skriptet skapar en ögonblicksbild från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration. Använd skriptet för att importera en särskild (inte generaliserad/Sysprep-avbildad) VHD till en ögonblicksbild och använd sedan ögonblicksbilden för att skapa flera identiska hanterade diskar på kort tid. Du kan också använda det för att importera en data-VHD till en ögonblicksbild och sedan använda ögonblicksbilden för att skapa flera hanterade diskar på kort tid.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon till att skapa en hanterad disk från en VHD i en annan prenumeration. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Skapar diskkonfigurationen som används för att skapa en disk. Den innehåller lagringstyp, plats, resurs-ID för det lagringskonto där överordnad VHD lagras, samt VHD-URI för överordnad VHD. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Skapar en disk med diskkonfiguration, disknamn och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en ögonblicksbild](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
