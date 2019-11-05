---
title: Azure Security Center och Azure Container Registry | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521870"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integration med Security Center (förhands granskning)

Azure Container Registry (ACR) är en hanterad privat Docker-registerpost som lagrar och hanterar behållar avbildningar för Azure-distributioner i ett centralt register. Den är baserad på Docker-registret 2,0 med öppen källkod.

När du använder ACR tillsammans med Azure Security Center standard nivån (se [prissättning](security-center-pricing.md)) får du djupare insyn i registret och avbildningens sårbarheter.

[![Azure Container Registry (ACR) rekommendationer i Azure Security Center](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

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