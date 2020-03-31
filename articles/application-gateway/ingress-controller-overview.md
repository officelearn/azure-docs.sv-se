---
title: Vad är Azure Application Gateway Ingress Controller?
description: Den här artikeln innehåller en introduktion till vad Application Gateway Ingress Controller är.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335807"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Vad är Application Gateway Ingress Controller?
Application Gateway Ingress Controller (AGIC) är ett Kubernetes-program, vilket gör det möjligt för [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) kunder att utnyttja Azures inbyggda [Program Gateway](https://azure.microsoft.com/services/application-gateway/) L7-belastningsutjämnare för att exponera molnprogramvara till Internet. AGIC övervakar Kubernetes-klustret som det finns på och uppdaterar kontinuerligt en Programgateway, så att valda tjänster exponeras för Internet.

Ingress Controller körs i sin egen pod på kundens AKS. AGIC övervakar en delmängd av Kubernetes-resurser för ändringar. Tillståndet för AKS-klustret översätts till specifik konfiguration av Application Gateway och tillämpas på [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Fördelar med Ingress-styrenhet för ingående programgateway
AGIC gör att distributionen kan styra flera AKS-kluster med en enda Application Gateway Ingress Controller. AGIC hjälper också till att eliminera behovet av att ha en annan belastningsutjämnare/offentlig IP framför AKS-klustret och undviker flera hopp i din datapat innan begäranden når AKS-klustret. Application Gateway talar med poddar med hjälp av sin privata IP direkt och kräver inte NodePort eller KubeProxy tjänster. Detta ger också bättre prestanda för dina distributioner.

Ingress Controller stöds uteslutande av Standard_v2 och WAF_v2 SKU: er, vilket också ger dig fördelar för automatisk skalning. Application Gateway kan reagera som svar på en ökning eller minskning av trafikbelastning och skala därefter, utan att förbruka några resurser från AKS-klustret.

Genom att använda Application Gateway utöver AGIC kan du också skydda AKS-klustret genom att tillhandahålla WAF-funktioner (TLS policy and Web Application Firewall).

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC konfigureras via Kubernetes [Ingress-resurs](https://kubernetes.io/docs/user-guide/ingress/), tillsammans med Service och distributioner/Pods. Den innehåller ett antal funktioner som utnyttjar Azures inbyggda programgateway L7-belastningsutjämnare. För att nämna några:
  - URL-routning
  - Cookie-baserad tillhörighet
  - TLS-avslutning
  - på slutna TLS
  - Stöd för offentliga, privata och hybridwebbplatser
  - Integrerad brandvägg för webbprogram

AGIC kan hantera flera namnområden och har Förbjudnatar, vilket innebär att AGIC kan konfigurera Application Gateway specifikt för AKS-kluster utan att påverka andra befintliga serverdelar. 

## <a name="next-steps"></a>Efterföljande moment

- [**Greenfield Deployment**](ingress-controller-install-new.md): Instruktioner om hur du installerar AGIC, AKS och Application Gateway på tom-skiffer infrastruktur.
- [**Brownfield Deployment**](ingress-controller-install-existing.md): Installera AGIC på en befintlig AKS och Application Gateway.

