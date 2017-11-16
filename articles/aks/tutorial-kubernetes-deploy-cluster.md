---
title: "Kubernetes på Azure tutorial – distribuera klustret | Microsoft Docs"
description: "AKS tutorial – distribuera kluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 69dea4ab748d88d18cf01dc9b3fc1bdddd562681
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Distribuera ett kluster i Azure Container Service (AKS)

Kubernetes tillhandahåller en distribuerad plattform för behållarbaserade program. Med AKS är etablering av en klar Kubernetes produktionskluster snabbt och enkelt. I den här självstudiekursen avsnitt tre åtta distribueras ett Kubernetes kluster i AKS. Slutfört stegen innefattar:

> [!div class="checklist"]
> * Distribuera en Kubernetes AKS-kluster
> * Installation av Kubernetes CLI (kubectl)
> * Konfigurationen av kubectl

I efterföljande självstudiekurser Azure rösten programmet distribueras till klustret, skalas, uppdateras och Operations Management Suite är konfigurerad för att övervaka Kubernetes klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudiekurser en behållare avbildning skapades och överförs till en Azure-behållare registret-instans. Om du inte har gjort dessa steg och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./tutorial-kubernetes-prepare-app.md).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Aktivera AKS förhandsgranskning för din Azure-prenumeration
AKS är i förhandsvisning, måste skapa nya kluster flaggan funktionen för din prenumeration. Du kan begära den här funktionen för valfritt antal prenumerationer som du vill använda. Använd den `az provider register` kommando för att registrera providern AKS:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

När du har registrerat kan är du nu redo att skapa ett Kubernetes kluster med AKS.

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster

I följande exempel skapas ett kluster med namnet `myK8sCluster` i en resursgrupp med namnet `myResourceGroup`. Den här resursgruppen har skapats i den [tidigare kursen](./tutorial-kubernetes-prepare-acr.md).

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Distributionen är klar efter flera minuter, och returnerar json-formaterad information om AKS-distributionen.

## <a name="install-the-kubectl-cli"></a>Installera kubectl CLI

Om du vill ansluta till klustret Kubernetes från din klientdator [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes för klienten.

Om du använder Azure CloudShell är kubectl redan installerat. Om du vill installera det lokalt, kör du följande kommando:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Ansluta med kubectl

Om du vill konfigurera kubectl att ansluta till klustret Kubernetes, kör du följande kommando:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

För att verifiera anslutningen till klustret kör den [kubectl hämta noder](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) kommando.

```azurecli
kubectl get nodes
```

Resultat:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

I kursen slutförande har du ett AKS kluster redo för arbetsbelastningar. I efterföljande självstudiekurser har ett program för flera behållare distribuerats till det här klustret, skala ut, uppdateras och övervakas.

## <a name="next-steps"></a>Nästa steg

Ett Kubernetes kluster har distribuerats i AKS i den här självstudiekursen. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster
> * Installerat Kubernetes CLI (kubectl)
> * Konfigurerade kubectl

Gå vidare till nästa kurs vill veta mer om programmet körs i klustret.

> [!div class="nextstepaction"]
> [Distribuera program i Kubernetes](./tutorial-kubernetes-deploy-application.md)
