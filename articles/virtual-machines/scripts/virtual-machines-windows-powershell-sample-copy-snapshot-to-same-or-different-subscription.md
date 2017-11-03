---
title: "Azure PowerShell skriptexempel - kopia (flytta) ögonblicksbild av en hanterad disk till samma eller olika prenumeration | Microsoft Docs"
description: "Azure PowerShell skriptexempel - kopia (flytta) ögonblicksbild av en hanterad disk till samma eller en annan prenumeration"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiera ögonblicksbild av hanterade diskar i samma prenumeration eller annan prenumeration med PowerShell

Det här skriptet skapar en kopia av en ögonblicksbild i samma samma prenumeration eller annan prenumeration. Använd det här skriptet för att flytta en ögonblicksbild till annan prenumeration för datalagring. Lagra ögonblicksbilder i annan prenumeration för att skydda mot oavsiktlig borttagning av ögonblicksbilder i prenumerationen huvudsakliga. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen använder ögonblicksbilden käll-Id. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Skapar ögonblicksbild konfigurationen som används för att skapa en ögonblicksbild. Den innehåller resurs-Id för överordnad ögonblicksbild och plats som är samma som den överordnade ögonblicksbilden.  |
| [Ny AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Skapar en ögonblicksbild med hjälp av ögonblicksbilder konfiguration, namnet på ögonblicksbilder och resursgruppens namn som parametrar skickades. |


## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).