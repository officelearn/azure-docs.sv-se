---
title: Azure PowerShell-skriptexempel – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration | Microsoft Docs
description: Azure PowerShell-skriptexempel – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 8447ffc27068fbbdf5793acdc51bb9724ee41cb8
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976732"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiera en ögonblicksbild av en hanterad disk i sammaprenumeration eller en annan prenumeration med PowerShell

Det här skriptet skapar en kopia av en ögonblicksbild i samma prenumeration eller i en annan prenumeration. Använd det här skriptet för att flytta en ögonblicksbild till annan prenumeration för datakvarhållning. Genom att lagra ögonblicksbilder i en annan prenumeration skyddas du mot oavsiktlig borttagning av ögonblicksbilder i din huvudprenumeration. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Skapar konfiguration av ögonblicksbild som används för att skapa ögonblicksbilder. Den innehåller resurs-ID:t för den överordnade ögonblicksbilden och den plats som är samma som för den överordnade ögonblicksbilden.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Skapar en ögonblicksbild med hjälp av konfiguration av ögonblicksbild, namn på ögonblicksbild och resursgruppens namn som parametrar. |


## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).