---
title: Azure Standard Load Balancer och Virtual Machine Scale Sets
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Med den här utbildnings vägen kommer du igång med Azure Standard Load Balancer och Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: fdca40d5113f06d185255be2e237cb52b47e9793
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697449"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer med skalnings uppsättningar för virtuella Azure-datorer

När du arbetar med skalnings uppsättningar och belastningsutjämnare för virtuella datorer bör du tänka på följande rikt linjer:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Port vidarebefordring och inkommande NAT-regler:
  * När skalnings uppsättningen har skapats kan Server dels porten inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning av belastningsutjämnaren. Om du vill ändra porten kan du ta bort hälso avsökningen genom att uppdatera skalnings uppsättningen för den virtuella Azure-datorn, uppdatera porten och sedan konfigurera hälso avsökningen igen.
  * När du använder skalnings uppsättningen för den virtuella datorn i belastningsutjämnaren för belastningsutjämnaren skapas standard reglerna för inkommande NAT automatiskt.
## <a name="inbound-nat-pool"></a>Inkommande NAT-pool:
  * Varje skalnings uppsättning för virtuella datorer måste ha minst en inkommande NAT-pool. 
  * Inkommande NAT-pool är en samling inkommande NAT-regler. En inkommande NAT-pool kan inte stödja flera skalnings uppsättningar för virtuella datorer.
  * Om du vill ta bort en NAT-pool från en befintlig virtuell dators skalnings uppsättning måste du först ta bort NAT-poolen från skalnings uppsättningen. Ett fullständigt exempel som använder CLI visas nedan:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler:
  * När du använder skalnings uppsättningen för den virtuella datorn i belastningsutjämnaren, skapas standard regeln för belastnings utjämning automatiskt.
## <a name="outbound-rules"></a>Utgående regler:
  *  Om du vill skapa en regel för utgående trafik för en backend-pool som redan refereras till av en belastnings Utjämnings regel måste du först markera **"skapa implicit utgående regel"** som **Nej** i portalen när den inkommande belastnings Utjämnings regeln skapas.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Skapa belastnings Utjämnings regel" border="true":::

Följande metoder kan användas för att distribuera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer.

* [Konfigurera en skalnings uppsättning för en virtuell dator med en befintlig Azure Load Balancer med hjälp av Azure Portal](./configure-vm-scale-set-portal.md).
* [Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer att använda Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer med hjälp av Azure CLI](./configure-vm-scale-set-cli.md).