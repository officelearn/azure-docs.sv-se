---
title: Ögonblicks bild av hanterad disk till prenumeration (Windows) – PowerShell
description: Azure PowerShell-skriptexempel – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration
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
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: b5ac47c8074a3a7bec86a35075a98a141ea0ccd1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082434"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>Kopiera ögonblicks bild av en hanterad disk i samma prenumeration eller en annan prenumeration med PowerShell (Windows)

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för följande scenarier:

1. Migrera en ögonblicks bild i Premium Storage (Premium_LRS) till standard lagring (Standard_LRS eller Standard_ZRS) för att minska kostnaderna.
1. Migrera en ögonblicks bild från lokalt redundant lagring (Premium_LRS Standard_LRS) till zonens redundanta lagring (Standard_ZRS) för att dra nytta av ZRS-lagringens högre tillförlitlighet.
1. Flytta en ögonblicks bild till en annan prenumeration i samma region för längre kvarhållning.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | Skapar konfiguration av ögonblicksbild som används för att skapa ögonblicksbilder. Den innehåller resurs-ID:t för den överordnade ögonblicksbilden och den plats som är samma som för den överordnade ögonblicksbilden.  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | Skapar en ögonblicksbild med hjälp av konfiguration av ögonblicksbild, namn på ögonblicksbild och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
