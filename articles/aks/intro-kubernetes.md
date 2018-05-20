---
title: Introduktion till Azure Kubernetes Service
description: Med Azure Kubernetes Service kan du enkelt distribuera och hantera behållarbaserade program i Azure.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4936554465fbbed45000f43853a6a77567c3028f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="introduction-to-azure-kubernetes-service-aks-preview"></a>Introduktion till förhandsversionen av Azure Kubernetes Service (AKS)

Med Azure Kubernetes Service (AKS) kan du enkelt skapa, konfigurera och hantera ett kluster med virtuella datorer som är förkonfigurerade för att köra program i behållare. På så sätt kan du använda dina befintliga kunskaper eller använda en stor och växande mängd communityexpertis för att distribuera och hantera behållarbaserade program i Microsoft Azure.

Genom att använda AKS kan du dra nytta av Azures funktioner på företagsnivå samtidigt som programmen fortsätter att vara bärbara via Kubernetes och Docker-avbildningsformatet.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) finns för närvarande i **förhandsversion**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes i Azure

AKS minskar komplexiteten och arbetet med att hantera ett Kubernetes-kluster genom att avlasta en stor del av det ansvaret till Azure. Som Kubernetes-värdtjänst hanterar Azure viktiga uppgifter som övervakning av hälsotillstånd och underhåll åt dig. Dessutom betalar du bara för agentnoderna i klustren, inte för huvudservrarna. Som en hanterad Kubernetes-tjänst ger AKS:

> [!div class="checklist"]
> * Automatiska Kubernetes-versionsuppgraderingar och uppdateringar
> * Enkel klusterskalning
> * Värdbaserade kontrollplan med självåterställning (huvudservrar)
> * Kostnadsbesparingar – betala endast för agentpoolnoder som körs

När Azure sköter hanteringen av noderna i AKS-klustret behöver du inte längre utföra så många uppgifter manuellt, t.ex. klusteruppgraderingar. Eftersom Azure hanterar dessa viktiga underhållsuppgifter åt dig behöver inte AKS ge direktåtkomst (t.ex. med SSH) till klustret.

## <a name="using-azure-kubernetes-service-aks"></a>Använda Azure Kubernetes Service (AKS)
Målet med AKS är att tillhandahålla en behållarvärdmiljö med hjälp av verktyg och teknik med öppen källkod som är populära bland kunderna idag. Därför tillgängliggör vi Kubernetes API-standardslutpunkter. Genom att använda dessa standardslutpunkter kan du använda all programvara som kan kommunicera till ett Kubernetes-kluster. Du kan till exempel välja [kubectl][kubectl-overview], [helm][helm] eller [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-kubernetes-service-aks"></a>Skapa ett Kubernetes-kluster med Azure Kubernetes Service (AKS)
Om du vill börja använda AKS distribuerar du ett AKS-kluster med [Azure CLI][aks-quickstart] eller via portalen (sök efter **Azure Kubernetes Service** på Marketplace). Om du är en avancerad användare som behöver mer kontroll över Azure Resource Manager-mallarna kan du öppna projektet [acs-engine][acs-engine] med öppen källkod för att bygga ett eget anpassat Kubernetes-kluster och distribuera det via `az` CLI.

### <a name="using-kubernetes"></a>Använda Kubernetes
Kubernetes automatiserar distributionen, skalningen och hanteringen av program som använder behållare. Det har en omfattande uppsättning funktioner. Till exempel:
* Automatisk paketering
* Självåterställning
* Horisontell skalning
* Tjänstidentifiering och belastningsutjämning
* Automatiserade distributioner och återställningar
* Hemlighets- och konfigurationshantering
* Storage-dirigering
* Batch-körning

## <a name="videos"></a>Videoklipp

Azure Kubernetes Service (AKS) – Azure Friday, oktober 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Verktyg för att utveckla och distribuera program på Kubernetes – Azure OpenDev, juni 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Läs mer om att distribuera och hantera AKS med snabbstarten om AKS.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

