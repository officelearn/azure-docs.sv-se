---
title: Virtuell dator i Azure PowerShell-exempel | Microsoft Docs
description: Virtuell dator i Azure PowerShell-exempel
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/20/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9d0db50e5dcf6af97e15019c11fdeafb209de639
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell-exempel

Följande tabell innehåller länkar till exempel på PowerShell-skript som skapar och hanterar virtuella Windows-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en helt konfigurerade virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglighet och konfiguration för Utjämning av nätverksbelastning.|
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder Azure önskad tillstånd Configuration DSC ()-tillägg för att installera IIS. |
| [Överför en virtuell Hårddisk och skapa virtuella datorer](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Överför en lokal VHD-fil till Azure, skapar och avbildningen från den virtuella Hårddisken och skapar sedan en virtuell dator från den avbildningen. |
| [Skapa en virtuell dator från en hanterad OS-disk](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator genom att koppla en befintlig hanteras Disk som OS-disken. |
| [Skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator från en ögonblicksbild genom att först skapa en hanterad disk från en ögonblicksbild och kopplar den nya hantera disken som OS-disken. |
|**Skapa virtuella datorer med ny AzVM**||
| [Skapa en helt konfigurerade virtuell dator](./../scripts/virtual-machines-windows-powershell-sample-create-vm-auto.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis-auto.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc-auto.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder Azure önskad tillstånd Configuration DSC ()-tillägg för att installera IIS. |
|**Hantera lagring**||
| [Skapa hanterade diskar från en virtuell Hårddisk i samma eller olika prenumeration](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en hanterad disk från en särskild virtuell Hårddisk som en OS-disk eller från en virtuell Hårddisk som datadisk i samma eller en annan prenumeration.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en hanterad disk från en ögonblicksbild. |
| [Kopiera hanterade diskar till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopior hanteras disk till samma eller olika prenumeration men hanteras i samma region som överordnat disken. 
| [Exportera en ögonblicksbild som VHD till ett lagringskonto](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporterar en hanterad ögonblicksbild som VHD till ett lagringskonto i en annan region. |
| [Skapa en ögonblicksbild från en virtuell Hårddisk](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar ögonblicksbild från en virtuell Hårddisk för att skapa flera identiska hanterade diskar från en ögonblicksbild på kort tid.  |
| [Kopiera ögonblicksbilden till samma eller en annan prenumeration](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Kopior ögonblicksbild till samma eller olika prenumeration men i samma region som den överordnade ögonblicksbilden. |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator- och datadiskar](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Skapar en Azure Key Vault, krypteringsnyckeln och tjänstens huvudnamn och krypterar en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator, installerar du Operations Management Suite-agenten och registrerar den virtuella datorn i en OMS-arbetsyta.  |
| | |

