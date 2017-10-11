---
title: "Kopiera en Azure-hanterade disken för tillbaka in | Microsoft Docs"
description: "Lär dig hur du skapar en kopia av en Azure hanteras Disk som ska använda för tillbaka eller felsökning av problem med disken."
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
ms.openlocfilehash: c91367ef11c9d531bebac7c069d2df586607ec29
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Skapa en kopia av en virtuell Hårddisk lagras som en hanterad Azure-disken med hjälp av hanterade ögonblicksbilder
Ta en ögonblicksbild av en hanterad disk för säkerhetskopiering eller skapa en Disk som hanteras av ögonblicksbilden och koppla den till en virtuell testdator för att felsöka. En ögonblicksbild av en hanterad är en fullständig i tidpunkt kopia av en virtuell dator hanteras Disk. Den skapar en skrivskyddad kopia av den virtuella Hårddisken och som standard lagras som en Standard hanteras Disk. 

Information om priser finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/managed-disks/). <!--Add link to topic or blog post that explains managed disks. -->

Använda Azure-portalen eller Azure CLI 2.0 för att ta en ögonblicksbild av den hanterade disken.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Använda Azure CLI 2.0 för att ta en ögonblicksbild

> [!NOTE] 
> I följande exempel kräver Azure CLI 2.0 installerat och loggat in på ditt Azure-konto.

Följande steg visar hur du hämtar och ta en ögonblicksbild av en hanterad OS disk med den `az snapshot create` kommandot med de `--source-disk` parameter. Följande exempel förutsätter att det finns en virtuell dator kallas `myVM` skapats med en hanterad OS-disk i den `myResourceGroup` resursgruppen.

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
2. Klicka på Start i det övre vänstra **ny** och Sök efter **ögonblicksbild**.
3. I bladet ögonblicksbild klickar du på **skapa**.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig [resursgrupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny. 
6. Välj ett Azure-datacenter plats.  
7. För **källdisken**, Välj den hanterade disken till ögonblicksbild.
8. Välj den **kontotyp** du vill lagra ögonblicksbilden. Vi rekommenderar **Standard_LRS** om du inte behöver den lagras på en disk med hög prestanda.
9. Klicka på **Skapa**.

Om du planerar att använda ögonblicksbilden för att skapa en hanterad Disk och koppla den till en virtuell dator som måste vara höga prestanda, använder du parametern `--sku Premium_LRS` med den `az snapshot create` kommando. Detta skapar ögonblicksbilden så att den lagras som en Premium hanteras Disk. Hanterade Premiumdiskar bättre eftersom de SSD-enheter (SSD), men kostar mer än standarddiskar (HDD).


