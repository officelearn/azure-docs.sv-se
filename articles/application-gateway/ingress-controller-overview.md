---
title: Vad är Azure Application Gateway ingress ingångs kontroll?
description: Den här artikeln innehåller en introduktion till Application Gateway ingångs kontroll av kontrollanten.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335807"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Vad är Application Gateway ingress-styrenhet?
Application Gateway ingress (AGIC) är ett Kubernetes-program som gör det möjligt för [Azure Kubernetes service-kunder (AKS)](https://azure.microsoft.com/services/kubernetes-service/) att utnyttja Azures inbyggda [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7-belastningsutjämnare för att exponera moln program vara till Internet. AGIC övervakar det Kubernetes-kluster som det finns på och uppdaterar kontinuerligt en Application Gateway, så att valda tjänster exponeras för Internet.

Ingångs styrenheten körs i sin egen Pod på kundens AKS. AGIC övervakar en delmängd av Kubernetes-resurser för ändringar. Status för AKS-klustret översätts till Application Gateway speciell konfiguration och tillämpas på [Azure Resource Manager (arm)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Fördelar med Application Gateway ingress-kontrollant
AGIC gör att distributionen kan styra flera AKS-kluster med en enda Application Gateway ingångs kontroll. AGIC bidrar också till att eliminera behovet av att ha en annan belastningsutjämnare/offentlig IP framför AKS-kluster och undvika flera hopp i din Datapath innan begär Anden når AKS-klustret. Application Gateway pratar med poddar med sin privata IP-adress direkt och kräver inte NodePort-eller KubeProxy-tjänster. Detta ger också bättre prestanda för dina distributioner.

Ingångs kontroll stöds exklusivt av Standard_v2 och WAF_v2 SKU: er som också ger dig automatiska skalnings förmåner. Application Gateway kan reagera som svar på en ökning eller minskning av trafikbelastningen och skala därefter, utan att behöva använda några resurser från ditt AKS-kluster.

Genom att använda Application Gateway utöver AGIC bidrar också till att skydda ditt AKS-kluster genom att tillhandahålla TLS-princip och WAF-funktioner (Web Application Firewall).

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC konfigureras via Kubernetes [ingress-resursen](https://kubernetes.io/docs/user-guide/ingress/), tillsammans med tjänsten och distributioner/poddar. Det innehåller ett antal funktioner som använder Azures inbyggda Application Gateway L7-belastningsutjämnare. Så här namnger du några:
  - URL-routning
  - Cookie-baserad tillhörighet
  - TLS-terminering
  - Slut punkt till slut punkt – TLS
  - Stöd för offentliga, privata och hybrid webbplatser
  - Integrerad brand vägg för webbaserade program

AGIC kan hantera flera namn rymder och har ProhibitedTargets, vilket innebär att AGIC kan konfigurera Application Gateway specifikt för AKS-kluster utan att påverka andra befintliga Server delar. 

## <a name="next-steps"></a>Nästa steg

- [**Bygg-distribution**](ingress-controller-install-new.md): instruktioner om hur du installerar AGIC, AKS och Application Gateway på en tom infrastruktur.
- [**Brownfield-distribution**](ingress-controller-install-existing.md): installera AGIC på en befintlig AKS och Application Gateway.

