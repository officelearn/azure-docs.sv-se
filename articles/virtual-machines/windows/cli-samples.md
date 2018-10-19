---
title: Windows Azure CLI-exempel | Microsoft Docs
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
ms.openlocfilehash: 6861399b63b7f06bac7599704a6dd1aa87800ebf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403346"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-exempel för Windows-datorer

I följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI och som distribuerar Windows-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell Windows-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure Desired State Configuration (DSC) för att installera IIS. |
|**Virtuella datorer**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar två virtuella datorer och alla relaterade resurser som en intern och extern nätverkssäkerhetsgrupper (NSG). |
|**Skydda virtuella datorer**||
| [Kryptera en virtuell dator- och datadiskar](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en Azure Key Vault, en krypteringsnyckel och ett huvudnamn för tjänsten och sedan krypterar en virtuell dator. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Log Analytics](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn i en Log Analytics-arbetsyta.  |
| | |
