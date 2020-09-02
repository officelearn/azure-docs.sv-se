---
title: Skapa en hanterad disk från Snapshot – PowerShell-exempel
description: Azure PowerShell-skriptexempel – Skapa en hanterad disk från en ögonblicksbild
services: virtual-machines
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
ms.openlocfilehash: f3a890083e4763bcff95a1361bed69907b80e033
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323198"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Skapa en hanterad disk från en ögonblicksbild med PowerShell 

Det här skriptet skapar en hanterad disk från en ögonblicksbild. Du kan använda den för att återställa en virtuell dator från ögonblicksbilder av operativsystem och datadiskar. Skapa OS och hanterade datadiskar från respektive ögonblicksbilder, och skapa sedan en ny virtuell dator genom att koppla hanterade diskar. Du kan även återställa datadiskar på en befintlig virtuell dator genom att koppla datadiskar som skapats från ögonblicksbilder.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en ögonblicksbild. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Hämtar ögonblicksbildsegenskaper.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Skapar diskkonfigurationen som används för att skapa en disk. Den innehåller resurs-ID:t för den överordnade ögonblicksbilden, platsen (samma som platsen för den överordnade ögonblicksbilden) och lagringstypen.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Skapar en disk med diskkonfiguration, disknamn och resursgruppens namn som parametrar. |


## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
