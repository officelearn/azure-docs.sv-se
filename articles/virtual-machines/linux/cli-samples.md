---
title: Azure CLI-exempel
description: Azure CLI-exempel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: eda3154bb921a46bbe3b768713d4e72f1cc45d5f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036856"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-exempel för virtuella Linux-datorer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell Linux-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en resurs grupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration. |
| [Skapa en virtuell dator och kör konfigurations skript](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azures anpassade skript tillägg för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerat](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azures anpassade skript tillägg för att installera WordPress. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad disk som operativ system disk. |
| [Skapa en virtuell dator från en ögonblicks bild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator från en ögonblicks bild genom att först skapa en hanterad disk från ögonblicks bild och sedan koppla den nya hanterade disken som operativ system disk. |
|**Hantera lagring**||
| [Skapa hanterad disk från en virtuell hård disk](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hård disk som en OS-disk eller från en data-VHD som data disk.  |
| [Skapa en hanterad disk från en ögonblicks bild](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en ögonblicks bild. |
| [Kopiera hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar hanterad disk till samma eller en annan prenumeration, men i samma region som den överordnade hanterade disken. 
| [Exportera en ögonblicks bild som VHD till ett lagrings konto](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar en hanterad ögonblicks bild som VHD till ett lagrings konto i en annan region. |
| [Exportera den virtuella hård disken för en hanterad disk till ett lagrings konto](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar den underliggande virtuella hård disken för en hanterad disk till ett lagrings konto i en annan region. |
| [Kopiera ögonblicks bild till samma eller en annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar ögonblicks bild till samma eller en annan prenumeration, men i samma region som den överordnade ögonblicks bilden. |
|**Virtuella nätverks datorer**||
| [Skydda nätverks trafik mellan virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en intern och extern nätverks säkerhets grupp (NSG). |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator och data diskar](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en Azure Key Vault, en krypterings nyckel och ett huvud namn för tjänsten och krypterar sedan en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor loggar](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics agent och registrerar den virtuella datorn i en Log Analytics arbets yta.  |
|**Felsöka virtuella datorer**||
| [Felsöka en operativ system disk för virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monterar operativ system disken från en virtuell dator som en datadisk på en annan virtuell dator. |
| | |
