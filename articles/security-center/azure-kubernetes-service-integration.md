---
title: Azure Security Center-och Azure Kubernetes-tjänsten | Microsoft Docs
description: Lär dig mer om Azure Security Center s integrering med Azure Kubernetes Services
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
ms.openlocfilehash: 4fc0654aab1c68888e86dc95de658bc69a01e02d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123976"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integrering med Azure Kubernetes Services med Security Center (för hands version)
Azure Kubernetes service (AKS) är Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare. 

Använd AKS tillsammans med Azure Security Center standard nivån (se [prissättning](security-center-pricing.md)) för att få djupare insyn i dina AKS-noder, moln trafik och säkerhets kontroller.

Security Center ger säkerhets förmåner till dina AKS-kluster med hjälp av data som redan har samlats in av AKS Master-noden. 

![Översikt över Azure Security Center och Azure Kubernetes service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Tillsammans utgör dessa två verktyg det bästa molnets inbyggda Kubernetes säkerhets erbjudande. 

## <a name="benefits-of-integration"></a>Fördelar med integrering

Att använda de två tjänsterna tillsammans ger:

* **Säkerhets rekommendationer** – Security Center identifierar dina AKS-resurser och kategoriserar dem: från kluster till enskilda virtuella datorer. Du kan sedan Visa säkerhets rekommendationer per resurs. Mer information finns i [så här implementerar du säkerhets rekommendationer](security-center-recommendations.md). 

    > [!NOTE]
    > Om namnet på en Security Center rekommendation slutar med en "(för hands version)"-tagg, är det hänvisning till rekommendationens förhands gransknings typ. inte funktionen.

* **Miljö härdning** – Security Center ständigt övervakar konfigurationen av dina Kubernetes-kluster och genererar säkerhets rekommendationer som återspeglar bransch standarder.

* **Körnings skydd** – genom kontinuerlig analys av följande AKS-källor, Security Center varnar dig om hot och skadlig aktivitet som identifieras på värd- *och* AKS-kluster nivå (mer information finns i [Azure Container Service ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * Rå säkerhets händelser, t. ex. nätverks data och process skapande
    * Gransknings loggen för Kubernetes

![Azure Security Center och Azure Kubernetes service (AKS) i detalj](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Några av de data som genomsöks av Azure Security Center från din Kubernetes-miljö kan innehålla känslig information.

## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integrering med Azure Container Registry](azure-container-registry-integration.md)

* [Skydd av virtuella datorer](security-center-virtual-machine-protection.md) – beskriver Security Center rekommendationer

* [Data hantering på Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – beskriver data principerna för Microsoft-tjänster (inklusive Azure, Intune och Office 365), information om Microsofts data hantering och de bevarande principer som påverkar dina data