---
title: Azure CLI-exempel
description: Azure CLI-exempel
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 76803f8c3a703071eb733c5cfde65482ffd07f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970085"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-exempel för virtuella Linux-datorer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell Linux-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en resursgrupp, en virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgång](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar flera virtuella datorer i en högtillgänglig och belastningsbalanserad konfiguration. |
| [Skapa ett vm- och körningskonfigurationsskript](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azure Custom Script-tillägget för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerat](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azure Custom Script-tillägget för att installera WordPress. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad disk som OS-disk. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från ögonblicksbild och sedan koppla den nya hanterade disken som OS-disk. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hårddisk](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hårddisk som en OS-disk eller från en data-VIRTUELLD som datadisk.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera hanterad disk till samma eller annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar hanterad disk till samma eller olika prenumerationer men i samma region som den överordnade hanterade disken. 
| [Exportera en ögonblicksbild som en virtuell hårddisk till ett lagringskonto](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar en hanterad ögonblicksbild som virtuell hårddisk till ett lagringskonto i en annan region. |
| [Exportera den virtuella hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar den underliggande virtuella hårddisken för en hanterad disk till ett lagringskonto i en annan region. |
| [Kopiera ögonblicksbild till samma eller andra prenumerationer](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar ögonblicksbild till samma eller olika prenumerationer men i samma region som den överordnade ögonblicksbilden. |
|**Virtuella datorer i nätverket**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en intern och extern nätverkssäkerhetsgrupper (NSG). |
|**Säkra virtuella datorer**||
| [Kryptera en virtuell dator och datadiskar](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar ett Azure Key Vault, krypteringsnyckel och tjänsthuvudnamn och krypterar sedan en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor-loggar](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn på en Log Analytics-arbetsyta.  |
|**Felsöka virtuella datorer**||
| [Felsöka en operativsystemdisk för virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monterar operativsystemdisken från en virtuell dator som en datadisk på en andra virtuell dator. |
| | |
