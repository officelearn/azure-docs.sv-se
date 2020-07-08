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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800193"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrering med Azure Kubernetes Services med Security Center

Azure Kubernetes service (AKS) är Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare. 

Om du använder Azure Security Center standard nivån kan du lägga till AKS-paketet (se [priser](security-center-pricing.md)) för att få djupare insyn i dina AKS-noder, moln trafik och säkerhets kontroller.

Tillsammans, Security Center och AKS utgör det bästa molnet Kubernetes säkerhets erbjudande.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Vilka är komponenterna i Security Center Kubernetes Protection?

Security Centers skydd för Kubernetes tillhandahålls genom en kombination av två element:

- **Azure Security Center s skydd för virtuella datorer** – med samma Log Analytics-agent som Security Center använder på andra virtuella datorer kan Security Center Visa säkerhets problem som uppstår på dina AKS-noder. Agenten övervakar också för container-/regionsspecifika analyser.

- **Azure Security Center det valfria Kubernetes-paketet** – Kubernetes-paketet tar emot loggar och information från Kubernetes-undersystemet via AKS-tjänsten. Dessa loggar är redan tillgängliga i Azure via AKS-tjänsten. När du aktiverar Security Center Kubernetes-paketet ger du Security Center åtkomst till loggarna. Så Security Center ger säkerhets fördelarna till dina AKS-kluster med hjälp av data som redan har samlats in av AKS Master-noden. Några av de data som genomsöks av Azure Security Center från din Kubernetes-miljö kan innehålla känslig information.

    ![Översikt över Azure Security Center och Azure Kubernetes service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Vilka skydd tillhandahålls?

Att använda de två tjänsterna tillsammans ger:

* **Säkerhets rekommendationer** – Security Center identifierar dina AKS-resurser och kategoriserar dem: från kluster till enskilda virtuella datorer. Du kan sedan Visa säkerhets rekommendationer per resurs. Mer information finns i rekommendationer för behållare i [referens listan för rekommendationer](recommendations-reference.md#recs-containers). 

* **Miljö härdning** – Security Center ständigt övervakar konfigurationen av dina Kubernetes-kluster och Docker-konfigurationer. Den genererar sedan säkerhets rekommendationer som återspeglar bransch standarder.

* **Kör tids skydd** – genom kontinuerlig analys av följande AKS-källor, Security Center varnar dig om hot och skadlig aktivitet som identifierats på värd- *och* AKS-klustret. [Läs mer om hot skydd för behållare](threat-protection.md#azure-containers).


     

![Azure Security Center och Azure Kubernetes service (AKS) i detalj](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS med Security Center vanliga frågor och svar

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan jag fortfarande få AKS-skydd utan Log Analytics-agenten?

Som nämnts ovan ger det valfria Kubernetes-paketet skydd på kluster nivå, Log Analytics agenten för Azure Security Center standard nivå skydda dina noder. 

Vi rekommenderar att du distribuerar båda, för det mest kompletta skyddet.

Om du väljer att inte installera agenten på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.



## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integration med Azure Container Registry](azure-container-registry-integration.md)

* [Data hantering på Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – beskriver data principerna för Microsoft-tjänster (inklusive Azure, Intune och Microsoft 365), information om Microsofts data hantering och de bevarande principer som påverkar dina data
