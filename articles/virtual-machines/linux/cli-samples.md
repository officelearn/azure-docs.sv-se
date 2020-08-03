---
title: Azure CLI-exempel
description: Azure CLI-exempel
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: ecc195017b99da576cba9944613c76bea3c31abf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498726"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-exempel för virtuella Linux-datorer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| Skript | Description |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell Linux-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en resurs grupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglig och belastningsutjämnad konfiguration. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azures anpassade skript tillägg för att installera NGINX. |
| [Skapa en VM med WordPress-installation](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azures anpassade skript tillägg för att installera WordPress. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad disk som operativ system disk. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator från en ögonblicks bild genom att först skapa en hanterad disk från ögonblicks bild och sedan koppla den nya hanterade disken som operativ system disk. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hårddisk](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hård disk som en OS-disk eller från en data-VHD som data disk.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en ögonblicks bild. |
| [Kopiera hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar hanterad disk till samma eller en annan prenumeration, men i samma region som den överordnade hanterade disken. 
| [Exportera en ögonblicksbild som en virtuell hårddisk till ett lagringskonto](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar en hanterad ögonblicks bild som VHD till ett lagrings konto i en annan region. |
| [Exportera den virtuella hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar den underliggande virtuella hård disken för en hanterad disk till ett lagrings konto i en annan region. |
| [Kopiera ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar ögonblicks bild till samma eller en annan prenumeration, men i samma region som den överordnade ögonblicks bilden. |
|**Virtuella datorer i nätverket**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en intern och extern nätverks säkerhets grupp (NSG). |
|**Säkra virtuella datorer**||
| [Kryptera en virtuell dator och datadiskar](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en Azure Key Vault, en krypterings nyckel och ett huvud namn för tjänsten och krypterar sedan en virtuell dator. |
|**Övervakning av virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor-loggar](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics agent och registrerar den virtuella datorn i en Log Analytics arbets yta.  |
|**Felsöka virtuella datorer**||
| [Felsöka en operativsystemdisk för virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monterar operativ system disken från en virtuell dator som en datadisk på en annan virtuell dator. |
| | |
