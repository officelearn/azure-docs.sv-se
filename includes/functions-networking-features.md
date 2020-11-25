---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021015"
---


| Funktion |[Förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premiumplan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikerad plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Inkommande IP-begränsningar och åtkomst till privata platser](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrering av virtuella nätverk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ja (regional)|✅Ja (regional och gateway)|✅Yes| ✅Yes|
|[Virtuella nätverks utlösare (icke-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybrid anslutningar](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (endast Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Utgående IP-begränsningar](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Ja|