---
title: Kvoter, SKU och regiontillgänglighet i Azure Kubernetes Service (AKS)
description: Lär dig mer om standardkvoter, begränsad noden VM SKU-storlekar och regiontillgänglighet för Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413054"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvoter, begränsningar för VM-storlek och regiontillgänglighet i Azure Kubernetes Service (AKS)

Alla Azure-tjänster har vissa standardgränser och kvoter för resurser och funktioner. För nodstorleken vissa virtuella datorer (VM) SKU: er är sedan begränsade för användning.

Den här artikeln beskriver standardresursgränserna för resurser i Azure Kubernetes Service (AKS), samt tillgänglighet för AKS-tjänsten i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Etablerad infrastruktur

Alla andra begränsningar för nätverk, beräkning och lagring gäller för den etablerade infrastrukturen. I informationen om [prenumerations- och tjänstbegränsningar i Azure](../azure-subscription-service-limits.md) finns information om relevanta begränsningar.

## <a name="restricted-vm-sizes"></a>Begränsad VM-storlekar

Varje nod i ett AKS-kluster innehåller en fast mängd beräkningsresurser som virtuella processorer och minne. Om ett AKS-noden innehåller inte tillräckligt med beräkningsresurser, misslyckas poddar ska kunna köras korrekt. Att säkerställa att de nödvändiga *kube system* poddar och dina program på ett tillförlitligt sätt kan schemaläggas, följande VM SKU: er kan inte användas i AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Läs mer på VM-typer och deras beräkningsresurser [storlekar för virtuella datorer i Azure][vm-skus].

## <a name="region-availability"></a>Regional tillgänglighet

För den senaste listan över var du kan distribuera och kör kluster, se [AKS regiontillgänglighet][region-availability].

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om du vill begära en ökning av en eller flera resurser som kan ökas skickar du in en [Azure-supportbegäran][azure-support] (välj ”Kvot” för **Typ av problem**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
