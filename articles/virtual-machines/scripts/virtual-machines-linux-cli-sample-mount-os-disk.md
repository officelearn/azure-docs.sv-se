---
title: Skriptexempel för Azure CLI – Montera operativsystemsdisk
description: Skriptexempel för Azure CLI – Montera operativsystemsdisk
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74037599"
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
