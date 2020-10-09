---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111681"
---
Våra olika mallar hjälper dig att hantera vissa integreringsscenarier. Välj det alternativ som passar dig bäst och glöm inte att ändra parameterfilen så att den återspeglar din miljö. Mer information om hur du använder filerna i din distribution finns i [Registrera en kund i Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Mall** | **Beskrivning** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Registrera en kunds prenumeration i Azure Lighthouse. En separat distribution krävs för varje prenumeration. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Registrera en eller flera av en kunds resursgrupper i Azure Lighthouse. Använd **rgDelegatedResourceManagement** för en enskild resursgrupp eller **multipleRgDelegatedResourceManagement**  om du behöver publicera flera resursgrupper i samma prenumeration. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Om du har [publicerat ett erbjudande för hanterade tjänster till Azure Marketplace ](../articles/lighthouse/how-to/publish-managed-services-offers.md) kan du välja att använda den här mallen för att publicera resurser för kunder som har accepterat erbjudandet. Marketplace-värdena i parameterfilen måste matcha de värden som du använde när du publicerade erbjudandet. |

Normalt krävs en separat distribution för varje prenumeration som registreras, men du kan även distribuera mallar över flera prenumerationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Distribuera Azure Resource Manager-mallar i flera prenumerationer. |
