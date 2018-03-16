---
title: Virtuell dator i Azure PowerShell-exempel | Microsoft Docs
description: Virtuell dator i Azure PowerShell-exempel
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
ms.date: 03/01/2017
ms.author: iainfou
ms.openlocfilehash: 3f0466f5ecfabcf74e677e0c0f9fe2c33ca0519c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell-exempel

Följande tabell innehåller länkar till exempel på PowerShell-skript som skapar och hanterar virtuella Linux-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en helt konfigurerade virtuell dator](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa en virtuell dator med Docker aktiverad](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator, konfigurerar den här virtuella datorn som en Docker-värd och kör en NGINX-behållare. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerad](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure anpassat skript för att installera WordPress. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Operations Management Suite](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator, installerar du Operations Management Suite-agenten och registrerar den virtuella datorn i en OMS-arbetsyta.  |
| | |
