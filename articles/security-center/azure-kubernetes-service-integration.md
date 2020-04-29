---
title: Azure Security Center-och Azure Kubernetes-tjänsten
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
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436180"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrering med Azure Kubernetes Services med Security Center

Azure Kubernetes service (AKS) är Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare. 

Använd AKS tillsammans med Azure Security Center standard nivån (se [prissättning](security-center-pricing.md)) för att få djupare insyn i dina AKS-noder, moln trafik och säkerhets kontroller.

Security Center ger säkerhets förmåner till dina AKS-kluster med hjälp av data som redan har samlats in av AKS Master-noden. 

![Översikt över Azure Security Center och Azure Kubernetes service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Tillsammans utgör dessa två verktyg det bästa molnets inbyggda Kubernetes säkerhets erbjudande. 

## <a name="benefits-of-integration"></a>Fördelar med integrering

Att använda de två tjänsterna tillsammans ger:

* **Säkerhets rekommendationer** – Security Center identifierar dina AKS-resurser och kategoriserar dem: från kluster till enskilda virtuella datorer. Du kan sedan Visa säkerhets rekommendationer per resurs. Mer information finns i rekommendationer för behållare i [referens listan för rekommendationer](recommendations-reference.md#recs-containers). 

* **Miljö härdning** – Security Center ständigt övervakar konfigurationen av dina Kubernetes-kluster och Docker-konfigurationer. Den genererar sedan säkerhets rekommendationer som återspeglar bransch standarder.

* **Körnings skydd** – genom kontinuerlig analys av följande AKS-källor, Security Center varnar dig om hot och skadlig aktivitet som identifieras på värd- *och* AKS-kluster nivån:
    * Rå säkerhets händelser, t. ex. nätverks data och process skapande
    * Gransknings loggen för Kubernetes

    Mer information finns i [hot Protection for Azure containers](threat-protection.md#azure-containers)

    En lista över möjliga aviseringar finns i följande avsnitt i referens tabellen för aviseringar: [AKS på kluster nivå varningar](alerts-reference.md#alerts-akscluster) och [aviseringar på värdnivå för behållare](alerts-reference.md#alerts-containerhost).  

![Azure Security Center och Azure Kubernetes service (AKS) i detalj](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Några av de data som genomsöks av Azure Security Center från din Kubernetes-miljö kan innehålla känslig information.


## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integration med Azure Container Registry](azure-container-registry-integration.md)

* [Data hantering på Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – beskriver data principerna för Microsoft-tjänster (inklusive Azure, Intune och Office 365), information om Microsofts data hantering och de bevarande principer som påverkar dina data
