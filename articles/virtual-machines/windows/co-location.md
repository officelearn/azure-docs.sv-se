---
title: Samplacera virtuella datorer för förbättrad svars tid
description: Lär dig mer om hur samplaceringen av Azure VM-resurser kan förbättra svars tiden.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: eb7b5d3b477a511432dbd3ee8a6b382002aaab44
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488480"
---
# <a name="co-locate-resource-for-improved-latency"></a>Samplacera resursen för förbättrad svars tid

När du distribuerar ditt program i Azure skapar spridnings instanser över regioner eller tillgänglighets zoner nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 


## <a name="proximity-placement-groups"></a>Närhetsplaceringsgrupper 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhets placerings grupp](proximity-placement-groups.md) med hjälp av Azure PowerShell.

Lär dig hur du [testar nätverks fördröjningen](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur du [optimerar nätverks data flödet](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Lär dig hur du [använder närhets placerings grupper med SAP-program](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).