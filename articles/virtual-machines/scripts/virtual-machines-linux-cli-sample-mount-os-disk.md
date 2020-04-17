---
title: Skriptexempel för Azure CLI – Montera operativsystemsdisk
description: Skriptexempel för Azure CLI – Montera operativsystemsdisk
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
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
ms.openlocfilehash: f15beeee2c5843506f0bf89b4c6d66d77826e152
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459898"
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
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Hämtar en lista över virtuella datorer. I det här fallet används frågealternativet att returnera operativsystemsdisken på den virtuella disken. Det här värdet läggs sedan till i variabelnamnet ”uri”. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Tar bort en virtuell dator. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Skapar en virtuell dator.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Bifogar en disk till en virtuell dator. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Returnerar IP-adresser för en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
