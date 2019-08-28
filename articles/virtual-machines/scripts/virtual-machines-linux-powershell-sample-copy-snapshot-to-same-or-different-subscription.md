---
title: Azure PowerShell-skriptexempel – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration | Microsoft Docs
description: Azure PowerShell-skriptexempel – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: f7dbda75eaf403e9bd99dce63e5ed37118fd83ea
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081370"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiera en ögonblicksbild av en hanterad disk i sammaprenumeration eller en annan prenumeration med PowerShell

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för följande scenarier:

1. Migrera en ögonblicks bild i Premium Storage (Premium_LRS) till standard lagrings utrymme (Standard_LRS eller Standard_ZRS) för att minska kostnaderna.
1. Migrera en ögonblicks bild från lokalt redundant lagring (Premium_LRS, Standard_LRS) till zon redundant lagring (Standard_ZRS) för att dra nytta av högre tillförlitlighet för ZRS-lagring.
1. Flytta en ögonblicks bild till en annan prenumeration i samma region för längre kvarhållning.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Skapar konfiguration av ögonblicksbild som används för att skapa ögonblicksbilder. Den innehåller resurs-ID:t för den överordnade ögonblicksbilden och den plats som är samma som för den överordnade ögonblicksbilden.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Skapar en ögonblicksbild med hjälp av konfiguration av ögonblicksbild, namn på ögonblicksbild och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).