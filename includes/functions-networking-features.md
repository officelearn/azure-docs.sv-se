---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906740"
---


| Funktion |[Förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premiumplan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikerad plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Inkommande IP-begränsningar och åtkomst till privata platser](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Virtual Network-integration](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nej|✅Ja (regional)|✅Ja (regional och gateway)|✅Ja| ✅Ja|
|[Virtuella nätverks utlösare (icke-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nej| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybrid anslutningar](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (endast Windows)|❌Nej|✅Ja|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nej| ✅Ja|✅Ja|✅Ja|✅Ja|