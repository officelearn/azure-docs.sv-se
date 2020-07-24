---
title: Kopiera Managed disks to Subscription (Linux) – PowerShell-exempel
description: Azure PowerShell skript exempel – kopiera (eller flytta) hanterade diskar till samma eller en annan prenumeration
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: ca594801c0886903f395dddb7ccbfccb42bff15f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069383"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell-linux"></a>Kopiera hanterade diskar i samma prenumeration eller en annan prenumeration med PowerShell (Linux)

Det här skriptet skapar en kopia av en befintlig hanterad disk i samma prenumeration eller i en annan prenumeration. Den nya disken skapas i samma region som den överordnade hanterade disken.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ny hanterad disk i målprenumerationen med den hanterade källdiskens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Skapar diskkonfigurationen som används för att skapa en disk. Den innehåller resurs-ID:t för den överordnade disken och platsen som är samma som platsen för den överordnade disken.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Skapar en disk med diskkonfiguration, disknamn och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
