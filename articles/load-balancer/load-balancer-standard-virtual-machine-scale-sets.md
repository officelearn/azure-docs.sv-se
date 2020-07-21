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
ms.openlocfilehash: f5a8453f84854108facb4da4616a7d362e0fb38c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532277"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer med skalnings uppsättningar för virtuella Azure-datorer

När du arbetar med skalnings uppsättningar och belastningsutjämnare för virtuella datorer bör du tänka på följande rikt linjer:

## <a name="multiple-virtual-machine-scale-sets-cant-use-the-same-load-balancer"></a>Flera skalnings uppsättningar för virtuella datorer kan inte använda samma belastningsutjämnare
## <a name="port-forwarding-and-inbound-nat-rules"></a>Port vidarebefordring och inkommande NAT-regler:
  * När skalnings uppsättningen har skapats kan Server dels porten inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning av belastningsutjämnaren. Om du vill ändra porten kan du ta bort hälso avsökningen genom att uppdatera skalnings uppsättningen för den virtuella Azure-datorn, uppdatera porten och sedan konfigurera hälso avsökningen igen.
  * När du använder skalnings uppsättningen för den virtuella datorn i belastningsutjämnaren för belastningsutjämnaren skapas standard reglerna för inkommande NAT automatiskt.
## <a name="inbound-nat-pool"></a>Inkommande NAT-pool:
  * Varje skalnings uppsättning för virtuella datorer måste ha minst en inkommande NAT-pool. 
  * Inkommande NAT-pool är en samling inkommande NAT-regler. En inkommande NAT-pool kan inte stödja flera skalnings uppsättningar för virtuella datorer.
  
## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler:
  * När du använder skalnings uppsättningen för den virtuella datorn i belastningsutjämnaren, skapas standard regeln för belastnings utjämning automatiskt.
## <a name="outbound-rules"></a>Utgående regler:
  *  Om du vill skapa en regel för utgående trafik för en backend-pool som redan refereras till av en belastnings Utjämnings regel måste du först markera **"skapa implicit utgående regel"** som **Nej** i portalen när den inkommande belastnings Utjämnings regeln skapas.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Skapa belastnings Utjämnings regel" border="true":::

Följande metoder kan användas för att distribuera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer.

* [Konfigurera en skalnings uppsättning för en virtuell dator med en befintlig Azure Load Balancer med hjälp av Azure Portal](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer att använda Azure PowerShell](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer med hjälp av Azure CLI](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
