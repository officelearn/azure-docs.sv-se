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
ms.openlocfilehash: 21351654f01127acb5fe712021ceebb31b020bdc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484952"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Storlekar för virtuella Linux-datorer i Azure

I den här artikeln beskrivs tillgängliga storlekar och alternativ för de virtuella Azure-datorer som du kan använda för att köra dina Linux-appar och arbets belastningar. Den innehåller också distributions överväganden för att vara medveten om när du planerar att använda dessa resurser. Den här artikeln är också tillgänglig för [virtuella Windows-datorer](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Typ | Storlekar | Beskrivning |
|------|-------|-------------|
| [Generellt syfte](../sizes-general.md)   | B, Dsv3, dv3, Dasv4, Dav4, DSv2, Dv2, AV2, DC  | Balanserat förhållande mellan processor och minne. Perfekt för testning och utveckling, små till medel stora databaser och webb servrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](../sizes-compute.md) | Fsv2 | Högt förhållande mellan processor och minne. Passar för medel stora trafik webb servrar, nätverks enheter, batch-processer och program servrar. |
| [Minnesoptimerad](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Högt förhållande mellan minne och processor. Perfekt för Relations databas servrar, medel stora till stora cacheminnen och intern analys.                 |
| [Lagringsoptimerad](../sizes-storage.md) | Lsv2 | Hög disk data flöde och i/o perfekt för Big data, SQL, NoSQL-databaser, data lager hantering och stora transaktions databaser.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (för hands version), NV, NVv3, NVv4 | Specialiserade virtuella datorer som är riktade mot tung grafisk rendering och video redigering, samt modell utbildning och inferencing (ND) med djup inlärning. Tillgängligt med en eller flera GPU: er. |
| [Databehandling med höga prestanda](../sizes-hpc.md) | HB, HC,  H | Våra snabbaste och mest kraftfulla processor virtuella datorer med valfria nätverks gränssnitt (RDMA) med höga data flöden. |

- Information om priser för de olika storlekarna finns [Virtual Machines prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- För tillgänglighet för VM-storlekar i Azure-regioner, se [produkter tillgängliga per region](https://azure.microsoft.com/regions/services/).
- Om du vill se allmänna gränser för virtuella Azure-datorer kan du läsa om [begränsningar, kvoter och begränsningar för Azure-prenumerationer och tjänster](../../azure-subscription-service-limits.md).
- Lär dig mer om hur [Azure Compute Units (ACU)](../acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

## <a name="rest-api"></a>REST-API

Information om hur du använder REST API för att fråga efter VM-storlekar finns i följande avsnitt:

- [Lista tillgängliga storlekar för virtuella datorer för storleks ändring](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista tillgängliga storlekar för virtuella datorer för en prenumeration](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Visa en lista över tillgängliga storlekar för virtuella datorer i en tillgänglighets uppsättning](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Lär dig mer om hur [Azure Compute Units (ACU)](../acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Lär dig mer om beräknings prestanda för virtuella Linux-datorer med [CoreMark-benchmark-resultat](compute-benchmark-scores.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika VM-storlekar som är tillgängliga:

- [Generellt syfte](../sizes-general.md)
- [Beräkningsoptimerad](../sizes-compute.md)
- [Minnesoptimerad](../sizes-memory.md)
- [Lagringsoptimerad](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Databehandling med höga prestanda](../sizes-hpc.md)
- Kontrol lera [föregående generations](../sizes-previous-gen.md) sida för en standard, DV1 (D1-4 och D11-14 v1) och A8-A11-serien