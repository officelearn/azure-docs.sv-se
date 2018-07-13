---
title: Exempelskript för Azure PowerShell – Skapa en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration | Microsoft Docs
description: Exempelskript för Azure PowerShell – Skapa en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 6dfdd88f2f1e776fca69ffa53b2e424fe9d2c8ea
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531256"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Skapa en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration med PowerShell

Det här skriptet skapar en hanterad disk från en VHD-fil på ett lagringskonto i samma eller en annan prenumeration. Använd det här skriptet för att importera en specialiserad (inte generaliserad/syspreppad) VHD till en hanterad OS-disk för att skapa en virtuell dator. Du kan också använda det för att importera en data-VHD till en hanterad datadisk. 

Skapa inte flera identiska hanterade diskar från en VHD-fil under kort tid. När du skapar hanterade diskar från en VHD-fil, skapas en blobögonblicksbild av VHD-filen som sedan används för att skapa hanterade diskar. Endast en blobögonblicksbild kan skapas per minut, vilket medför fel när disken skapas på grund av nätverksbegränsningen. För att undvika denna begränsning kan du skapa en [hanterad ögonblicksbild från VHD-filen](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) och sedan använda den hanterade ögonblicksbilden till att skapa flera hanterade diskar på kort tid. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt, måste du i den här självstudien ha Azure PowerShell med modulversion 4.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure PowerShell](/powershell/azure/install-azurerm-ps). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon till att skapa en hanterad disk från en VHD i en annan prenumeration. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Skapar den diskkonfiguration som används för att skapa en disk. Den innehåller lagringstyp, plats, resurs-ID för det lagringskonto där överordnad VHD lagras, samt VHD-URI för överordnad VHD. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Skapar en disk med diskkonfiguration, disknamn och resursgruppens namn som parametrar. |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator genom att koppla en hanterad disk som OS-disk](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
