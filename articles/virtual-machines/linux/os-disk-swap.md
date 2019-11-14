---
title: Byt OS-disk för en virtuell Azure-dator med CLI
description: Ändra den operativ system disk som används av en virtuell Azure-dator med hjälp av CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: bfbe47fb68ffe7cee1ee2f9f7b94b418d8da2a1d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035335"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Ändra den OS-disk som används av en virtuell Azure-dator med hjälp av CLI


Om du har en befintlig virtuell dator, men vill byta disk för en säkerhets kopierings disk eller en annan OS-disk, kan du använda Azure CLI för att växla OS-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn. Du kan till och med använda en hanterad disk i en annan resurs grupp, så länge den inte redan används.

Den virtuella datorn måste vara stopped\deallocated. resurs-ID: t för den hanterade disken kan ersättas med resurs-ID: t för en annan hanterad disk. 

Kontrol lera att storleken på den virtuella datorn och lagrings typen är kompatibla med den disk som du vill ansluta. Om den disk som du vill använda till exempel är i Premium Storage måste den virtuella datorn kunna Premium Storage (som en storlek för en DS-serie).

Den här artikeln kräver Azure CLI version 2.0.25 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 


Använd [AZ disk List](/cli/azure/disk) för att hämta en lista över diskarna i resurs gruppen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Använd [AZ VM Stop](/cli/azure/vm) för att stop\deallocate den virtuella datorn innan du byter ut diskarna.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Använd [AZ VM Update](/cli/azure/vm#az-vm-update) med det fullständiga resurs-ID: t för den nya disken för parametern `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Starta om den virtuella datorn med [AZ VM start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Nästa steg**

Information om hur du skapar en kopia av en disk finns i [ögonblicks bilder av en disk](snapshot-copy-managed-disk.md).
