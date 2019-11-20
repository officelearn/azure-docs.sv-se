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
ms.openlocfilehash: f2a07774fa23173738bc33907dd00017ca260fd9
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196384"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integration med Security Center (förhands granskning)

Azure Container Registry (ACR) är en hanterad privat Docker-registerpost som lagrar och hanterar behållar avbildningar för Azure-distributioner i ett centralt register. Den är baserad på Docker-registret 2,0 med öppen källkod.

För djupare insyn i ditt register och avbildnings sårbarheter kan användare av Azure Security Center standard nivån aktivera de valfria paketen för behållar register. Mer information finns i [prissättning](security-center-pricing.md). När paketet har Aktiver ATS genomsöker Security Center automatiskt bilder i din ACR-tjänst när en avbildning skickas till registret. När genomsökningen är klar (vanligt vis efter cirka 10 minuter), är avgöranden tillgängliga i Security Center i rekommendationer som detta:

[![exempel Azure Security Center rekommendationer om sårbarheter som identifierats i en Azure Container Registry (ACR) värd avbildning](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Fördelar med integrering

Security Center identifierar ACR-register i din prenumeration och erbjuder sömlöst:

* **Azure – ursprunglig sårbarhets sökning** för alla publicerade Linux-avbildningar. Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys. Den här interna lösningen integreras sömlöst som standard.

* **Säkerhets rekommendationer** för Linux-avbildningar med kända sårbarheter. Security Center innehåller information om varje rapporterat sårbarhets-och allvarlighets GRADS klassificering. Dessutom ger den vägledning för hur du kan åtgärda de enskilda säkerhets risker som finns på varje avbildning som skickas till registret.

![Azure Security Center och Azure Container Registry (ACR) Översikt på hög nivå](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integrering med Azure Kubernetes-tjänsten](azure-kubernetes-service-integration.md)

* [Skydd av virtuella datorer](security-center-virtual-machine-protection.md) – beskriver Security Center rekommendationer