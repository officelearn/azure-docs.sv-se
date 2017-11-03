---
title: "Azure CLI-skript Sample - skapa en virtuell dator från en ögonblicksbild | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en virtuell dator från en ögonblicksbild"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: be282f79445c505ece7c6115df7a29c20a6a5f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Skapa en virtuell dator från en ögonblicksbild med CLI

Det här skriptet skapar en virtuell dator från en ögonblicksbild av en OS-disk.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en hanterad disk, den virtuella datorn och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ ögonblicksbild visa](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Hämtar ögonblicksbild med namnet på ögonblicksbilder och resursgruppens namn. ID-egenskapen för det returnerade objektet används för att skapa en hanterad disk.  |
| [Skapa AZ disk](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Skapar hanterade diskar från en ögonblicksbild med hjälp av ögonblicksbilder Id, namn på disk, lagringstyp och storlek  |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar en virtuell dator med hjälp av en hanterad OS-disk |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
