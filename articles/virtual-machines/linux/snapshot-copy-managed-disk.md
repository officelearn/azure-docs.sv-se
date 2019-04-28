---
title: Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs
description: Lär dig hur du skapar en kopia av en virtuell Hårddisk i Azure som en tillbaka upp eller för att felsöka problem.
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
ms.openlocfilehash: 9f2f3ac3668f0e48716fc30fb69cd1782dbd4e56
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765677"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild 

Ta en ögonblicksbild av en OS- eller datadisken för säkerhetskopiering eller för att felsöka problem med Virtuella. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. 

## <a name="use-azure-cli"></a>Använda Azure CLI 

I följande exempel kräver att du använder [Cloud Shell](https://shell.azure.com/bash) eller ha Azure CLI installerat.

Följande steg visar hur du tar en ögonblicksbild med hjälp av den **az snapshot skapa** med den **--källdisken** parametern. I följande exempel förutsätter att det finns en virtuell dator som kallas *myVM* i den *myResourceGroup* resursgrupp.

Hämta disk-ID med [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Ta en ögonblicksbild med namnet *osDisk backup* med [az snapshot skapa](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Om du vill lagra dina ögonblicksbild i zonen flexibel lagring måste du skapa den i en region som har stöd för [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera den **--sku Standard_ZRS** parametern.

Du kan se en lista över ögonblicksbilder med hjälp av [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Använda Azure-portalen 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Börja längst upp till vänster och klicka på **skapa en resurs** och Sök efter **ögonblicksbild**. Välj **ögonblicksbild** från sökresultaten.
3. I den **ögonblicksbild** bladet klickar du på **skapa**.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig resursgrupp eller Skriv namnet på en ny resursgrupp. 
7. För **källdisken**, Välj den hantera disken till ögonblicksbild.
8. Välj den **kontotyp** du använder för att lagra ögonblicksbilden. Använd **Standard HDD** såvida du inte behöver det i en hög prestanda SSD.
9. Klicka på **Skapa**.


## <a name="next-steps"></a>Nästa steg

 Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från ögonblicksbilden och sedan koppla ny hanterad disk som OS-disk. Mer information finns i den [skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skript.

