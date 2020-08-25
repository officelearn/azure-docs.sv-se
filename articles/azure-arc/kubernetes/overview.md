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
ms.openlocfilehash: 885c96b83edb83cfb62fc117d9b4406792827056
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723732"
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

## <a name="supported-regions"></a>Regioner som stöds 

Azure Arc-aktiverade Kubernetes stöds för närvarande i följande regioner: 

* East US 
* Europa, västra

## <a name="next-steps"></a>Nästa steg

* [Anslut ett kluster](./connect-cluster.md)
