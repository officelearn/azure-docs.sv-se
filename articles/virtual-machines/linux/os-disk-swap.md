---
title: Växla mellan OS-diskar med CLI '
description: Ändra operativsystemets disk som används av en virtuell Azure-dator med CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: a8c879ae56eb22d9817841667d33e37109a4f63d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759398"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Ändra os-disken som används av en virtuell Azure-dator med CLI


Om du har en befintlig virtuell dator, men vill byta disk mot en reservdisk eller en annan OS-disk, kan du använda Azure CLI för att byta os-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn. Du kan till och med använda en hanterad disk i en annan resursgrupp, så länge den inte redan används.

Den virtuella datorn måste stoppas\deallocated, då resurs-ID för den hanterade disken kan ersättas med resurs-ID för en annan hanterad disk. 

Kontrollera att den virtuella datorns storlek och lagringstyp är kompatibla med den disk som du vill koppla. Om disken du till exempel vill använda finns i Premium Storage måste den virtuella datorn kunna hantera Premium Storage (som en storlek i DS-serien).

Den här artikeln kräver Azure CLI version 2.0.25 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 


Använd [az-disklistan](/cli/azure/disk) för att få en lista över diskarna i resursgruppen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Använd [az vm stop](/cli/azure/vm) för att stoppa\deallocate den virtuella datorn innan du byter diskarna.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Använd [az vm-uppdatering](/cli/azure/vm#az-vm-update) med det fullständiga resurs-ID:et för den nya disken för parametern `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Starta om den virtuella datorn med [az vm-start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Nästa steg**

Information om hur du skapar en kopia av en disk finns i [Ögonblicksbild en disk](snapshot-copy-managed-disk.md).
