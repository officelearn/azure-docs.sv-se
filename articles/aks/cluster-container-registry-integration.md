---
title: Integrera Azure Container Registry med Azure Kubernetes-tjänsten
description: Lär dig hur du integrerar Azure Kubernetes service (AKS) med Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 3c11367945b74db9be20ade86c7bc26901440e4d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305150"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Förhands granskning – autentisera med Azure Container Registry från Azure Kubernetes-tjänsten

När du använder Azure Container Registry (ACR) med Azure Kubernetes service (AKS) måste du upprätta en autentiseringsmekanism. Den här artikeln beskriver de rekommenderade konfigurationerna för autentisering mellan dessa två Azure-tjänster.

Du kan ställa in AKS till ACR-integrering i några enkla kommandon med Azure CLI.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS](support-policies.md)
> * [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande:

* Rollen **ägare** eller **administratör för Azure-konto** på Azure- **prenumerationen**
* Du behöver också Azure CLI-version 2.0.70 eller senare och AKS 0.4.8-tillägget för-Preview
* Du måste ha [Docker installerat](https://docs.docker.com/install/) på klienten och du behöver åtkomst till [Docker Hub](https://hub.docker.com/)

## <a name="install-latest-aks-cli-preview-extension"></a>Installera det senaste AKS CLI Preview-tillägget

Du behöver tillägget **AKS-Preview 0.4.13** eller senare.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Skapa ett nytt AKS-kluster med ACR-integrering

Du kan ställa in AKS-och ACR-integration när du skapar ditt AKS-kluster.  För att tillåta ett AKS-kluster att samverka med ACR används ett Azure Active Directory **tjänstens huvud namn** . Med följande CLI-kommando kan du auktorisera en befintlig ACR i din prenumeration och konfigurera rätt **ACRPull** -roll för tjänstens huvud namn. Ange giltiga värden för parametrarna nedan.  Parametrarna inom hak paren tes är valfria.
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
\* * Ett ACR resurs-ID har följande format: 

/Subscriptions/< prenumeration-d >/resourceGroups/< resurs-grupp-Name >/providers/Microsoft.ContainerRegistry/registries/{name} 
  
Det här steget kan ta flera minuter att slutföra.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurera ACR-integrering för befintliga AKS-kluster

Integrera en befintlig ACR med befintliga AKS-kluster genom att ange giltiga värden för **ACR-Name** eller **ACR-Resource-ID** enligt nedan.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Du kan också ta bort integrationen mellan ett ACR och ett AKS-kluster med följande
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>Logga in på din ACR

Använd följande kommando för att logga in på din ACR.  <acrname> Ersätt parametern med ditt ACR-namn.  Standardvärdet är till exempel **aks < resurs gruppen > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Hämta en avbildning från Docker Hub och push-överför till din ACR

Hämta en avbildning från Docker Hub, tagga avbildningen och skicka till din ACR.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Uppdatera tillstånd och verifiera poddar

Utför följande steg för att verifiera distributionen.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Visa yaml-filen och redigera bild egenskapen genom att ersätta värdet med din ACR-inloggnings Server, bild och tagg.

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
