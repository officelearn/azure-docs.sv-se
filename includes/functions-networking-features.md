---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906740"
---


| Funktion |[Förbruknings plan](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premiumplan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikerad plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Inkommande IP-begränsningar och åtkomst till privata platser](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integrering av virtuella nätverk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ja (regional)|✅Ja (regional och gateway)|✅Ja| ✅Ja|
|[Virtuella nätverks utlösare (icke-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybrid anslutningar](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (endast Windows)|❌No|✅Ja|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Ja|✅Ja|✅Ja|✅Ja|