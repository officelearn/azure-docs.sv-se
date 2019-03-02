---
title: PowerShell-exempel för Azure-dator | Microsoft Docs
description: Virtuell dator i Azure PowerShell-exempel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: e094a95fef5e010471dde3e60f2fc9aaaaad6344
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246386"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure VM-PowerShell-exempel

I följande tabell innehåller länkar till PowerShell-exempelskript som skapar och hanterar virtuella Linux-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa en virtuell dator med Docker aktiverat](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, konfigurerar den här virtuella datorn som en Docker-värd och kör en NGINX-behållare. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerad](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera WordPress. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad Disk som OS-disk. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från ögonblicksbilden och sedan den nya hanterade disken som OS-disk. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en VHD i samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell Hårddisk som en OS-disk eller från en data-VHD som en datadisk i samma eller en annan prenumeration.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Exportera en ögonblicksbild som en virtuell Hårddisk till ett lagringskonto](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar en hanterad ögonblicksbild som en virtuell Hårddisk till ett lagringskonto i en annan region. |
| [Exportera den virtuella Hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar underliggande VHD från en hanterad disk till ett lagringskonto i en annan region. |
| [Skapa en ögonblicksbild från en virtuell Hårddisk](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en ögonblicksbild av en virtuell Hårddisk och använder sedan den ögonblicksbilden för att snabbt skapa flera identiska hanterade diskar.  |
| [Kopiera en ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar ögonblicksbilden till samma eller en annan prenumeration som är i samma region som den överordnade ögonblicksbilden. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Azure Log Analytics](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn i en Log Analytics-arbetsyta.  |
| [Kopiera en hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar en hanterad disk till samma eller en annan prenumeration som är i samma region som den överordnade disken.
| | |
