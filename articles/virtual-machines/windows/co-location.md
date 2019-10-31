---
title: Samordna virtuella Windows Azure-datorer
description: Lär dig mer om hur samplaceringen av Azure VM-resurser kan förbättra svars tiden.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171083"
---
# <a name="co-locate-resource-for-improved-latency"></a>Samplacera resursen för förbättrad svars tid

När du distribuerar ditt program i Azure skapar spridnings instanser över regioner eller tillgänglighets zoner nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 


## <a name="proximity-placement-groups"></a>Placerings grupper för närhet 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhets placerings grupp](proximity-placement-groups.md) med hjälp av Azure PowerShell.

Lär dig hur du [testar nätverks fördröjningen](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur du [optimerar nätverks data flödet](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Lär dig hur du [använder närhets placerings grupper med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).