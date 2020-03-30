---
title: Skapa en ögonblicksbild av en virtuell hårddisk i Azure
description: Lär dig hur du skapar en kopia av en virtuell hårddisk i Azure som en säkerhetskopia eller för felsökningsproblem.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 15696469ca3861586617e9f418f8a55a7ea90467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034784"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild 

Ta en ögonblicksbild av ett operativsystem eller en datadisk för säkerhetskopiering eller för att felsöka problem med virtuella datorer. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk. 

## <a name="use-azure-cli"></a>Använda Azure CLI 

Följande exempel kräver att du använder [Cloud Shell](https://shell.azure.com/bash) eller har Azure CLI installerat.

Följande steg visar hur du tar en ögonblicksbild med kommandot **az snapshot create** med parametern **--source-disk.** I följande exempel förutsätts att det finns en virtuell dator som heter *myVM* i resursgruppen *myResourceGroup.*

Hämta disk-ID med [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Ta en ögonblicksbild med namnet *osDisk-backup* med [az snapshot create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Om du vill lagra ögonblicksbilden i zontålig lagring måste du skapa den i en region som stöder [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera parametern **--sku Standard_ZRS.**

Du kan se en lista över ögonblicksbilder med [az snapshot-lista](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Använda Azure-portalen 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Börja längst upp till vänster, klicka på **Skapa en resurs** och sök efter **ögonblicksbild**. Välj **Ögonblicksbild** i sökresultaten.
3. Klicka på **Skapa**i bladet **Ögonblicksbild** .
4. Ange ett **namn** för ögonblicksbilden.
5. Välj en befintlig resursgrupp eller skriv namnet på en ny. 
7. För **källdisk**väljer du den hanterade disken som ska ögonblicksbild.
8. Välj den **kontotyp** som ska användas för att lagra ögonblicksbilden. Använd **standard hdd** om du inte behöver den lagrad på en högpresterande SSD.
9. Klicka på **Skapa**.


## <a name="next-steps"></a>Nästa steg

 Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från ögonblicksbilden och sedan koppla den nya hanterade disken som OS-disken. Mer information finns i [Skapa en virtuell dator från ett ögonblicksbildskript.](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json)

