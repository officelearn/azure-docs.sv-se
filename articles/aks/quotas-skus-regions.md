---
title: Kvoter, SKU:er och regiontillgänglighet i Azure Kubernetes Service (AKS)
description: Lär dig mer om standardkvoter, begränsade nod VM SKU-storlekar och regiontillgänglighet för Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252836"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvoter, storleksbegränsningar för virtuella datorer och regiontillgänglighet i Azure Kubernetes Service (AKS)

Alla Azure-tjänster anger standardgränser och kvoter för resurser och funktioner. Vissa virtuella datorer (VM) SKU:er är också begränsade för användning.

I den här artikeln beskrivs standardresursgränserna för AKS-resurser (Azure Kubernetes Service) och tillgängligheten för AKS i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Etablerad infrastruktur

Alla andra begränsningar för nätverk, beräkning och lagring gäller för den etablerade infrastrukturen. För relevanta gränser finns i [Azure-prenumerations- och tjänstgränser](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> När du uppgraderar ett AKS-kluster förbrukas ytterligare resurser tillfälligt. Dessa resurser inkluderar tillgängliga IP-adresser i ett virtuellt nätverksundernät eller virtuell dator vCPU kvot. Om du använder Windows Server-behållare (för närvarande i förhandsversion i AKS) är den enda metod som är godkänd för att tillämpa de senaste uppdateringarna på noderna att utföra en uppgraderingsåtgärd. En misslyckad klusteruppgraderingsprocess kan tyda på att du inte har det tillgängliga IP-adressutrymmet eller vCPU-kvoten för att hantera dessa tillfälliga resurser. Mer information om uppgraderingsprocessen för Windows Server-nod finns [i Uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Begränsade vm-storlekar

Varje nod i ett AKS-kluster innehåller en fast mängd beräkningsresurser som vCPU och minne. Om en AKS-nod innehåller otillräckliga beräkningsresurser kan poddar misslyckas med att köras korrekt. Använd **inte följande virtuella sku:er i AKS**för att säkerställa att de *kube-system poddar* som krävs och dina program kan schemaläggas på ett tillförlitligt sätt:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Mer information om vm-typer och deras [beräkningsresurser][vm-skus]finns i Storlekar för virtuella datorer i Azure .

## <a name="region-availability"></a>Regional tillgänglighet

Den senaste listan över var du kan distribuera och köra kluster finns i [AKS-regionens tillgänglighet][region-availability].

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om din resurs stöder en ökning, begär ökningen via en [Azure-supportbegäran][azure-support] (för **ärendetyp**väljer du **Kvot**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
