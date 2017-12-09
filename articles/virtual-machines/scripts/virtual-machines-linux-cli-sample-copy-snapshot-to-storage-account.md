---
title: "Skriptexempel Azure CLI - Export/kopiera ögonblicksbild som VHD till ett lagringskonto i annan region | Microsoft Docs"
description: "Skriptexempel Azure CLI - Export/kopiera ögonblicksbild som VHD till ett lagringskonto i samma eller olika prenumeration"
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
ms.openlocfilehash: cd0128256e016a329b0940eec4be41426cdaf51a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>Exportera eller kopieras hanterad ögonblicksbilder som VHD ett lagringskonto i en annan region med CLI

Det här skriptet exporterar en hanterad ögonblicksbild till ett lagringskonto i en annan region. Den första genererar ögonblicksbilden SAS-URI och sedan används för att kopiera den till ett lagringskonto i en annan region. Använd det här skriptet för att underhålla säkerhetskopiering hanterade diskar i annan region för katastrofåterställning. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att generera SAS URI för en hanterad ögonblicksbild och kopierar ögonblicksbilden till ett lagringskonto med hjälp av SAS-URI. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ ögonblicksbild bevilja åtkomst](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Genererar skrivskyddad SA som används för att kopiera underliggande VHD-filen till ett lagringskonto eller ladda ned den lokalt  |
| [Starta AZ storage blob-kopia](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Kopierar en blobb asynkront från ett lagringskonto till en annan |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en virtuell Hårddisk](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella datorer och hanterade diskar CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
