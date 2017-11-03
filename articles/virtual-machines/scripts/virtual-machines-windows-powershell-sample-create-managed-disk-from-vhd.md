---
title: "Azure PowerShell-skript Sample - skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma eller olika prenumeration | Microsoft Docs"
description: "Azure PowerShell-skript Sample - skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma eller olika prenumeration"
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 6d37fb1308ce0b866b42f961ada84d0869f25615
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma eller olika prenumerationen med PowerShell

Det här skriptet skapar en hanterad disk från en VHD-fil i ett lagringskonto i samma eller en annan prenumeration. Använd det här skriptet för att importera ett speciellt (inte generaliserad/Sysprep) VHD till hanterade OS-disken för att skapa en virtuell dator. Använda den också för att importera en VHD-data till hanterade datadisk. 

Skapa inte flera identiska hanterade diskar från en VHD-fil i tid. Om du vill skapa hanterade diskar från en vhd-fil, blob ögonblicksbild av vhd-filen skapas och den används för att skapa hanterade diskar. Endast en blob ögonblicksbild kan skapas per minut som orsakar fel när disken skapas på grund av begränsning. För att undvika denna begränsning, skapa en [hanterade ögonblicksbild från vhd-filen](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) och sedan Använd hanterade ögonblicksbilden att skapa flera hanterade diskar kort tid. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här kursen att du är Azure PowerShell Modulversion 4.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera, se [installera Azure PowerShell](/powershell/azure/install-azurerm-ps). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en virtuell Hårddisk i annan prenumeration. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Skapar diskkonfigurationen som används för att skapa en disk. Den omfattar lagringstyp, plats, resurs-Id för det lagringskonto där överordnat virtuella Hårddisken lagras VHD-URI för överordnat virtuella Hårddisken. |
| [Ny AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Skapar en disk med diskkonfigurationen disknamnet och resursgruppens namn som parametrar skickades. |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator genom att koppla en hanterade diskar som OS-disk](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).