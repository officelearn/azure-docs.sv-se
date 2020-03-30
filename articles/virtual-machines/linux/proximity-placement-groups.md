---
title: Använd närhetsplaceringsgrupper för virtuella Linux-datorer
description: Lär dig mer om hur du skapar och använder närhetsplaceringsgrupper för virtuella Linux-datorer i Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73171049"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Distribuera virtuella datorer till närhetsplaceringsgrupper med Azure CLI

Om du vill få virtuella datorer så nära som möjligt och uppnå lägsta möjliga svarstid bör du distribuera dem inom en [närhetsplaceringsgrupp](co-location.md#proximity-placement-groups).

En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser är fysiskt placerade nära varandra. Närhetsplaceringsgrupper är användbara för arbetsbelastningar där låg svarstid är ett krav.


## <a name="create-the-proximity-placement-group"></a>Skapa proximity placement-gruppen
Skapa en närhetsplaceringsgrupp med [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista närhetsplaceringsgrupper

Du kan lista alla dina närhetsplaceringsgrupper med [az ppg-listan](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i gruppen för närhetsplacering med [nya az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Du kan se den virtuella datorn i gruppen proximity placement med [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighetsuppsättning i din närhetsplaceringsgrupp. Använd samma `--ppg` parameter med [az vm-tillgänglighetsuppsättning skapa](/cli/azure/vm/availability-set#az-vm-availability-set-create) för att skapa en tillgänglighetsuppsättning och alla virtuella datorer i tillgänglighetsuppsättningen skapas också i samma närhetsplaceringsgrupp.

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalningsuppsättning i din närhetsplaceringsgrupp. Använd samma `--ppg` parameter med [az vmss skapa](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) för att skapa en skalningsuppsättning och alla instanser kommer att skapas i samma närhetsplaceringsgrupp.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure CLI-kommandon](/cli/azure/ppg) för närhetsplaceringsgrupper.
