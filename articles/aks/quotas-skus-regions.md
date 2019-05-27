---
title: 'Kvoter, SKU: er och regiontillgänglighet i Azure Kubernetes Service (AKS)'
description: Lär dig mer om standardkvoter, begränsad noden VM SKU-storlekar och regiontillgänglighet för Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901453"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvoter, begränsningar för VM-storlek och regiontillgänglighet i Azure Kubernetes Service (AKS)

Alla Azure-tjänster ange standardgränser och kvoter för resurser och funktioner. Vissa SKU: er för virtuella datorer (VM) är också begränsad till användning.

Den här artikeln beskriver standardresursgränserna för Azure Kubernetes Service (AKS) resurser och tillgängligheten för AKS i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Etablerad infrastruktur

Alla andra begränsningar för nätverk, beräkning och lagring gäller för den etablerade infrastrukturen. Om relevanta begränsningar, finns i [Azure-prenumeration och tjänstbegränsningar](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> När du uppgraderar ett AKS-kluster förbrukas tillfälligt ytterligare resurser. Dessa resurser inkluderar tillgängliga IP-adresser i ett undernät för virtuellt nätverk eller vCPU-kvoten för virtuella datorer. Om du använder Windows Server-behållare (för närvarande i förhandsversion i AKS), är det enda godkända sättet att installera de senaste uppdateringarna till noder att utföra en uppgraderingsåtgärd. En misslyckad kluster uppgraderingsprocessen kan tyda på att det inte finns tillgänglig IP-adress utrymme eller vCPU kvot att hantera dessa tillfälliga resurser. Mer information om uppgraderingsprocessen för Windows Server-noden finns i [uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Begränsad VM-storlekar

Varje nod i ett AKS-kluster innehåller en fast mängd beräkningsresurser som virtuella processorer och minne. Om ett AKS-noden innehåller inte tillräckligt med beräkningsresurser, misslyckas poddar ska kunna köras korrekt. Att säkerställa att de nödvändiga *kube system* poddar och dina program på ett tillförlitligt sätt kan schemaläggas kan inte använda följande VM SKU: er i AKS:

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

Vissa standardgränser och kvoter kan ökas. Om din resurs har stöd för en ökning, begär ökning via en [Azure-supportbegäran] [ azure-support] (för **typ av problem**väljer **kvot** ).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
