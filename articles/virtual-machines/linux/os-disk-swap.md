---
title: Växlingen OS-disk för en virtuell Azure-dator med hjälp av CLI | Microsoft Docs
description: Ändra operativsystemets disk som används av en virtuell Azure-dator med hjälp av CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 1732b60ee135b765cdeea43f981bcef9575ff32c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Ändra OS-disk som används av en virtuell Azure-dator med hjälp av CLI


Om du har en befintlig virtuell dator, men du vill byta disken för en säkerhetskopiering disk eller en annan OS-disk, kan du använda Azure CLI för att växla OS-diskar. Du behöver ta bort och återskapa den virtuella datorn. Du kan även använda en hanterade diskar i en annan resursgrupp, så länge det inte redan används.

Den virtuella datorn måste vara stopped\deallocated och resurs-ID för hanterade diskar kan ersättas med resurs-ID för en annan hanterad disk. 

Kontrollera att typen VM storlek och lagring är kompatibla med den disk som du vill bifoga. Till exempel om disken som du vill använda i Premium-lagring, måste sedan den virtuella datorn kunna Premium-lagring (t.ex. en DS-storlek).

Den här artikeln kräver Azure CLI version 2.0.25 eller högre. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


Använd [az Disklista](/cli/azure/disk#list) att hämta en lista över diskarna i resursgruppen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Använd [az vm stoppa](/cli/azure/vm#stop) till stop\deallocate den virtuella datorn innan du byter diskarna.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Använd [az vm-uppdateringen](/cli/azure/vm#az-vm-update) med fullständiga resurs-ID för den nya disken för den `--osdisk` parameter 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Starta om den virtuella datorn med hjälp av [az vm start](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Nästa steg**

För att skapa en kopia av en disk, se [ögonblicksbild en disk](snapshot-copy-managed-disk.md).