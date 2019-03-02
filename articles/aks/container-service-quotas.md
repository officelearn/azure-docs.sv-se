---
title: Kvoter och regional tillgänglighet för Azure Kubernetes Service (AKS)
description: Standardkvoter och regional tillgänglighet för Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: c8a2c0cac963fcc0622cff547e85593a13aa076a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243847"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Kvoter och regional tillgänglighet för Azure Kubernetes Service (AKS)

Alla Azure-tjänster har vissa standardgränser och kvoter för resurser och funktioner. Följande avsnitt beskriver standardresursgränserna för flera Azure Kubernetes Service-resurser (AKS), samt tillgänglighet för AKS-tjänsten i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Etablerad infrastruktur

Alla andra begränsningar för nätverk, beräkning och lagring gäller för den etablerade infrastrukturen. I informationen om [prenumerations- och tjänstbegränsningar i Azure](../azure-subscription-service-limits.md) finns information om relevanta begränsningar.

## <a name="region-availability"></a>Regional tillgänglighet

Azure Kubernetes Service (AKS) är tillgängligt i följande regioner:

- Östra Australien
- Sydöstra Australien
- Centrala Kanada
- Östra Kanada
- Centrala USA
- Östasien
- Östra USA
- USA, östra 2
- Frankrike, centrala
- Östra Japan
- Norra Europa
- Sydostasien
- Södra Indien
- Storbritannien, södra
- Storbritannien, västra
- Västra Europa
- Västra USA
- Västra USA 2

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om du vill begära en ökning av en eller flera resurser som kan ökas skickar du in en [Azure-supportbegäran][azure-support] (välj ”Kvot” för **Typ av problem**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
