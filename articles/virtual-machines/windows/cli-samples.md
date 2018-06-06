---
title: Azure CLI exempel för Windows | Microsoft Docs
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
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c9837ae7b218fd4fdf6d0b97c0218fdfc9de3c53
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726201"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-exempel för Windows-datorer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI och som distribuerar Windows-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell Windows-dator med minimal konfiguration. |
| [Skapa en helt konfigurerade virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglighet och konfiguration för Utjämning av nätverksbelastning. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator och använder Azure önskad tillstånd Configuration DSC ()-tillägg för att installera IIS. |
|**Virtuella datorer i nätverk**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en interna och externa nätverkssäkerhetsgrupper (NSG). |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator- och datadiskar](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en Azure Key Vault, krypteringsnyckeln och tjänstens huvudnamn och krypterar en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Operations Management Suite](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator, installerar du Operations Management Suite-agenten och registrerar den virtuella datorn i en OMS-arbetsyta.  |
| | |
