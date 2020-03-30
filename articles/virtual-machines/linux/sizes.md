---
title: Storlekar på virtuella Linux-datorer i Azure
description: Visar en lista över de olika storlekar som är tillgängliga för virtuella Linux-datorer i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 46fad0dd2e39f1f563ed248458da622ad83f061e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161124"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Storlekar för virtuella Linux-datorer i Azure

I den här artikeln beskrivs tillgängliga storlekar och alternativ för de virtuella Azure-datorer som du kan använda för att köra dina Linux-appar och arbetsbelastningar. Det ger också distributionsöverväganden att vara medveten om när du planerar att använda dessa resurser. Den här artikeln är också tillgänglig för [virtuella Windows-datorer](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Typ | Storlekar | Beskrivning |
|------|-------|-------------|
| [Generellt syfte](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2  | Balanserat förhållande mellan CPU och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](../sizes-compute.md) | Fsv2 (på andra sätt) | Högt förhållande mellan CPU och minne. Bra för mellantrafik webbservrar, nätverksenheter, batchprocesser och programservrar. |
| [Minnesoptimerad](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Högt förhållande mellan minne och CPU. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](../sizes-storage.md) | Lsv2 (på andra sätt) | Hög diskgenomströmning och IO perfekt för Big Data, SQL, NoSQL-databaser, datalagring och stora transaktionsdatabaser.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (förhandsversion), NV, NVv3, NVv4 | Specialiserade virtuella maskiner riktade för tung grafisk rendering och videoredigering, samt modellutbildning och inferencing (ND) med djupinlärning. Finns med en eller flera GPU:er. |
| [Databehandling med höga prestanda](../sizes-hpc.md) | HB, HBv2, HC, H | Våra snabbaste och mest kraftfulla virtuella CPU-datorer med rdma (high-throughput network interfaces) (rdma) (high-throughput network interfaces). |

- Information om prissättning av de olika storlekarna finns i [Prissättning för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Information om hur du är tillgänglig för vm-storlekar i Azure-regioner finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).
- Information om allmänna begränsningar för virtuella Azure-datorer finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-subscription-service-limits.md).
- Läs mer om hur [Azure compute units (ACU)](../acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.

## <a name="rest-api"></a>REST API

Information om hur du använder REST API för att fråga efter VM-storlekar finns i följande avsnitt:

- [Lista tillgängliga storlekar för virtuella datorer för storleksändring](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista tillgängliga storlekar för virtuella datorer för en prenumeration](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Lista tillgängliga storlekar för virtuella datorer i en tillgänglighetsuppsättning](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Läs mer om hur [Azure compute units (ACU)](../acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Läs mer om beräkningsprestanda för virtuella Linux-datorer med hjälp av [CoreMarks benchmarkpoäng](compute-benchmark-scores.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika vm-storlekar som är tillgängliga:

- [Generellt syfte](../sizes-general.md)
- [Beräkningsoptimerad](../sizes-compute.md)
- [Minnesoptimerad](../sizes-memory.md)
- [Lagringsoptimerad](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Databehandling med höga prestanda](../sizes-hpc.md)
- Kontrollera [föregående generation](../sizes-previous-gen.md) sida för A Standard, Dv1 (D1-4 och D11-14 v1), och A8-A11-serien