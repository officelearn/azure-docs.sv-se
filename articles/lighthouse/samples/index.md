---
title: Exempel och mallar för Azure Lighthouse
description: Dessa exempel och Azure Resource Manager-mallar visar hur du registrerar kunder för Azure-delegerad resurshantering och ger stöd för Azure Lighthouse-scenarier.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 07/11/2019
ms.author: jenhayes
ms.openlocfilehash: d888fb0d50bf0336f8fe830c567586e66065871f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286135"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse-exempel

Följande tabell innehåller länkar till viktiga Azure Resource Manager-mallar för Azure Lighthouse. Dessa filer och mycket annat finns även på [lagringsplatsen för Azure Lighthouse-exempel](https://github.com/Azure/Azure-Lighthouse-samples/).

| **Mall** | **Beskrivning** |
|---------|---------|
| [create-multiple-rgs](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/create-multiple-rgs) | Skapar flera resursgrupper från en enda Azure Resource Manager-mall. |
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Distribuera lagringskonton i två olika resursgrupper. |
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Distribuera Azure Resource Manager-mallar i flera prenumerationer. |
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Registrera en kunds prenumeration för Azure-delegerad resurshantering. |
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Skapar Azure-hanteringstjänster, kopplar dem till varandra och distribuerar ytterligare lösningar. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Aktiverar och konfigurerar Azure Security Center i Azure-målprenumerationen. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Registrera en kunds prenumeration för Azure-delegerad resurshantering, baserat på ett erbjudande om hanterade tjänster på Marketplace. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Registrera en eller flera av en kunds resursgrupper för Azure-delegerad resurshantering. |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md).
- Lär dig hur du [registrerar kunder för Azure-delegerad resurshantering](../how-to/onboard-customer.md) med hjälp av Azure Resource Manager-mallar.
