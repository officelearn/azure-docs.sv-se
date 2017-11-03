---
title: Azure PowerShell skriptexempel - kopia (flytta) hanterade diskar till samma eller olika prenumeration | Microsoft Docs
description: Azure PowerShell skriptexempel - kopia (flytta) hanterade diskar till samma eller en annan prenumeration
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
ms.openlocfilehash: a14b25236fc233ef7b98b29e62a1270c5e4d8f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Kopiera hanterade diskar i samma prenumeration eller annan prenumeration med PowerShell

Det här skriptet skapar en kopia av en befintlig hanterade disk i samma prenumeration eller annan prenumeration. Den nya disken skapas i samma region som den överordnade hantera disken.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en ny hanterade disk i målprenumerationen med ID: T för den hanterade källdisken. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Skapar diskkonfigurationen som används för att skapa en disk. Den innehåller resurs-Id för överordnad disk och plats som är samma som plats för överordnad disk.  |
| [Ny AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Skapar en disk med diskkonfigurationen disknamnet och resursgruppens namn som parametrar skickades. |


## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).