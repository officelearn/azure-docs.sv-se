---
title: Skriptexempel för Azure CLI – Montera operativsystemsdisk | Microsoft Docs
description: Skriptexempel för Azure CLI – Montera operativsystemsdisk
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c550e1366397fc08b2dc5f5132375a1ad874d103
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096072"
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
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar en virtuell dator.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) | Bifogar en disk till en virtuell dator. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | Returnerar IP-adresser för en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
