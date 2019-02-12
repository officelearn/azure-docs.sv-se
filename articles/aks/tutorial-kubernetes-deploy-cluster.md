---
title: Självstudie om Kubernetes i Azure – Distribuera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) ska du skapa ett AKS-kluster och använda kubectl för att ansluta till Kubernetes-huvudnoden.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 54872a1c5a40cdb3f51c17362daed93c3892001e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754565"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Självstudier: Distribuera ett AKS-kluster (Azure Kubernetes Service)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Med AKS kan du snabbt skapa ett produktionsklart Kubernetes-kluster. I del tre av sju i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Lär dig att:

> [!div class="checklist"]
> * Skapa ett tjänsthuvudnamn för resursinteraktioner
> * Distribuera ett Kubernetes AKS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

I ytterligare självstudier distribueras Azure-appen Vote till klustret. Sedan skalas och uppdateras den.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har utfört de här stegen och vill följa med så kan du börja med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

För den här självstudien behöver du köra Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Du kan skapa ett huvudnamn för tjänsten automatiskt via Azure CLI eller portalen, eller så kan du skapa ett i förväg och tilldela ytterligare behörigheter. I den här självstudien skapar du ett huvudnamn för tjänsten, beviljar åtkomst till ACR-instansen (Azure Container Registry) du skapade i föregående självstudie och skapar sedan ett AKS-kluster.

Skapa ett huvudnamn för tjänsten med kommandot [az ad sp create-for-rbac][]. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas. Som standard är tjänstens huvudnamn giltigt i ett år.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Utdata ser ut ungefär så här:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anteckna värdena för *appId* och *password*. De här värdena används i senare steg.

## <a name="configure-acr-authentication"></a>Konfigurera ACR-autentisering

Om AKS-tjänstens huvudnamn ska kunna komma åt avbildningar som lagras i ACR måste du tilldela lämpliga behörigheter.

Börja med att hämta ACR-resurs-ID:t med [az acr show][]. Uppdatera `<acrName>`-registernamnet till namnet på ACR-instansen och resursgruppen där ACR-instansen finns.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

För att ge korrekt åtkomst så att AKS-klustret kan hämta avbildningar som lagras i ACR tilldelar du rollen `AcrPull` med hjälp av kommandot [az role assignment create][]. Byt ut `<appId`> och `<acrId>` mot värdena du antecknade i föregående två steg.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

AKS-kluster kan använda rollbaserad Kubernetes-åtkomstkontroll (RBAC). Med dessa kontroller kan du definiera åtkomst till resurser baserat på roller som är tilldelade till användare. Du kan kombinera behörigheter om en användare har tilldelats flera roller, och behörigheter kan begränsas till en enda namnrymd eller tillämpas på hela klustret. Som standard aktiverar Azure CLI automatiskt RBAC när du skapar ett AKS-kluster.

Skapa ett AKS-kluster med [az aks create][]. I följande exempel skapas ett kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Den här resursgruppen skapades i [föregående självstudie][aks-tutorial-prepare-acr]. Ange dina värden för `<appId>` och `<password>` från föregående steg där du skapade tjänstens huvudnamn.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Efter några minuter slutförs distributionen och JSON-formaterad information om AKS-distributionen returneras.

## <a name="install-the-kubernetes-cli"></a>Installera Kubernetes CLI

När du ska ansluta till Kubernetes-klustret från din lokala dator använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ansluta till klustret med kubectl

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifterna för AKS-klustret med namn *myAKSCluster* i *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kontrollera anslutningen till klustret med kommandot [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen distribuerade du ett Kubernetes-kluster i AKS och konfigurerade `kubectl` för anslutning till klustret. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett tjänsthuvudnamn för resursinteraktioner
> * Distribuera ett Kubernetes AKS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

Gå vidare till nästa självstudie och lär dig hur du distribuerar ett program i klustret.

> [!div class="nextstepaction"]
> [Distribuera program i Kubernetes][aks-tutorial-deploy-app]

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
