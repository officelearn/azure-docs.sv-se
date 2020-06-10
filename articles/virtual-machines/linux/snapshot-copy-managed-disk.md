---
title: Skapa en ögonblicks bild av en virtuell hård disk i Azure
description: Lär dig hur du skapar en kopia av en virtuell hård disk i Azure som en säkerhets kopiering eller för fel söknings problem.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7f4489b3073462a93319886ee5560ed5a6660111
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659960"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild 

Ta en ögonblicks bild av ett operativ system eller en data disk för säkerhets kopiering eller för att felsöka problem med virtuella datorer. En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. 

## <a name="use-azure-cli"></a>Använda Azure CLI 

I följande exempel krävs att du använder [Cloud Shell](https://shell.azure.com/bash) eller har Azure CLI installerat.

Följande steg visar hur du tar en ögonblicks bild med kommandot **AZ Snapshot Create** med parametern **--Source-disk** . I följande exempel förutsätts att det finns en virtuell dator som heter *myVM* i resurs gruppen *myResourceGroup* .

Hämta disk-ID med [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Ta en ögonblicks bild med namnet *osDisk-backup* med [AZ Snapshot Create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Om du vill lagra din ögonblicks bild i en zon fri lagring måste du skapa den i en region som har stöd för [tillgänglighets zoner](../../availability-zones/az-overview.md) och inkludera parametern **--SKU Standard_ZRS** .

Du kan se en lista över ögonblicks bilderna med [AZ Snapshot List](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Använda Azure-portalen 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Börja i det övre vänstra hörnet, klicka på **skapa en resurs** och Sök efter **ögonblicks bild**. Välj **ögonblicks bild** från Sök resultaten.
3. Klicka på **skapa**på bladet **ögonblicks bild** .
4. Ange ett **namn** för ögonblicks bilden.
5. Välj en befintlig resurs grupp eller Skriv namnet på en ny resurs grupp. 
7. För **käll disk**väljer du den hanterade disk som ska avbildas.
8. Välj den **Kontotyp** som ska användas för att lagra ögonblicks bilden. Använd **standard HDD** om du inte behöver den lagrad på ett högt presterande SSD.
9. Klicka på **Skapa**.


## <a name="next-steps"></a>Nästa steg

 Skapa en virtuell dator från en ögonblicks bild genom att skapa en hanterad disk från ögonblicks bilden och sedan ansluta den nya hanterade disken som operativ system disk. Mer information finns i [skapa en virtuell dator från ett ögonblicks bild](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skript.

