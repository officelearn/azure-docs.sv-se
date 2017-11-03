---
title: "Azure CLI-skript Sample - skapa en hanterad disk från en ögonblicksbild | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en hanterad disk från en ögonblicksbild"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ffdad7faa34fec09623a415664b5a260868e9dbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Skapa en hanterad disk från en ögonblicksbild med CLI

Det här skriptet skapar en hanterad disk från en ögonblicksbild. Du kan använda den för att återställa en virtuell dator från ögonblicksbilder av Operativsystemet och datadiskarna. Skapa OS och data från respektive ögonblicksbilder-hanterade diskar och sedan skapa en ny virtuell dator genom att koppla hanterade diskar. Du kan även återställa datadiskar på en befintlig virtuell dator genom att koppla datadiskar som skapas med hjälp av ögonblicksbilder.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en ögonblicksbild. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ ögonblicksbild visa](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Hämtar alla egenskaper för en ögonblicksbild med hjälp av namn och egenskaper för resursgrupp av ögonblicksbilden. ID-egenskapen används för att skapa hanterade diskar.  |
| [Skapa AZ disk](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Skapar en hanterad disk med en ögonblicksbild Id för en hanterad ögonblicksbild |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator genom att koppla en hanterade diskar som OS-disk](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella datorer och hanterade diskar CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
