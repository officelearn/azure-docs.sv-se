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
ms.date: 03/01/2017
ms.author: cynthn
ms.openlocfilehash: 10c72f84a13eb1edb653d23722cb03ee97298c6b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407630"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure VM-PowerShell-exempel

I följande tabell innehåller länkar till PowerShell-exempelskript som skapar och hanterar virtuella Linux-datorer.

| | |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en resursgrupp, virtuell dator och alla relaterade resurser.|
| [Skapa en virtuell dator med Docker aktiverat](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator, konfigurerar den här virtuella datorn som en Docker-värd och kör en NGINX-behållare. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera NGINX. |
| [Skapa en virtuell dator med WordPress installerad](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator och använder tillägget för anpassat skript för Azure för att installera WordPress. |
|**Övervaka virtuella datorer**||
| [Övervaka en virtuell dator med Azure Log Analytics](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics-agenten och registrerar den virtuella datorn i en Log Analytics-arbetsyta.  |
| | |
