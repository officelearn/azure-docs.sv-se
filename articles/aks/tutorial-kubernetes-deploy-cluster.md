---
title: Självstudie om Kubernetes i Azure – Distribuera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) ska du skapa ett AKS-kluster och använda kubectl för att ansluta till Kubernetes-huvudnoden.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9f06aa44824c28369b331d4079e9e81417bf17c7
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263852"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Självstudie: Distribuera ett Azure Kubernetes Service-kluster (AKS)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Med AKS kan du snabbt skapa ett produktionsklart Kubernetes-kluster. I del tre av sju i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Lär dig att:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster som kan autentisera till ett Azure Container Registry
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

I ytterligare självstudier distribueras Azure-appen Vote till klustret. Sedan skalas och uppdateras den.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har utfört dessa steg och vill följa med, börjar du med [självstudier 1 – Skapa behållar avbildningar][aks-tutorial-prepare-app].

I den här självstudien måste du köra Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

AKS-kluster kan använda rollbaserad Kubernetes-åtkomstkontroll (RBAC). Med dessa kontroller kan du definiera åtkomst till resurser baserat på roller som är tilldelade till användare. Du kan kombinera behörigheter om en användare har tilldelats flera roller, och behörigheter kan begränsas till en enda namnrymd eller tillämpas på hela klustret. Som standard aktiverar Azure CLI automatiskt RBAC när du skapar ett AKS-kluster.

Skapa ett AKS-kluster med [az aks create][]. I följande exempel skapas ett kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Den här resurs gruppen skapades i [föregående självstudie][aks-tutorial-prepare-acr]. För att ett AKS-kluster ska kunna interagera med andra Azure-resurser skapas ett Azure Active Directory tjänstens huvud namn automatiskt, eftersom du inte angav något. Här ges tjänstens huvud namn [rätt att hämta avbildningar][container-registry-integration] från den Azure Container Registry-instans (ACR) som du skapade i föregående självstudie.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Efter några minuter slutförs distributionen och JSON-formaterad information om AKS-distributionen returneras.

> [!NOTE]
> För att klustret ska fungera på ett tillförlitligt sätt bör du köra minst två noder.

## <a name="install-the-kubernetes-cli"></a>Installera Kubernetes CLI

Om du vill ansluta till Kubernetes-klustret från den lokala datorn använder du [kubectl][kubectl], Kubernetes kommando rads klient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ansluta till klustret med kubectl

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifterna för AKS-klustret med namn *myAKSCluster* i *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Om du vill kontrol lera anslutningen till klustret kör du kommandot [kubectl get Nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen distribuerade du ett Kubernetes-kluster i AKS och konfigurerade `kubectl` för anslutning till klustret. Du har lärt dig att:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster som kan autentisera till ett Azure Container Registry
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

Gå vidare till nästa självstudie och lär dig hur du distribuerar ett program i klustret.

> [!div class="nextstepaction"]
> [Distribuera programmet i Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
