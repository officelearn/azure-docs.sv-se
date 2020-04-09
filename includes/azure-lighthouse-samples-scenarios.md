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
ms.openlocfilehash: 4c50ca2449787d681d8b9571083a92077d95dcd8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986625"
---
De här exemplen illustrerar olika uppgifter som kan utföras i hanteringsscenarier med flera klientorganisationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Distribuera lagringskonton i två olika resursgrupper.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Skapar Azure-hanteringstjänster, kopplar dem till varandra och distribuerar ytterligare lösningar. För en distribution från slutpunkt till slutpunkt använder du mallen **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Aktiverar och konfigurerar Azure Security Center i Azure-målprenumerationen. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Distribuerar och aktiverar Azure Sentinel på en befintlig Log Analytics-arbetsyta i en delegerad prenumeration. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Med dessa mallar kan du distribuera VM-tillägg för Log Analytics till virtuella Windows- och Linux-datorer och ansluta dem till den angivna Log Analytics-arbetsytan |
