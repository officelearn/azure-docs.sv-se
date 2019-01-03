---
title: Hantera feldomäner i Azure VM scale sets | Microsoft Docs
description: Lär dig hur du väljer rätt antal FD även skapa en VM-skalningsuppsättning.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/18
ms.author: rajraj
ms.openlocfilehash: 3b5568e0eed06684667b8aeefa389b6d0aab2a5f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636194"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Välja rätt antal feldomäner för virtual machine scale Sets
VM-skalningsuppsättningar skapas med fem feldomäner som standard i Azure-regioner utan zoner. För de regioner som har stöd för zonindelade distribution av VM-skalningsuppsättningar, är standardvärdet för antalet feldomäner 1 för alla zoner. FD = 1 i det här fallet innebär det att de Virtuella datorinstanserna som hör till skalningsuppsättningen sprids över flera rack efter bästa förmåga.

Du kan också justera antalet feldomäner för scale set med antalet feldomäner för Managed Disks. Den här justering kan förhindra förlust av kvorum om en hel Managed Disks feldomän som stängs av. Antalet FD kan anges till mindre än eller lika med antalet hanterade diskar feldomäner som är tillgängliga i var och en av regionerna. Referera till denna [dokumentet](../virtual-machines/windows/manage-availability.md) vill veta mer om antalet feldomäner för hanterade diskar per region.

## <a name="rest-api"></a>REST-API
Du kan ange egenskapen `properties.platformFaultDomainCount` till 1, 2 eller 3 (standard är 5 om inget anges). Läs dokumentationen för REST API [här](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Du kan ange parametern `--platform-fault-domain-count` till 1, 2 eller 3 (standard är 5 om inget anges). Läs dokumentationen för Azure CLI [här](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [tillgänglighet och redundans funktioner](../virtual-machines/windows/regions-and-availability.md) för Azure-miljöer.
