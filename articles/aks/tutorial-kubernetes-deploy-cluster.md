---
title: "Självstudie om Kubernetes i Azure – Distribuera kluster"
description: "Självstudie om AKS – Distribuera kluster"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e0d5bd57a40fca837ead42e691e1fa0c802dc013
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Distribuera ett Azure Container Service-kluster (AKS)

Kubernetes tillhandahåller en distribuerad plattform för behållarbaserade program. Med AKS kan du snabbt och enkelt etablera ett produktionsklart Kubernetes-kluster. I del åtta av åtta i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Det här är några av stegen:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl

I efterföljande självstudier distribuerar du programmet Azure Vote till klustret, skalar ut det, uppdaterar det och konfigurerar Operations Management Suite för att övervaka Kubernetes-klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar][aks-tutorial-prepare-app].

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Aktivera förhandsversionen av AKS i din Azure-prenumeration
Så länge AKS tillhandahålls i förhandsversion måste du lägga till en funktionsflagga i prenumerationen för att få skapa nya kluster. Du kan begära den här funktionen i valfritt antal prenumerationer. Använd kommandot `az provider register` för att registrera AKS-providern:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Efter registreringen kan du skapa ett Kubernetes-kluster med AKS.

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster

I följande exempel skapas ett kluster med namnet `myAKSCluster` i en resursgrupp med namnet `myResourceGroup`. Den här resursgruppen skapades i [föregående självstudie][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Efter några minuter slutförs distributionen och json-formaterad information om AKS-distributionen returneras.

## <a name="install-the-kubectl-cli"></a>Installera CLI:t kubectl

När du ska ansluta till Kubernetes-klustret från klientdatorn använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure CloudShell är kubectl redan installerat. Kör följande kommando om du vill installera det lokalt:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Ansluta med kubectl

Du konfigurerar kubectl att ansluta till ditt Kubernetes-kluster genom att köra följande kommando:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

Kontrollera anslutningen till klustret med kommandot [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Resultat:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.7
```

När självstudien är utförd har du ett AKS-kluster som är redo för arbetsbelastningar. I senare självstudier distribuerar du ett program med flera behållare i det här klustret, skalar ut programmet, uppdaterar och övervakar det.

## <a name="next-steps"></a>Nästa steg

Ett Kubernetes-kluster har distribuerats i AKS i den här självstudien. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuerade ett Kubernetes AKS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl

Gå vidare till nästa självstudie om du vill lära dig om att köra programmet i klustret.

> [!div class="nextstepaction"]
> [Distribuera program i Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md