---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204545"
---
De här exemplen illustrerar olika uppgifter som kan utföras i hanteringsscenarier med flera klientorganisationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Skapar ett nyckelvalv i kundens klientorganisation och skapar åtkomstprinciper.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Distribuera lagringskonton i två olika resursgrupper.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Skapar Azure-hanteringstjänster, kopplar dem till varandra och distribuerar ytterligare lösningar. För en distribution från slutpunkt till slutpunkt använder du mallen **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Aktiverar och konfigurerar Azure Security Center i Azure-målprenumerationen. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Distribuerar och aktiverar Azure Sentinel på en befintlig Log Analytics-arbetsyta i en delegerad prenumeration. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Med dessa mallar kan du distribuera VM-tillägg för Log Analytics till virtuella Windows- och Linux-datorer och ansluta dem till den angivna Log Analytics-arbetsytan |
