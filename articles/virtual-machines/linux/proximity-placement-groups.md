---
title: Använd närhets placerings grupper för virtuella Linux-datorer | Microsoft Docs
description: Lär dig mer om att skapa och använda närhets placerings grupper för virtuella Linux-datorer i Azure.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385743"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Förhandsversion: Distribuera virtuella datorer till närhets placerings grupper med Azure CLI

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en [närhets placerings grupp](co-location.md#preview-proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.

> [!IMPORTANT]
> Närhets placerings grupper finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Placerings grupper för närhet är inte tillgängliga i dessa regioner under för hands versionen: **Östra Japan**, östra **Australien** och **Indien, centrala**.

## <a name="create-the-proximity-placement-group"></a>Skapa placerings gruppen närhet
Skapa en närhets placerings [`az ppg create`](/cli/azure/ppg#az-ppg-create)grupp med. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista närhets placerings grupper

Du kan visa alla närhets placerings grupper med [AZ PPG List](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i närhets placerings gruppen med [den nya virtuella AZ-datorn](/cli/azure/vm#az-vm-create).
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

Du kan se den virtuella datorn i närhets placerings gruppen med [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighets uppsättning i närheten av placerings gruppen. Använd samma `--ppg` parameter med [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) för att skapa en tillgänglighets uppsättning och alla virtuella datorer i tillgänglighets uppsättningen kommer också att skapas i samma närhets placerings grupp.

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalnings uppsättning i närheten av placerings gruppen. Använd samma `--ppg` parameter med [AZ VMSS Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) för att skapa en skalnings uppsättning och alla instanser kommer att skapas i samma närhets placerings grupp.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure CLI](/cli/azure/ppg) -kommandon för närhets placerings grupper.
