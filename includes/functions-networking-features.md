---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578849"
---


| Funktion |[Förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premiumplan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikerad plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Inkommande IP-begränsningar och åtkomst till privata platser](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integrering av virtuella nätverk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Inga|✅Ja (regional)|✅Ja (regional och gateway)|✅Ja| ✅Ja|
|[Virtuella nätverks utlösare (icke-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Inga| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybrid anslutningar](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (endast Windows)|❌Inga|✅Ja|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Inga| ✅Ja|✅Ja|✅Ja|✅Ja|