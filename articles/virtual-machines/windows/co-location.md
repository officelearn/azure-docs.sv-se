---
title: Samplacera virtuella datorer för förbättrad svars tid
description: Lär dig mer om hur samplaceringen av Azure VM-resurser kan förbättra svars tiden.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 85832fe56b064916b50a63bf4b399f0a21afaaac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028165"
---
# <a name="co-locate-resource-for-improved-latency"></a>Samplacera resursen för förbättrad svars tid

När du distribuerar ditt program i Azure skapar spridnings instanser över regioner eller tillgänglighets zoner nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 


## <a name="proximity-placement-groups"></a>Närhetsplaceringsgrupper 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhets placerings grupp](proximity-placement-groups.md) med hjälp av Azure PowerShell.

Lär dig hur du [testar nätverks fördröjningen](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur du [optimerar nätverks data flödet](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Lär dig hur du [använder närhets placerings grupper med SAP-program](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
