---
title: Självstudie om Kubernetes i Azure – Distribuera kluster
description: Självstudie om AKS – Distribuera kluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341407"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Självstudie: Distribuera ett Azure Kubernetes Service-kluster (AKS)

Kubernetes tillhandahåller en distribuerad plattform för behållarbaserade program. Med AKS kan du snabbt etablera ett produktionsklart Kubernetes-kluster. I del tre av sju i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Det här är några av stegen:

> [!div class="checklist"]
> * Skapa ett tjänsthuvudnamn för interaktioner med resurser
> * Distribuera ett Kubernetes AKS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl

I senare självstudier distribueras Azure-appen Vote till klustret. Sedan skalas och uppdateras den.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar][aks-tutorial-prepare-app].

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Du kan skapa ett huvudnamn för tjänsten automatiskt via Azure CLI eller portalen, eller så kan du skapa ett i förväg och tilldela ytterligare behörigheter. I den här självstudien skapar du ett huvudnamn för tjänsten, beviljar åtkomst till ACR-instansen (Azure Container Registry) du skapade i föregående självstudie och skapar sedan ett AKS-kluster.

Skapa ett huvudnamn för tjänsten med [az ad sp create-for-rbac][]. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas.

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

Hämta först ID:t för ACR-resursen med [az acr show][]. Uppdatera `<acrName>`-registernamnet till namnet på ACR-instansen och resursgruppen där ACR-instansen finns.

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

Skapa en rolltilldelning med [az role assignment create][] så att AKS-klustret kan använda avbildningar som lagras i ACR. Byt ut `<appId`> och `<acrId>` mot värdena du antecknade i föregående två steg.

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster

Skapa nu ett AKS-kluster med [az aks create][]. I följande exempel skapas ett kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Den här resursgruppen skapades i [föregående självstudie][aks-tutorial-prepare-acr]. Ange dina värden för `<appId>` och `<password>` från föregående steg där du skapade tjänstens huvudnamn.

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Efter några minuter slutförs distributionen och JSON-formaterad information om AKS-distributionen returneras.

## <a name="install-the-kubectl-cli"></a>Installera CLI:t kubectl

När du ska ansluta till Kubernetes-klustret från klientdatorn använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är kubectl redan installerat. Du kan även installera det lokalt med [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Ansluta med kubectl

Du konfigurerar kubectl för anslutning till Kubernetes-klustret med kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifterna för AKS-klustret med namn *myAKSCluster* i *myResourceGroup*:

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

Kontrollera anslutningen till klustret med kommandot [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Resultat:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>Nästa steg

Ett Kubernetes-kluster har distribuerats i AKS i den här självstudien. Följande steg har slutförts:

> [!div class="checklist"]
> * Skapade ett tjänsthuvudnamn för interaktioner med resurser
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
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
