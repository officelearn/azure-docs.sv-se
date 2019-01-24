---
title: Skriptexempel för Azure CLI – Montera operativsystemsdisk | Microsoft Docs
description: Skriptexempel för Azure CLI – Montera operativsystemsdisk
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5459ba31d30a86efbe40239f9531298c10cf9d98
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413608"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Felsöka en operativsystemdisk för virtuella datorer

Det här skriptet tar operativsystemsdisken från en misslyckad eller problematisk virtuell dator och monterar den som en datadisk på en annan virtuell dator. Detta kan vara användbart vid felsökning av diskproblem eller återställning av data.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#az_vm_show) | Hämtar en lista över virtuella datorer. I det här fallet används frågealternativet att returnera operativsystemsdisken på den virtuella disken. Det här värdet läggs sedan till i variabelnamnet ”uri”. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#az_vm_delete) | Tar bort en virtuell dator. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Skapar en virtuell dator.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Bifogar en disk till en virtuell dator. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | Returnerar IP-adresser för en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
