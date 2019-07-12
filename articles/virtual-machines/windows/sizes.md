---
title: Windows VM-storlekar i Azure | Microsoft Docs
description: Visar en lista över de olika storlekarna som är tillgängliga för Windows-datorer i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: jonbeck
ms.openlocfilehash: 78b0de43652aba20563e2712e249e18ccd18c407
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710085"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Storlekar för Windows-datorer i Azure

Den här artikeln beskrivs tillgängliga storlekar och alternativ för Azure-datorer som du kan använda för att köra dina Windows-appar och arbetsbelastningar. Det ger också distributionsöverväganden ska tänka på när du planerar att använda de här resurserna.  Den här artikeln är också tillgängligt för [Linux-datorer](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| type                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generellt syfte](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Balanserat förhållande mellan processor och minne. Utmärkt för tester och utveckling, små till mellanstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](sizes-compute.md)        | Fsv2           | Högt förhållande mellan processor och minne. Bra för webbservrar med medelhög trafik, nätverkstillämpningar, batchprocesser och programservrar.        |
| [Minnesoptimerad](sizes-memory.md)         | Esv3, Ev3, Mv2, M, DSv2, Dv2  | Högt minne att CPU-förhållande. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](sizes-storage.md)        | Lsv2                | Högt diskgenomflöde och I/O som är perfekt för Stordata, SQL, NoSQL-databaser, informationslagerhantering och stora transaktionsdatabaser.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (förhandsversion), NV, NVv3 (förhandsversion) | Särskilda virtuella datorer avsedda för tung grafisk rendering och videoredigering samt modellera utbildnings- och inferensjobb (IV) med djupinlärning. Tillgängligt med en eller flera GPU: er.       |
| [Databehandling med höga prestanda](sizes-hpc.md) | HB, HC,  H | Våra virtuella datorer med de snabbaste och mest kraftfulla processorerna med nätverksgränssnitt för stora dataflöden (RDMA). |


<br> 

- Information om prissättning i olika storlekar finns i [prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Allmänna begränsningar på Azure Virtual Machines finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md).
- Lagringskostnaderna beräknas separat baserat på använda sidor i lagringskontot. Mer information [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.


## <a name="rest-api"></a>REST-API

Information om hur du använder REST-API för att fråga för VM-storlekar finns i följande:

- [Lista över tillgängliga VM-storlekar för storleksändring](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista över tillgängliga VM-storlekar för en prenumeration](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Lista över tillgängliga VM-storlekar i en tillgänglighetsuppsättning](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Lär dig mer om compute prestanda för virtuella Windows-datorer med hjälp av den [CoreMark benchmark-poäng](compute-benchmark-scores.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika storlekar som är tillgängliga:
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)
- [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- Kontrollera den [föregående generations](sizes-previous-gen.md) för A Standard, Dv1 (D1 – 4 och D11 – 14 v1), och A8 – A11-serien




