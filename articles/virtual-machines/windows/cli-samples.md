---
title: Windows Azure CLI-exempel | Microsoft Docs
description: Windows Azure CLI-exempel
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
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3c15febed58e5e833bc338b6f27da779409582d9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247145"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-exempel för Windows-datorer

I följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI och som distribuerar Windows-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell Windows-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure Desired State Configuration (DSC) för att installera IIS. |
|**Hantera lagring**||
| [Skapa hanterad disk från en virtuell Hårddisk](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hårddisk (VHD) som en OS-disk eller från en data-VHD som datadisk.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera hanterade diskar till samma eller en annan prenumeration](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopior hanterad disk till samma eller en annan prenumeration men i samma region som överordnat hanterad disk. 
| [Exportera en ögonblicksbild som VHD till ett lagringskonto](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar en hanterad ögonblicksbild som VHD till ett lagringskonto i annan region. |
| [Exportera den virtuella Hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar underliggande VHD från en hanterad disk till ett lagringskonto i annan region. |
| [Kopiera ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar ögonblicksbilden till samma eller en annan prenumeration men i samma region som den överordnade ögonblicksbilden. |
|**Virtuella datorer**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar två virtuella datorer och alla relaterade resurser som en intern och extern nätverkssäkerhetsgrupper (NSG). |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator- och datadiskar](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en Azure Key Vault, en krypteringsnyckel och ett huvudnamn för tjänsten och sedan krypterar en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Log Analytics](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn i en Log Analytics-arbetsyta.  |
| | |
