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
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456738"
---
Våra olika mallar hjälper dig att hantera vissa integreringsscenarier. Välj det alternativ som passar dig bäst och glöm inte att ändra parameterfilen så att den återspeglar din miljö. Mer information om hur du använder de här filerna i distributionen finns i [Föra över en kund till Azure-delegerad resurshantering](../articles/lighthouse/how-to/onboard-customer.md).

| **Mall** | **Beskrivning** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Registrera en kunds prenumeration för Azure-delegerad resurshantering. En separat distribution krävs för varje prenumeration. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Registrera en eller flera av en kunds resursgrupper för Azure-delegerad resurshantering. Använd **rgDelegatedResourceManagement** för en enskild resursgrupp eller **multipleRgDelegatedResourceManagement**  om du behöver publicera flera resursgrupper i samma prenumeration. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Om du har [publicerat ett erbjudande för hanterade tjänster till Azure Marketplace ](../articles/lighthouse/how-to/publish-managed-services-offers.md) kan du välja att använda den här mallen för att publicera resurser för kunder som har accepterat erbjudandet. Marketplace-värdena i parameterfilen måste matcha de värden som du använde när du publicerade erbjudandet. |

Normalt krävs en separat distribution för varje prenumeration som registreras, men du kan även distribuera mallar över flera prenumerationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Distribuera Azure Resource Manager-mallar i flera prenumerationer. |
