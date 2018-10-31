---
title: Linux VM-storlekar i Azure | Microsoft Docs
description: Visar en lista över de olika storlekarna som är tillgängliga för Linux-datorer i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/01/2018
ms.author: jonbeck
ms.openlocfilehash: 93bfccf8e9120dbbf5618e01b498e30378e6dc89
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238361"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Storlekar för Linux-datorer i Azure
Den här artikeln beskrivs tillgängliga storlekar och alternativ för Azure-datorer som du kan använda för att köra Linux-appar och arbetsbelastningar. Det ger också distributionsöverväganden ska tänka på när du planerar att använda de här resurserna. Den här artikeln är också tillgängligt för [Windows virtuella datorer](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Typ                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generellt syfte](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Balanserat förhållande mellan processor och minne. Utmärkt för tester och utveckling, små till mellanstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](sizes-compute.md)        | Fsv2, Fs, F             | Högt förhållande mellan processor och minne. Bra för webbservrar med medelhög trafik, nätverkstillämpningar, batchprocesser och programservrar.        |
| [Minnesoptimerad](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Högt minne att CPU-förhållande. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](sizes-storage.md)        | Ls                | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, NCv3, ND            | Särskilda virtuella datorer avsedda för tung grafisk rendering och videoredigering samt modellera utbildnings- och inferensjobb (IV) med djupinlärning. Tillgängligt med en eller flera GPU: er.       |
| [Databehandling med höga prestanda](sizes-hpc.md) | H       | Våra virtuella datorer med de snabbaste och mest kraftfulla processorerna med nätverksgränssnitt för stora dataflöden (RDMA). |


<br>

- Information om prissättning i olika storlekar finns i [prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Tillgängligheten för VM-storlekar i Azure-regioner, se [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).
- Allmänna begränsningar på Azure Virtual Machines finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md).
- Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.


## <a name="rest-api"></a>REST-API

Information om hur du använder REST-API för att fråga för VM-storlekar finns i följande:

- [Lista över tillgängliga VM-storlekar för storleksändring](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista över tillgängliga VM-storlekar för en prenumeration](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [Lista över tillgängliga VM-storlekar i en tillgänglighetsuppsättning](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Lär dig mer om compute prestanda för virtuella Linux-datorer med hjälp av den [CoreMark benchmark-poäng](compute-benchmark-scores.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika storlekar som är tillgängliga:
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- Kontrollera den [föregående generations](sizes-previous-gen.md) för A Standard, Dv1 (D1 – 4 och D11 – 14 v1), och A8 – A11-serien



