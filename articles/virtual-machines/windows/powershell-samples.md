---
title: PowerShell-exempel för Azure Virtual Machine
description: PowerShell-exempel för Azure Virtual Machine
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f068b79f1b1eaa9a11df70052619c8e3993101cb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033003"
---
# <a name="azure-virtual-machine-powershell-samples"></a>PowerShell-exempel för Azure Virtual Machine

Följande tabell innehåller länkar till exempel på PowerShell-skript som skapar och hanterar virtuella Windows-datorer (VM: ar).

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa snabbt en virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resurs grupp, en virtuell dator och alla relaterade resurser, med ett minimum av prompter.|
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resurs grupp, en virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglig och belastningsutjämnad konfiguration.|
| [Skapa en virtuell dator och köra ett konfigurations skript](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder Azures anpassade skript tillägg för att installera IIS. |
| [Skapa en virtuell dator och köra en DSC-konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure Desired State Configuration (DSC) för att installera IIS. |
| [Ladda upp en virtuell hård disk och skapa virtuella datorer](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Laddar upp en lokal VHD-fil till Azure, skapar en avbildning från den virtuella hård disken och skapar sedan en virtuell dator från avbildningen. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad disk som operativ system disk. |
| [Skapa en virtuell dator från en ögonblicks bild](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator från en ögonblicks bild genom att först skapa en hanterad disk från ögonblicks bilden och sedan koppla den nya hanterade disken som operativ system disk. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hård disk i samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hård disk som en OS-disk eller från en data-VHD som en datadisk, i samma eller en annan prenumeration.  |
| [Skapa en hanterad disk från en ögonblicks bild](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en ögonblicks bild. |
| [Kopiera en hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopierar en hanterad disk till samma eller en annan prenumeration som finns i samma region som den överordnade hanterade disken.
| [Exportera en ögonblicks bild som en virtuell hård disk till ett lagrings konto](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar en hanterad ögonblicks bild som en virtuell hård disk till ett lagrings konto i en annan region. |
| [Exportera den virtuella hård disken för en hanterad disk till ett lagrings konto](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar den underliggande virtuella hård disken för en hanterad disk till ett lagrings konto i en annan region. |
| [Skapa en ögonblicks bild från en virtuell hård disk](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en ögonblicks bild från en virtuell hård disk och använder sedan ögonblicks bilden för att skapa flera identiska hanterade diskar snabbt.  |
| [Kopiera en ögonblicks bild till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar ögonblicks bild till samma eller en annan prenumeration i samma region som den överordnade ögonblicks bilden. |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator och dess data diskar](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Skapar ett Azure Key Vault, en krypterings nyckel och ett huvud namn för tjänsten och krypterar sedan en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator, installerar Azure Log Analytics-agenten och registrerar den virtuella datorn i en Log Analytics arbets yta.  |
| [Samla in information om alla virtuella datorer i en prenumeration med PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en CSV som innehåller namnet på den virtuella datorn, resurs gruppens namn, region, Virtual Network, undernät, privat IP-adress, OS-typ och den offentliga IP-adressen för de virtuella datorerna i den angivna prenumerationen.
| | |
