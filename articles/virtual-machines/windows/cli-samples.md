---
title: Azure CLI-exempel i Windows
description: Azure CLI-exempel i Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4868254ffc0448562ff6115a4e6bb030e4353ee4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083317"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-exempel för virtuella Windows-datorer

Följande tabell innehåller länkar till bash-skript som skapats med azure CLI som distribuerar virtuella Windows-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell Windows-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resursgrupp, en virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgång](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar flera virtuella datorer i en högtillgänglig och belastningsbalanserad konfiguration. |
| [Skapa ett vm- och körningskonfigurationsskript](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder Azure Custom Script-tillägget för att installera IIS. |
| [Skapa en virtuell dator och köra DSC-konfiguration](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder DSC-tillägget (Azure Desired State Configuration) för att installera IIS. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hårddisk](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hårddisk som en OS-disk eller från en data-VIRTUELLD som datadisk.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera hanterad disk till samma eller annan prenumeration](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar hanterad disk till samma eller olika prenumerationer men i samma region som den överordnade hanterade disken. 
| [Exportera en ögonblicksbild som en virtuell hårddisk till ett lagringskonto](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar en hanterad ögonblicksbild som virtuell hårddisk till ett lagringskonto i en annan region. |
| [Exportera den virtuella hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar den underliggande virtuella hårddisken för en hanterad disk till ett lagringskonto i en annan region. |
| [Kopiera ögonblicksbild till samma eller andra prenumerationer](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar ögonblicksbild till samma eller olika prenumerationer men i samma region som den överordnade ögonblicksbilden. |
|**Virtuella datorer i nätverket**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en intern och extern nätverkssäkerhetsgrupper (NSG). |
|**Säkra virtuella datorer**||
| [Kryptera en virtuell dator och datadiskar](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar ett Azure Key Vault, krypteringsnyckel och tjänsthuvudnamn och krypterar sedan en virtuell dator. |
|**Övervakning av virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn på en Log Analytics-arbetsyta.  |
| | |
