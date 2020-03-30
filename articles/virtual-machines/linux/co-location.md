---
title: Samlokalisera virtuella Linux-datorer
description: Lär dig mer om hur samlokalisering av Azure VM-resurser kan förbättra svarstiden.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250288"
---
# <a name="co-locate-resources-for-improved-latency"></a>Samlokalisera resurser för förbättrad svarstid

När du distribuerar ditt program i Azure skapas nätverksfördröjning som påverkar programmets övergripande prestanda när du distribuerar ditt program i Azure. 

## <a name="proximity-placement-groups"></a>Närhetsplaceringsgrupper

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhetsplaceringsgrupp](proximity-placement-groups.md) med Hjälp av Azure CLI.

Lär dig hur du [testar nätverksfördröjning](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Lär dig hur du [optimerar nätverksdataflödet](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Lär dig hur du [använder närhetsplaceringsgrupper med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
