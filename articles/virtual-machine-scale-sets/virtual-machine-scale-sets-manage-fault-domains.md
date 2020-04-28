---
title: Hantera fel domäner i skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du väljer rätt antal fd när du skapar en skalnings uppsättning för virtuella datorer.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76275731"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Välja rätt antal feldomäner för skaluppsättningar för den virtuella datorn
Skalnings uppsättningar för virtuella datorer skapas med fem fel domäner som standard i Azure-regioner utan zoner. För regioner som stöder zonindelade-distribution av skalnings uppsättningar för virtuella datorer och det här alternativet har valts är standardvärdet för antal fel domäner 1 för var och en av zonerna. FD = 1 i det här fallet innebär det att de virtuella dator instanserna som hör till skalnings uppsättningen kommer att spridas över flera rack på bästa möjliga sätt.

Du kan också överväga att justera antalet fel domäner för skalnings uppsättningar med antalet Managed Disks fel domäner. Den här justeringen kan hjälpa till att förhindra förlust av kvorum om hela Managed Disks fel domänen slutar fungera. Antalet FD-instanser kan anges till mindre än eller lika med antalet Managed Disks fel domäner som är tillgängliga i varje region. Läs det här [dokumentet](../virtual-machines/windows/manage-availability.md) om du vill veta mer om antalet Managed disks fel domäner per region.

## <a name="rest-api"></a>REST-API
Du kan ange egenskapen `properties.platformFaultDomainCount` till 1, 2 eller 3 (Standardvärdet är 5 om inget anges). Läs dokumentationen för REST API [här](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Du kan ange parametern `--platform-fault-domain-count` till 1, 2 eller 3 (Standardvärdet är 5 om inget anges). Se dokumentationen för Azure CLI [här](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

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
- Lär dig mer om [tillgänglighets-och redundans funktioner](../virtual-machines/windows/availability.md) för Azure-miljöer.
