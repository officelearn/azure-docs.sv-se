---
title: Azure Virtual Machine PowerShell-exempel
description: Azure Virtual Machine PowerShell-exempel
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 434cf3f9342ad10bab39030e65cdd4bc4d0edc27
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458870"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell-exempel

I följande tabell finns länkar till PowerShell-skriptexempel som skapar och hanterar virtuella datorer (Windows.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa snabbt en virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resursgrupp, en virtuell dator och alla relaterade resurser, med ett minimum av uppmaningar.|
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resursgrupp, en virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgång](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar flera virtuella datorer i en konfiguration med hög tillgänglig och belastningsbalanserad.|
| [Skapa en virtuell dator och köra ett konfigurationsskript](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder Azure Custom Script-tillägget för att installera IIS. |
| [Skapa en virtuell dator och köra en DSC-konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder DSC-tillägget (Azure Desired State Configuration) för att installera IIS. |
| [Ladda upp en virtuell hårddisk och skapa virtuella datorer](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Överför en lokal VHD-fil till Azure, skapar en avbildning från den virtuella hårddisken och skapar sedan en virtuell dator från den avbildningen. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad disk som OS-disk. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från ögonblicksbilden och sedan koppla den nya hanterade disken som OS-disk. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hårddisk i samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hårddisk som en OS-disk, eller från en virtuell data-hårddisk som en datadisk, i samma eller en annan prenumeration.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera en hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopierar en hanterad disk till samma eller en annan prenumeration som finns i samma region som den överordnade hanterade disken.
| [Exportera en ögonblicksbild som en virtuell hårddisk till ett lagringskonto](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar en hanterad ögonblicksbild som en virtuell hårddisk till ett lagringskonto i en annan region. |
| [Exportera den virtuella hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar den underliggande virtuella hårddisken för en hanterad disk till ett lagringskonto i en annan region. |
| [Skapa en ögonblicksbild från en virtuell hårddisk](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en ögonblicksbild från en virtuell hårddisk och använder sedan ögonblicksbilden för att snabbt skapa flera identiska hanterade diskar.  |
| [Kopiera en ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar ögonblicksbild till samma eller en annan prenumeration som finns i samma region som den överordnade ögonblicksbilden. |
|**Säkra virtuella datorer**||
| [Kryptera en virtuell dator och dess datadiskar](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Skapar ett Azure-nyckelvalv, en krypteringsnyckel och ett tjänsthuvudnamn och krypterar sedan en virtuell dator. |
|**Övervakning av virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator, installerar Azure Log Analytics-agenten och registrerar den virtuella datorn på en Log Analytics-arbetsyta.  |
| [Samla in information om alla virtuella datorer i en prenumeration med PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en csv som innehåller VM-namn, resursgruppnamn, region, virtuellt nätverk, undernät, privat IP-adress, OS-typ och offentlig IP-adress för de virtuella datorerna i den angivna prenumerationen.
| | |
