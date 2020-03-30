---
title: Azure Virtual Machine PowerShell-exempel
description: Azure Virtual Machine PowerShell-exempel
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
ms.openlocfilehash: 903860dfd1e30f7941770efd759227a1349d41ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035252"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell-exempel

Följande tabell innehåller länkar till Exempel på PowerShell-skript som skapar och hanterar virtuella Linux-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en resursgrupp, en virtuell dator och alla relaterade resurser.|
| [Skapa en virtuell dator med Docker aktiverat](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, konfigurerar den här virtuella datorn som en Docker-värd och kör en NGINX-behållare. |
| [Skapa ett vm- och körningskonfigurationsskript](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azure Custom Script-tillägget för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerat](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator och använder Azure Custom Script-tillägget för att installera WordPress. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanterad disk som OS-disk. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från ögonblicksbilden och sedan koppla den nya hanterade disken som OS-disk. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hårddisk i samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hårddisk som en OS-disk, eller från en virtuell data-hårddisk som en datadisk, i samma eller en annan prenumeration.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Exportera en ögonblicksbild som en virtuell hårddisk till ett lagringskonto](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar en hanterad ögonblicksbild som en virtuell hårddisk till ett lagringskonto i en annan region. |
| [Exportera den virtuella hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporterar den underliggande virtuella hårddisken för en hanterad disk till ett lagringskonto i en annan region. |
| [Skapa en ögonblicksbild från en virtuell hårddisk](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en ögonblicksbild från en virtuell hårddisk och använder sedan ögonblicksbilden för att snabbt skapa flera identiska hanterade diskar.  |
| [Kopiera en ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar ögonblicksbild till samma eller en annan prenumeration som finns i samma region som den överordnade ögonblicksbilden. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor-loggar](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn på en Log Analytics-arbetsyta.  |
| [Kopiera en hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopierar en hanterad disk till samma eller en annan prenumeration som finns i samma region som den överordnade hanterade disken.
| [Samla in information om alla virtuella datorer i en prenumeration med PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Skapar en csv som innehåller VM-namn, resursgruppnamn, region, virtuellt nätverk, undernät, privat IP-adress, OS-typ och offentlig IP-adress för de virtuella datorerna i den angivna prenumerationen.
| | |
