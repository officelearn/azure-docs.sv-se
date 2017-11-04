---
title: "Självstudiekurs för Azure Container Service - distribuera klustret | Microsoft Docs"
description: "Självstudiekurs för Azure Container Service - distribuera kluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dcced32925ecd109be8dde6324cca4899923679b
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Distribuera ett Kubernetes kluster i Azure Container Service

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Kubernetes tillhandahåller en distribuerad plattform för behållarbaserade program. Med Azure Container Service är etablering av en klar Kubernetes produktionskluster snabbt och enkelt. I den här självstudiekursen, del 3 i 7, distribueras ett Azure Container Service Kubernetes klustret. Slutfört stegen innefattar:

> [!div class="checklist"]
> * Distribuera en Kubernetes ACS-kluster
> * Installation av Kubernetes CLI (kubectl)
> * Konfigurationen av kubectl

I efterföljande självstudiekurser Azure rösten programmet distribueras till klustret, skalas, uppdateras och Operations Management Suite är konfigurerad för att övervaka Kubernetes klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudiekurser en behållare avbildning skapades och överförs till en Azure-behållare registret-instans. Om du inte har gjort dessa steg och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster

Skapa ett Kubernetes-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#create). 

I följande exempel skapas ett kluster med namnet `myK8sCluster` i en resursgrupp med namnet `myResourceGroup`. Den här resursgruppen har skapats i den [tidigare kursen](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

I vissa fall, som vid en begränsad utvärderingsversion, har en Azure-prenumeration begränsad åtkomst till Azure-resurser. Om distributionen misslyckas på grund av begränsade tillgängliga kärnor minskar du antalet standardagenter genom att lägga till `--agent-count 1` till kommandot [az acs create](/cli/azure/acs#create). 

Distributionen har slutförts efter flera minuter, och returnerar json-formaterad information om ACS-distribution.

## <a name="install-the-kubectl-cli"></a>Installera kubectl CLI

Om du vill ansluta till klustret Kubernetes från din klientdator [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes för klienten. 

Om du använder Azure CloudShell är kubectl redan installerat. Om du vill installera det lokalt kan använda den [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) kommando.

Om körs i Linux eller macOS, kan du behöva köra med sudo. Windows, se till att gränssnittet har körts som administratör.

```azurecli-interactive 
az acs kubernetes install-cli 
```

På Windows, standardinstallation är *c:\program files (x86)\kubectl.exe*. Du kan behöva lägga till den här filen i Windows-sökvägen. 

## <a name="connect-with-kubectl"></a>Ansluta med kubectl

Du konfigurerar kubectl att ansluta till ditt Kubernetes-kluster genom att köra kommandot [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

För att verifiera anslutningen till klustret kör den [kubectl hämta noder](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) kommando.

```azurecli-interactive
kubectl get nodes
```

Resultat:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

I kursen slutförande har du ett Kubernetes ACS-kluster redo för arbetsbelastningar. I efterföljande självstudiekurser har ett program för flera behållare distribuerats till det här klustret, skala ut, uppdateras och övervakas.

## <a name="next-steps"></a>Nästa steg

Ett Azure Container Service Kubernetes kluster har distribuerats i den här självstudiekursen. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuera ett Kubernetes ACS-kluster
> * Installerat Kubernetes CLI (kubectl)
> * Konfigurerade kubectl

Gå vidare till nästa kurs vill veta mer om programmet körs i klustret.

> [!div class="nextstepaction"]
> [Distribuera program i Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
