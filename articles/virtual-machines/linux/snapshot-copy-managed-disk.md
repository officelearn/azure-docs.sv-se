---
title: Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs
description: Lär dig hur du skapar en kopia av en virtuell Hårddisk i Azure som en bakåt upp eller för att felsöka problem.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30318909"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild 

Ta en ögonblicksbild av ett operativsystem eller disk för säkerhetskopiering eller felsökning av problem med VM. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. 

## <a name="use-azure-cli"></a>Använda Azure CLI 

I följande exempel kräver Azure CLI 2.0 installerat och loggat in på ditt Azure-konto. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Följande steg visar hur du tar en ögonblicksbild med hjälp av den `az snapshot create` kommandot med de `--source-disk` parameter. Följande exempel förutsätter att det finns en virtuell dator kallas `myVM` i den `myResourceGroup` resursgruppen.

Hämta disk-ID.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Ta en ögonblicksbild med namnet *osDisk säkerhetskopiering*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Om du vill lagra din ögonblicksbild i zonen flexibel måste du skapa i en region som stöder [tillgänglighet zoner](../../availability-zones/az-overview.md) och inkludera den `--sku Standard_ZRS` parameter.

## <a name="use-azure-portal"></a>Använd Azure Portal 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på Start i det övre vänstra **skapar du en resurs** och Sök efter **ögonblicksbild**.
3. I bladet ögonblicksbild klickar du på **skapa**.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig [resursgrupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny. 
6. Välj ett Azure-datacenter plats.  
7. För **källdisken**, Välj den hanterade disken till ögonblicksbild.
8. Välj den **kontotyp** du vill lagra ögonblicksbilden. Vi rekommenderar **Standard_LRS** om du inte behöver den lagras på en disk med hög prestanda.
9. Klicka på **Skapa**.


## <a name="next-steps"></a>Nästa steg

 Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från ögonblicksbilden och kopplar den nya hantera disken som OS-disk. Mer information finns i [skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skript.

