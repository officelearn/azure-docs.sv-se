---
title: "Introduktion till Azure Container Service för Kubernetes | Microsoft Docs"
description: "Med Azure Container Service för Kubernetes kan du enkelt distribuera och hantera behållarbaserade program i Azure."
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Kubernetes, Docker, Containers, Microservices, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: a8ac18464d0efcc0db96e1667f18f2f853208573
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Introduktion till Azure Container Service (AKS)

Azure Container Service (AKS) gör det enkelt att skapa, konfigurera och hantera kluster för virtuella datorer som är förkonfigurerad för körning av program. På så sätt kan du använda dina befintliga kunskaper eller använda en stor och växande mängd communityexpertis för att distribuera och hantera behållarbaserade program i Microsoft Azure.

Genom att använda AKS kan dra du nytta av företagsklass funktioner i Azure, men har ändå programmet överföring via Kubernetes och Docker-bildformat.

## <a name="using-azure-container-service-aks"></a>Med Azure Container Service (AKS)
Vårt mål med AKS är att tillhandahålla en behållare värdmiljön med öppen källkod verktyg och tekniker som är populärt bland våra kunder idag. Därför tillgängliggör vi Kubernetes API-standardslutpunkter. Genom att använda dessa standardslutpunkter kan du använda all programvara som kan kommunicera till ett Kubernetes-kluster. Du kan till exempel välja [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) eller [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Skapar ett Kubernetes-kluster med Azure Container Service (AKS)
Om du vill börja använda AKS måste du distribuera ett AKS-kluster med den [Azure CLI](./kubernetes-walkthrough.md) eller via portalen (Sök Marketplace för **Azure Container Service**). Om du är en avancerad användare som behöver mer kontroll över Azure Resource Manager-mallarna kan du öppna projektet [acs-engine](https://github.com/Azure/acs-engine) med öppen källkod för att bygga ett eget anpassat Kubernetes-kluster och distribuera det via `az` CLI.

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

Azure Container Service (AKS) - Azure fredag oktober 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Verktyg för att utveckla och distribuera program på Kubernetes - Azure OpenDev juni 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Mer information om att distribuera och hantera AKS med AKS Snabbstart.

> [!div class="nextstepaction"]
> [AKS självstudiekursen](./kubernetes-walkthrough.md)