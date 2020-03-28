---
title: Kopiera ögonblicksbild av en hanterad disk till en prenumeration - PowerShell-exempel
description: Azure PowerShell-skriptexempel – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 4189822e493b8906152e5a73d87cffb1d05f31d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75368852"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiera en ögonblicksbild av en hanterad disk i sammaprenumeration eller en annan prenumeration med PowerShell

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för följande scenarier:

1. Migrera en ögonblicksbild i Premium-lagring (Premium_LRS) till standardlagring (Standard_LRS eller Standard_ZRS) för att minska din kostnad.
1. Migrera en ögonblicksbild från lokalt redundant lagring (Premium_LRS, Standard_LRS) till zon redundant lagring (Standard_ZRS) för att dra nytta av den högre tillförlitligheten hos ZRS-lagring.
1. Flytta en ögonblicksbild till olika prenumerationer i samma region för längre kvarhållning.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Skapar konfiguration av ögonblicksbild som används för att skapa ögonblicksbilder. Den innehåller resurs-ID:t för den överordnade ögonblicksbilden och den plats som är samma som för den överordnade ögonblicksbilden.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) | Skapar en ögonblicksbild med hjälp av konfiguration av ögonblicksbild, namn på ögonblicksbild och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
