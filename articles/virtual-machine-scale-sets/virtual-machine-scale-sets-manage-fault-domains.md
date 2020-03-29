---
title: Hantera feldomäner i Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig hur du väljer rätt antal FD:er när du skapar en skaluppsättning för virtuella datorer.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275731"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Välja rätt antal feldomäner för skaluppsättningar för den virtuella datorn
Skalningsuppsättningar för virtuella datorer skapas med fem feldomäner som standard i Azure-regioner utan zoner. För de regioner som stöder zondistribution av skaluppsättningar för virtuella datorer och det här alternativet är markerat är standardvärdet för antalet feldomäner 1 för var och en av zonerna. FD=1 innebär i det här fallet att VM-instanserna som tillhör skalningsuppsättningen kommer att spridas över många rack på bästa sätt.

Du kan också överväga att justera antalet skalenade feldomäner med antalet feldomäner för hanterade diskar. Den här justeringen kan förhindra förlust av kvorum om en hel feldomän för hanterade diskar går ned. Antalet fV kan anges till mindre än eller lika med antalet feldomäner för hanterade diskar som är tillgängliga i var och en av regionerna. Läs det här [dokumentet](../virtual-machines/windows/manage-availability.md) om du vill veta hur många feldomäner för hanterade diskar per region.

## <a name="rest-api"></a>REST API
Du kan ange `properties.platformFaultDomainCount` egenskapen till 1, 2 eller 3 (standard 5 om den inte anges). Se dokumentationen för REST API [här](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Du kan ställa `--platform-fault-domain-count` in parametern till 1, 2 eller 3 (standard 5 om den inte anges). Se dokumentationen för Azure CLI [här](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

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
- Läs mer om [tillgänglighets- och redundansfunktioner](../virtual-machines/windows/availability.md) för Azure-miljöer.
