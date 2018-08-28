---
title: Självstudie om Kubernetes i Azure – Distribuera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) ska du skapa ett AKS-kluster och använda kubectl för att ansluta till Kubernetes-huvudnoden.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 80b011f9df389098095f58c02008da891b2aa8a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920378"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Självstudie: Distribuera ett Azure Kubernetes Service-kluster (AKS)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Med AKS kan du snabbt etablera ett produktionsklart Kubernetes-kluster. I del tre av sju i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Lär dig att:

> [!div class="checklist"]
> * Skapa ett tjänsthuvudnamn för resursinteraktioner
> * Distribuera ett Kubernetes AKS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

I senare självstudier distribueras Azure-appen Vote till klustret. Sedan skalas och uppdateras den.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

I den här självstudien måste du köra Azure CLI version 2.0.44 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Du kan skapa ett huvudnamn för tjänsten automatiskt via Azure CLI eller portalen, eller så kan du skapa ett i förväg och tilldela ytterligare behörigheter. I den här självstudien skapar du ett huvudnamn för tjänsten, beviljar åtkomst till ACR-instansen (Azure Container Registry) du skapade i föregående självstudie och skapar sedan ett AKS-kluster.

Skapa ett huvudnamn för tjänsten med kommandot [az ad sp create-for-rbac][]. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas.

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

Skapa en rolltilldelning med kommandot [az role assignment create][] så att AKS-klustret kan använda avbildningar som lagras i ACR. Byt ut `<appId`> och `<acrId>` mot värdena du antecknade i föregående två steg.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role Reader
```

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

AKS-kluster kan använda rollbaserad Kubernetes-åtkomstkontroll (RBAC). Med dessa kontroller kan du definiera åtkomst till resurser baserat på roller som är tilldelade till användare. Du kan kombinera behörigheter om en användare har tilldelats flera roller, och behörigheter kan begränsas till en enda namnrymd eller tillämpas på hela klustret. Rollbaserad Kubernetes-åtkomstkontroll är för närvarande tillgängligt som en förhandsversion för AKS-kluster. Som standard aktiverar Azure CLI automatiskt RBAC när du skapar ett AKS-kluster.

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

Du konfigurerar `kubectl` för anslutning till Kubernetes-klustret med kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifterna för AKS-klustret med namn *myAKSCluster* i *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kontrollera anslutningen till klustret med kommandot [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.9
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
