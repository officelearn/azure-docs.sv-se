---
title: Windows VM-storlekar i Azure | Microsoft Docs
description: Listar de olika storlekarna som är tillgängliga för Windows-datorer i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/22/2018
ms.author: jonbeck
ms.openlocfilehash: d4e1b56ba695f3305d1be023c4352a3c4bcfe27d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "34658357"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Storlekar för virtuella Windows-datorer i Azure

Den här artikeln beskriver tillgängliga storlekar och alternativ för Azure virtuella datorer som du kan använda för att köra dina Windows-appar och arbetsbelastningar. Det ger också överväganden vid distribution för att vara medveten om när du planerar att använda dessa resurser.  Den här artikeln är också tillgängligt för [virtuella Linux-datorer](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Typ                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generellt syfte](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2 | Balanserat förhållande mellan processor och minne. Utmärkt för tester och utveckling, små till mellanstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](sizes-compute.md)        | Fsv2, Fs, F             | Högt förhållande mellan processor och minne. Bra för webbservrar med medelhög trafik, nätverkstillämpningar, batchprocesser och programservrar.        |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Förhållandet mellan hög minne till CPU. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)        | Ls                | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC, NCv2, NCv3, ND            | Särskilda virtuella datorer för tunga grafisk återgivning och redigera video samt modellen utbildning och inferencing (IV) med djup learning. Tillgängligt med en eller flera GPU-kort.       |
| [Databehandling med höga prestanda](sizes-hpc.md) | H       | Våra virtuella datorer med de snabbaste och mest kraftfulla processorerna med nätverksgränssnitt för stora dataflöden (RDMA). 


<br> 

- Information om priser med olika storlekar, se [prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Allmänna begränsningar på Azure Virtual Machines finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md).
- Lagringskostnaderna beräknas separat baserat på använda sidor i lagringskontot. Mer information [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Läs mer om hur [Azure compute-enheter (ACU)](acu.md) kan hjälpa dig att jämföra beräkning prestanda över Azure SKU: er.


## <a name="rest-api"></a>REST-API

Information om hur du använder REST-API för att fråga storlekar på VM finns i:

- [Visa en lista med tillgängliga virtuella datorstorlekar för storleksändring](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Visa en lista med tillgängliga virtuella datorstorlekar för en prenumeration](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [Visa en lista med tillgängliga virtuella datorstorlekar i en tillgänglighetsuppsättning](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Läs mer om hur [Azure compute-enheter (ACU)](acu.md) kan hjälpa dig att jämföra beräkning prestanda över Azure SKU: er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Lär dig mer om compute prestanda för virtuella Windows-datorer med hjälp av den [CoreMark benchmark poäng](compute-benchmark-scores.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika VM-storlekar som är tillgängliga:
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md)
- [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- Kontrollera den [föregående generation](sizes-previous-gen.md) för Standard A Dv1 (D1 4 och D11 14 v1), och A8 A11 serien




