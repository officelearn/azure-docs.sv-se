---
title: Azure PowerShell-skriptexempel – Skapa en hanterad disk från en ögonblicksbild | Microsoft Docs
description: Azure PowerShell-skriptexempel – Skapa en hanterad disk från en ögonblicksbild
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 055a24aac01b58178e8a4e24986786eeffff3430
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249699"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Skapa en hanterad disk från en ögonblicksbild med PowerShell

Det här skriptet skapar en hanterad disk från en ögonblicksbild. Du kan använda den för att återställa en virtuell dator från ögonblicksbilder av operativsystem och datadiskar. Skapa OS och hanterade datadiskar från respektive ögonblicksbilder, och skapa sedan en ny virtuell dator genom att koppla hanterade diskar. Du kan även återställa datadiskar på en befintlig virtuell dator genom att koppla datadiskar som skapats från ögonblicksbilder.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en ögonblicksbild. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | Hämtar ögonblicksbildsegenskaper.  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Skapar den diskkonfiguration som används för att skapa en disk. Den innehåller resurs-ID:t för den överordnade ögonblicksbilden, platsen (samma som platsen för den överordnade ögonblicksbilden) och lagringstypen.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Skapar en disk med diskkonfiguration, disknamn och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg


[Skapa en virtuell dator från en hanterad disk](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
