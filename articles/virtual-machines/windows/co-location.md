---
title: Samlokalisera virtuella windows azure-datorer
description: Lär dig mer om hur samlokalisering av Azure VM-resurser kan förbättra svarstiden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266798"
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