---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906740"
---


| Funktion |[Förbruknings plan](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premiumplan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikerad plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Inkommande IP-begränsningar och åtkomst till privata platser](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrering av virtuella nätverk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ja (regional)|✅Ja (regional och gateway)|✅Yes| ✅Yes|
|[Virtuella nätverks utlösare (icke-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybrid anslutningar](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (endast Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Utgående IP-begränsningar](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Ja|