---
title: PowerShell-exempel för Azure-dator | Microsoft Docs
description: Virtuell dator i Azure PowerShell-exempel
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 564e8e2968f31b9e2aef8f7eadbf848885037ccb
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040770"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure VM-PowerShell-exempel

I följande tabell innehåller länkar till PowerShell-exempelskript som skapar och hantera Windows-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa snabbt en virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser med minsta möjliga anvisningarna.|
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration.|
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure Desired State Configuration (DSC) för att installera IIS. |
| [Ladda upp en virtuell Hårddisk och skapa virtuella datorer](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Laddar upp en lokal VHD-fil till Azure, skapar och avbildningen från den virtuella Hårddisken och skapar en virtuell dator från avbildningen. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad Disk som OS-disk. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från en ögonblicksbild och sedan den nya hanterade disken som OS-disk. |
|**Hantera lagring**||
| [Skapa hanterad disk från en virtuell Hårddisk i samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell Hårddisk som en OS-disk eller från en data-VHD som datadisk i samma eller en annan prenumeration.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera hanterade diskar till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopior hanterad disk till samma eller en annan prenumeration men i samma region som överordnat hanterad disk. 
| [Exportera en ögonblicksbild som VHD till ett lagringskonto](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporterar en hanterad ögonblicksbild som VHD till ett lagringskonto i annan region. |
| [Exportera den virtuella Hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporterar underliggande VHD från en hanterad disk till ett lagringskonto i annan region. |
| [Skapa en ögonblicksbild från en virtuell Hårddisk](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar ögonblicksbild från en VHD för att skapa flera identiska hanterade diskar från en ögonblicksbild på kort tid.  |
| [Kopiera ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Kopierar ögonblicksbilden till samma eller en annan prenumeration men i samma region som den överordnade ögonblicksbilden. |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator- och datadiskar](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Skapar en Azure Key Vault, en krypteringsnyckel och ett huvudnamn för tjänsten och sedan krypterar en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator, Operations Management Suite-agenten installeras och registreras den virtuella datorn i en OMS-arbetsyta.  |
| | |

