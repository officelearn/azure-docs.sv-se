---
title: Samlokalisera virtuella datorer för förbättrad svarstid
description: Lär dig mer om hur samlokalisering av Azure VM-resurser kan förbättra svarstiden.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083180"
---
# <a name="co-locate-resource-for-improved-latency"></a>Samlokalisera resurs för förbättrad svarstid

När du distribuerar ditt program i Azure skapas nätverksfördröjning som påverkar programmets övergripande prestanda när du distribuerar ditt program i Azure. 


## <a name="proximity-placement-groups"></a>Närhetsplaceringsgrupper 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhetsplaceringsgrupp](proximity-placement-groups.md) med Azure PowerShell.

Lär dig hur du [testar nätverksfördröjning](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur du [optimerar nätverksdataflödet](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Lär dig hur du [använder närhetsplaceringsgrupper med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).