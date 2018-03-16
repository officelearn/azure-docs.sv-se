---
title: Azure CLI-exempel | Microsoft Docs
description: Azure CLI-exempel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ec76e211062890b4325b5c2cb1cf186ac7b89c55
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-exempel för Linux virtuella datorer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell Linux-dator med minimal konfiguration. |
| [Skapa en helt konfigurerade virtuell dator](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglighet och konfiguration för Utjämning av nätverksbelastning. |
| [Skapa en virtuell dator med Docker aktiverad](./../scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator, konfigurerar den här virtuella datorn som en Docker-värd och kör en NGINX-behållare. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerad](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera WordPress. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanteras Disk som OS-disken. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från en ögonblicksbild och kopplar den nya hantera disken som OS-disken. |
|**Hantera lagring**||
| [Skapa hanterade diskar från en virtuell Hårddisk](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | Skapar en hanterad disk från en särskild virtuell Hårddisk som en OS-disk eller från en virtuell Hårddisk som datadisk.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera hanterade diskar till samma eller en annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopior hanteras disk till samma eller olika prenumeration men hanteras i samma region som överordnat disken. 
| [Exportera en ögonblicksbild som VHD till ett lagringskonto](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) | Exporterar en hanterad ögonblicksbild som VHD till ett lagringskonto i en annan region. |
| [Kopiera ögonblicksbilden till samma eller en annan prenumeration](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopior ögonblicksbild till samma eller olika prenumeration men i samma region som den överordnade ögonblicksbilden. |
|**Virtuella datorer i nätverk**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en interna och externa nätverkssäkerhetsgrupper (NSG). |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator- och datadiskar](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en Azure Key Vault, krypteringsnyckeln och tjänstens huvudnamn och krypterar en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Operations Management Suite](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator, installerar du Operations Management Suite-agenten och registrerar den virtuella datorn i en OMS-arbetsyta.  |
|**Felsökning av virtuella datorer**||
| [Felsöka en operativsystemdisk för virtuella datorer](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Monterar operativsystemets disk från en virtuell dator som en datadisk på en andra virtuell dator. |
| | |
