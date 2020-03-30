---
title: Azure Security Center och Azure Container Registry
description: Lär dig mer om Azure Security Centers integrering med Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206001"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry-integrering med Security Center

Azure Container Registry (ACR) är en hanterad, privat Docker-registertjänst som lagrar och hanterar dina behållaravbildningar för Azure-distributioner i ett centralt register. Den är baserad på Hamnregistret 2.0 med öppen källkod.

Om du använder Azure Security Centers standardnivå kan du lägga till paketet Container Register.If you're on Azure Security Center's standard tier, you can add the Container Registerstries bundle. Den här valfria funktionen ger djupare insyn i bildernas sårbarheter i arm-baserade register. Aktivera eller inaktivera paketet på prenumerationsnivå för att täcka alla register i en prenumeration. Den här funktionen debiteras per bild, som visas på [prissidan](security-center-pricing.md). Om du aktiverar paketet Container Register säkerställer du att Security Center är redo att skanna avbildningar som skjuts till registret. 

När en avbildning trycks till registret söker Säkerhetscenter automatiskt igenom avbildningen. Om du vill utlösa genomsökningen av en bild skickar du den till databasen.

När genomsökningen är klar (vanligtvis efter cirka 10 minuter) finns resultaten i Säkerhetscenterrekommendationer som denna:

[![Exempel på Rekommendation för Azure Security Center om säkerhetsproblem som upptäckts i en ACR-avbildning (Azure Container Registry)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Fördelar med integration

Security Center identifierar ARM-baserade ACR-register i din prenumeration och tillhandahåller sömlöst:

* **Azure-native sårbarhetssökning** för alla pushade Linux-avbildningar. Security Center söker igenom bilden med hjälp av en skanner från den branschledande sårbarhetsskanningsleverantören Qualys. Den här inbyggda lösningen är sömlöst integrerad som standard.

* **Säkerhetsrekommendationer** för Linux-avbildningar med kända sårbarheter. Security Center innehåller information om varje rapporterat säkerhetsproblem och en allvarlighetsgrad. Dessutom ger det vägledning för hur du åtgärdar de specifika säkerhetsproblem som finns på varje avbildning som skjuts till registret.

![Översikt över Azure Security Center och Azure Container Registry (ACR) på hög nivå](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Nästa steg

Mer information om säkerhetsfunktionerna i Security Center finns i:

* [Azure Security Center och containersäkerhet](container-security.md)

* [Integration med Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Skydd för virtuella](security-center-virtual-machine-protection.md) datorer - Beskriver Säkerhetscentrets rekommendationer
