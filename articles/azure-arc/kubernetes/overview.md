---
title: Översikt över Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Den här artikeln innehåller en översikt över Azure Arc-aktiverade Kubernetes.
keywords: Kubernetes, båge, Azure, behållare
ms.custom: references_regions
ms.openlocfilehash: 61317f7f5f2bf17c88fc019294574993c1854e59
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540650"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Vad är Azure Arc Enabled Kubernetes Preview?

Du kan ansluta och konfigurera Kubernetes-kluster i eller utanför Azure med hjälp av Azure Arc Enabled Kubernetes Preview. När ett Kubernetes-kluster är kopplat till Azure-bågen visas det i Azure Portal. Det kommer att ha ett Azure Resource Manager-ID och en hanterad identitet. Kluster är anslutna till Azures standard prenumerationer, finns i en resurs grupp och kan ta emot Taggar precis som andra Azure-resurser. 

För att ansluta ett Kubernetes-kluster till Azure måste kluster administratören distribuera agenter. Dessa agenter körs i ett Kubernetes-namnområde med namnet `azure-arc` och är vanliga Kubernetes-distributioner. Agenterna ansvarar för anslutning till Azure, samlar in Azure Arc-loggar och-mått och tittar efter konfigurations begär Anden. 

Azure Arc Enabled Kubernetes har stöd för branschstandardiserade SSL för att skydda data under överföring. Dessutom lagras data som krypteras i vila i en Azure Cosmos DB databas för att säkerställa data sekretessen.
 
> [!NOTE]
> Azure Arc-aktiverade Kubernetes är en för hands version. Vi rekommenderar det inte för produktions arbets belastningar.

## <a name="supported-kubernetes-distributions"></a>Kubernetes-distributioner som stöds

Azure Arc-aktiverade Kubernetes fungerar med alla CNCF-certifierade (Cloud Native Computing Foundation) Kubernetes-kluster som AKS-motor på Azure, AKS-Engine på Azure Stack Hub, GKE, EKS och VMware vSphere-kluster.

Azure Arc-aktiverade Kubernetes-funktioner har testats av Arc-teamet på följande distributioner:
* RedHat OpenShift 4,3
* Rancher RKE 1.0.8
* Kanoniskt snabb Kubernetes 1,18
* AKS-motor
* AKS-motor på Azure Stack hubb
* Kluster-API-Provider Azure

## <a name="supported-scenarios"></a>Scenarier som stöds 

Azure Arc-aktiverade Kubernetes har stöd för följande scenarier: 

* Anslut Kubernetes som körs utanför Azure för inventering, gruppering och taggning.

* Distribuera program och tillämpa konfigurationen med hjälp av GitOps konfigurations hantering. 

* Använd Azure Monitor för behållare för att visa och övervaka dina kluster. 

* Tillämpa principer med Azure Policy för Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regioner som stöds 

Azure Arc-aktiverade Kubernetes stöds för närvarande i följande regioner: 

* East US 
* Europa, västra

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* Vad är skillnaden mellan Azure Arc-aktiverade Kubernetes och Azure Kubernetes service (AKS)?

    Azure Kubernetes service (AKS) är det hanterade Kubernetes-erbjudandet av Azure. AKS gör det enkelt att distribuera ett hanterat Kubernetes-kluster i Azure. AKS minskar komplexiteten och arbetet med att hantera Kubernetes genom att avlasta en stor del av det ansvaret till Azure. Kubernetes-huvudservrar hanteras av Azure. Du kan endast hantera och underhålla agentnoderna.

    Med Azure Arc-aktiverade Kubernetes kan du ansluta Kubernetes-kluster till Azure för att utöka Azures hanterings funktioner som Azure Monitor och Azure Policy. Underhåll av det underliggande Kubernetes-klustret görs av dig.

* Måste jag ansluta mina kluster för Azure Kubernetes-tjänsten som körs på Azure till Azure-bågen?

    Nej. Alla funktioner i Azure Arc Enabled Kubernetes, t. ex. Azure Monitor, Azure Policy (Gatekeeper) är internt tillgängliga med AKS, som redan har en resurs representation i Azure. Kluster konfiguration (GitOps) är också tillgängligt i AKS och för närvarande i privat för hands version. Använd det här [registrerings formuläret](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5acO18Lmx5Bk_qao2CrOcFUQ0UyRllDR1BEV1BPNENYRERYN1pFWTQ4WC4u) för att få åtkomst till den här funktionen.
    
* Bör jag ansluta mitt AKS-kluster på Azure Stack HCI till Azure-bågen? Vad händer om Kubernetes-kluster som körs på Azure Stack hubb eller Azure Stack motor?

    Ja, det finns fördelar med att ansluta dessa kluster till Azure-bågen. Det ger en resurs representation för dessa Kubernetes-kluster i Azure Resource Manager. Med den här resurs representationen kan funktioner som kluster konfiguration, Azure Monitor Azure Policy (Gatekeeper) utökas till dessa Kubernetes-kluster

## <a name="next-steps"></a>Nästa steg

* [Anslut ett kluster](./connect-cluster.md)
