---
title: Azure Security Center och Azure Kubernetes-tjänsten
description: Lär dig mer om Azure Security Centers integrering med Azure Kubernetes Services
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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436180"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Services-integrering med Security Center

Azure Kubernetes Service (AKS) är Microsofts hanterade tjänst för att utveckla, distribuera och hantera behållarprogram. 

Använd AKS tillsammans med Azure Security Centers standardnivå (se [priser)](security-center-pricing.md)för att få djupare synlighet för dina AKS-noder, molntrafik och säkerhetskontroller.

Security Center ger säkerhetsfördelar för DINA AKS-kluster med hjälp av data som redan samlats in av AKS-huvudnoden. 

![Översikt över Azure Security Center och Azure Kubernetes Service (AKS) på hög nivå](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Tillsammans utgör dessa två verktyg det bästa molnbaserade Kubernetes-säkerhetserbjudandet. 

## <a name="benefits-of-integration"></a>Fördelar med integration

Genom att använda de två tjänsterna tillsammans finns följande:

* **Säkerhetsrekommendationer** - Security Center identifierar dina AKS-resurser och kategoriserar dem: från kluster till enskilda virtuella datorer. Du kan sedan visa säkerhetsrekommendationer per resurs. Mer information finns i behållarrekommendationerna i [referenslistan med rekommendationer](recommendations-reference.md#recs-containers). 

* **Miljöhärdning** - Security Center övervakar ständigt konfigurationen av kubernetes-kluster och Docker-konfigurationer. Den genererar sedan säkerhetsrekommendationer som återspeglar branschstandarder.

* **Körningsskydd** – Genom kontinuerlig analys av följande AKS-källor varnar Security Center dig för hot och skadlig aktivitet som upptäckts på *värd- och* AKS-klusternivå:
    * Råa säkerhetshändelser, till exempel nätverksdata och skapande av processer
    * Kubernetes granskningslogg

    Mer information finns i [hotskydd för Azure-behållare](threat-protection.md#azure-containers)

    En lista över möjliga aviseringar finns i dessa avsnitt i referenstabellen för aviseringar: [AKS-klusternivåaviseringar](alerts-reference.md#alerts-akscluster) och [varningar på behållarnivå](alerts-reference.md#alerts-containerhost).  

![Azure Security Center och Azure Kubernetes Service (AKS) mer i detalj](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Vissa av de data som skannas av Azure Security Center från kubernetes-miljön kan innehålla känslig information.


## <a name="next-steps"></a>Nästa steg

Mer information om säkerhetsfunktionerna i Security Center finns i:

* [Azure Security Center och containersäkerhet](container-security.md)

* [Integration med Azure Container Registry](azure-container-registry-integration.md)

* [Datahantering på Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – Beskriver dataprinciperna för Microsoft-tjänster (inklusive Azure, Intune och Office 365), information om Microsofts datahantering och lagringsprinciper som påverkar dina data
