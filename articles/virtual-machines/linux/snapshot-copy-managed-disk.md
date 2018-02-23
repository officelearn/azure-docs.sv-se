---
title: "Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs"
description: "Lär dig hur du skapar en kopia av en virtuell Hårddisk i Azure som en bakåt upp eller för att felsöka problem."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 152c5a1103d32af27f689086cfcc9cc1a7acc5d3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild 

Ta en ögonblicksbild av ett operativsystem eller disk VHD för säkerhetskopiering eller felsökning av VM-problem. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Använda Azure CLI 2.0 för att ta en ögonblicksbild

I följande exempel kräver Azure CLI 2.0 installerat och loggat in på ditt Azure-konto. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Följande steg visar hur du tar en ögonblicksbild med hjälp av den `az snapshot create` kommandot med de `--source-disk` parameter. Följande exempel förutsätter att det finns en virtuell dator kallas `myVM` skapats med en hanterad OS-disk i den `myResourceGroup` resursgruppen.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

Utdata ska se ut ungefär:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Använd Azure-portalen för att ta en ögonblicksbild 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på Start i det övre vänstra **skapar du en resurs** och Sök efter **ögonblicksbild**.
3. I bladet ögonblicksbild klickar du på **skapa**.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig [resursgrupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny. 
6. Välj ett Azure-datacenter plats.  
7. För **källdisken**, Välj den hanterade disken till ögonblicksbild.
8. Välj den **kontotyp** du vill lagra ögonblicksbilden. Vi rekommenderar **Standard_LRS** om du inte behöver den lagras på en disk med hög prestanda.
9. Klicka på **Skapa**.

Om du planerar att använda ögonblicksbilden för att skapa en hanterad Disk och koppla den till en virtuell dator som måste vara höga prestanda, använder du parametern `--sku Premium_LRS` med den `az snapshot create` kommando. Detta skapar ögonblicksbilden så att den lagras som en Premium hanteras Disk. Hanterade Premiumdiskar bättre eftersom de SSD-enheter (SSD), men kostar mer än standarddiskar (HDD).


## <a name="next-steps"></a>Nästa steg

 Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från ögonblicksbilden och kopplar den nya hantera disken som OS-disk. Mer information finns i [skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skript.

