---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986689"
---
Våra olika mallar hjälper dig att hantera vissa integreringsscenarier. Välj det alternativ som passar dig bäst och glöm inte att ändra parameterfilen så att den återspeglar din miljö. Mer information om hur du använder de här filerna i distributionen finns i [Föra över en kund till Azure-delegerad resurshantering](../articles/lighthouse/how-to/onboard-customer.md).

| **Mall** | **Beskrivning** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Registrera en kunds prenumeration för Azure-delegerad resurshantering. En separat distribution krävs för varje prenumeration. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Registrera en eller flera av en kunds resursgrupper för Azure-delegerad resurshantering. Använd **rgDelegatedResourceManagement** för en enskild resursgrupp eller **multipleRgDelegatedResourceManagement**  om du behöver publicera flera resursgrupper i samma prenumeration. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Om du har [publicerat ett erbjudande för hanterade tjänster till Azure Marketplace ](../articles/lighthouse/how-to/publish-managed-services-offers.md) kan du välja att använda den här mallen för att publicera resurser för kunder som har accepterat erbjudandet. Marketplace-värdena i parameterfilen måste matcha de värden som du använde när du publicerade erbjudandet. |

Normalt krävs en separat distribution för varje prenumeration som registreras, men du kan även distribuera mallar över flera prenumerationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Distribuera Azure Resource Manager-mallar i flera prenumerationer. |
