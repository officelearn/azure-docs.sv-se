---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648810"
---
Åtkomst till privata webbplatser syftar bara på att göra appen tillgänglig från ett privat nätverk, till exempel ett virtuellt Azure-nätverk.

* Åtkomst till privata webbplatser är tillgängligt i [Premium](../articles/azure-functions/functions-premium-plan.md)-, [konsumtions](../articles/azure-functions/functions-scale.md#consumption-plan)-och [app Services](../articles/azure-functions/functions-scale.md#app-service-plan) planerna när tjänstens slut punkter konfigureras.
    * Tjänst slut punkter kan konfigureras per app med hjälp av **plattforms funktioner**  >  **nätverk**  >  **Konfigurera åtkomst begränsningar**  >  **Lägg till regel**. Virtuella nätverk kan nu väljas som regel typ.
    * Mer information finns i [tjänst slut punkter för virtuella nätverk](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Tänk på att med tjänst slut punkter har din funktion fortfarande fullständig utgående åtkomst till Internet, även om du har konfigurerat Virtual Network-integrering.
* Åtkomst till privata webbplatser är också tillgänglig i en App Service-miljön som har kon figurer ATS med en intern belastningsutjämnare (ILB). Mer information finns i [skapa och använda en intern belastningsutjämnare med en app service-miljön](../articles/app-service/environment/create-ilb-ase.md).

Information om hur du konfigurerar åtkomst till en privat plats finns i [upprätta Azure Functions privat plats åtkomst](../articles/azure-functions/functions-create-private-site-access.md).