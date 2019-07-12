---
title: PowerShell-exempel för Azure-dator | Microsoft Docs
description: Virtuell dator i Azure PowerShell-exempel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 2d954bc068693a34ef1d69e4296e972979d4f61b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671017"
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
| [Övervaka en virtuell dator med Azure Monitor-loggar](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn i en Log Analytics-arbetsyta.  |
| [Kopiera en hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar en hanterad disk till samma eller en annan prenumeration som är i samma region som den överordnade disken.
| [Samla in information om alla datorer i en prenumeration med PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en CSV-fil som innehåller namn, resursgrupp för virtuell dator namn, Region, virtuellt nätverk, undernät, privat IP-adress, OS-typ och offentliga IP-adressen för de virtuella datorerna i den angivna prenumerationen.
| | |
