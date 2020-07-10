---
title: VM-storlekar i Azure
description: Visar en lista över de olika storlekar som är tillgängliga för virtuella datorer i Azure.
author: ju-shim
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a60200bd35d142b8b55a40d6912f946f9421f52e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207661"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Storlekar för virtuella Windows-datorer i Azure

I den här artikeln beskrivs tillgängliga storlekar och alternativ för de virtuella Azure-datorer som du kan använda för att köra dina Windows-appar och arbets belastningar. Den innehåller också distributions överväganden för att vara medveten om när du planerar att använda dessa resurser.  Den här artikeln gäller både virtuella Windows-och Linux-datorer.

| Typ | Storlekar | Beskrivning |
|------|-------|-------------|
| [Generell användning](../sizes-general.md) | B, Dsv3, dv3, Dasv4, Dav4, DSv2, Dv2, AV2, DC, DCv2, DV4, Dsv4, Ddv4, Ddsv4 | Balanserat förhållande mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](../sizes-compute.md) | Fsv2 | Högt förhållande mellan processor och minne. Passar för medel stora trafik webb servrar, nätverks enheter, batch-processer och program servrar. |
| [Minnesoptimerad](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Högt förhållande mellan minne och processor. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser. |
| [Lagringsoptimerad](../sizes-storage.md)  | Lsv2 | Hög disk data flöde och i/o perfekt för Big data, SQL, NoSQL-databaser, data lager hantering och stora transaktions databaser.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (för hands version), NV, NVv3, NVv4 | Specialiserade virtuella datorer som är riktade mot tung grafisk rendering och video redigering, samt modell utbildning och inferencing (ND) med djup inlärning. Tillgängligt med en eller flera GPU: er. |
| [Databehandling med höga prestanda](../sizes-hpc.md) | HB, HBv2, HC, H | Våra snabbaste och mest kraftfulla processor virtuella datorer med valfria nätverks gränssnitt (RDMA) med höga data flöden. |

- Information om priser för de olika storlekarna finns [Virtual Machines prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
- Om du vill se allmänna gränser för virtuella Azure-datorer kan du läsa om [begränsningar, kvoter och begränsningar för Azure-prenumerationer och tjänster](../../azure-subscription-service-limits.md).
- Lagringskostnaderna beräknas separat baserat på använda sidor i lagringskontot. Mer information [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).
- Lär dig mer om hur [Azure Compute Units (ACU)](../acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

## <a name="rest-api"></a>REST API

Information om hur du använder REST API för att fråga efter VM-storlekar finns i följande avsnitt:

- [Lista tillgängliga storlekar för virtuella datorer för storleks ändring](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista tillgängliga storlekar för virtuella datorer för en prenumeration](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Visa en lista över tillgängliga storlekar för virtuella datorer i en tillgänglighets uppsättning](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Lär dig mer om hur [Azure Compute Units (ACU)](../acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Lär dig mer om beräknings prestanda för virtuella Windows-datorer med [CoreMark-benchmark-resultat](compute-benchmark-scores.md).

## <a name="manage-costs"></a>Hantera kostnader

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika VM-storlekar som är tillgängliga:

- [Generell användning](../sizes-general.md)
- [Beräkningsoptimerad](../sizes-compute.md)
- [Minnesoptimerad](../sizes-memory.md)
- [Lagringsoptimerad](../sizes-storage.md)
- [GPU-optimerad](../sizes-gpu.md)
- [Databehandling med höga prestanda](../sizes-hpc.md)
- Kontrol lera [föregående generations](../sizes-previous-gen.md) sida för en standard, DV1 (D1-4 och D11-14 v1) och A8-A11-serien
