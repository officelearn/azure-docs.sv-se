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
ms.openlocfilehash: bb7ffb06bb0cffeefae87ada665e2c123213186a
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2020
ms.locfileid: "84218653"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry integration med Security Center

Azure Container Registry (ACR) är en hanterad privat Docker-registerpost som lagrar och hanterar behållar avbildningar för Azure-distributioner i ett centralt register. Den är baserad på Docker-registret 2,0 med öppen källkod.

Om du är på Azure Security Center standard nivån kan du lägga till behållar register paketet. Den här valfria funktionen ger djupare insyn i säkerhets riskerna för avbildningarna i dina ARM-baserade register. Aktivera eller inaktivera paketet på prenumerations nivå för att ta med alla register i en prenumeration. Den här funktionen debiteras per avbildning, som visas på [sidan med priser](security-center-pricing.md). Genom att aktivera paket för behållar register ser du till att Security Center är redo att skanna bilder som skickas till registret. 

När en bild skickas till ditt register, Security Center skannar automatiskt avbildningen. Om du vill utlösa en genomsökning av en avbildning kan du skicka den till din lagrings plats.

När genomsökningen är klar (vanligt vis efter cirka 10 minuter, men kan ta upp till 48h), är avgöranden tillgängliga i Security Center rekommendationer som detta:

[![Exempel på Azure Security Center rekommendation om sårbarheter som identifierats i en Azure Container Registry (ACR) värd avbildning](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Fördelar med integrering

Security Center identifierar ARM-baserade ACR-register i din prenumeration och ger sömlös:

* **Azure – ursprunglig sårbarhets sökning** för alla publicerade Linux-avbildningar. Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys. Den här interna lösningen integreras sömlöst som standard.

* **Säkerhets rekommendationer** för Linux-avbildningar med kända sårbarheter. Security Center innehåller information om varje rapporterat sårbarhets-och allvarlighets GRADS klassificering. Dessutom ger den vägledning för hur du kan åtgärda de enskilda säkerhets risker som finns på varje avbildning som skickas till registret.

![Azure Security Center och Azure Container Registry (ACR) Översikt på hög nivå](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integration med Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Skydd av virtuella datorer](security-center-virtual-machine-protection.md) – beskriver Security Center rekommendationer
