---
title: Samordna virtuella Linux-datorer
description: Lär dig mer om hur samplaceringen av Azure VM-resurser kan förbättra svars tiden.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 0a28b238701eeb0572149fb55e1633cdd40ec0a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511032"
---
# <a name="co-locate-resources-for-improved-latency"></a>Samplacera resurser för förbättrad svars tid

När du distribuerar ditt program i Azure skapar spridnings instanser över regioner eller tillgänglighets zoner nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 

## <a name="proximity-placement-groups"></a>Närhetsplaceringsgrupper

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhets placerings grupp](proximity-placement-groups.md) med hjälp av Azure CLI.

Lär dig hur du [testar nätverks fördröjningen](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Lär dig hur du [optimerar nätverks data flödet](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Lär dig hur du [använder närhets placerings grupper med SAP-program](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
