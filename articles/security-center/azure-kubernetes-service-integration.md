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
ms.openlocfilehash: 7f43f50fc35b5ecbc9720224036ea13e2ba753f5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894928"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrering med Azure Kubernetes Services med Security Center

Azure Kubernetes service (AKS) är Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare. 

Aktivera Azure **Defender för Kubernetes** för att få djupare insyn i dina AKS-noder, moln trafik och säkerhets kontroller.

Tillsammans, Security Center och AKS utgör det bästa molnet Kubernetes säkerhets erbjudande.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Vilka är komponenterna i Security Center Kubernetes Protection?

Security Centers skydd för Kubernetes tillhandahålls genom en kombination av två element:

- **Azure Security Center s skydd för virtuella datorer** – med samma Log Analytics-agent som Security Center använder på andra virtuella datorer kan Security Center Visa säkerhets problem som uppstår på dina AKS-noder. Agenten övervakar också för container-/regionsspecifika analyser.

- **Azure Security Center är valfri Azure Defender for Kubernetes-plan** – Kubernetes-planen tar emot loggar och information från Kubernetes-undersystemet via AKS-tjänsten. Dessa loggar är redan tillgängliga i Azure via AKS-tjänsten. När du aktiverar Azure Defender för Kubernetes beviljar du Security Center åtkomst till loggarna. Så Security Center ger säkerhets fördelarna till dina AKS-kluster med hjälp av data som redan har samlats in av AKS Master-noden. Några av de data som genomsöks av Azure Security Center från din Kubernetes-miljö kan innehålla känslig information.


## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integration med Azure Container Registry](azure-container-registry-integration.md)

* [Data hantering på Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – beskriver data principerna för Microsoft-tjänster (inklusive Azure, Intune och Microsoft 365), information om Microsofts data hantering och de bevarande principer som påverkar dina data
