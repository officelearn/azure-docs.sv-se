---
title: Integrera Azure-behållarregister med Azure Kubernetes-tjänsten
description: Lär dig hur du integrerar Azure Kubernetes Service (AKS) med Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617412"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autentisera med Azure Container Registry från Azure Kubernetes Service

När du använder Azure Container Registry (ACR) med Azure Kubernetes Service (AKS) måste en autentiseringsmekanism upprättas. Den här artikeln innehåller exempel för att konfigurera autentisering mellan dessa två Azure-tjänster. 

Du kan ställa in AKS till ACR-integrering i några enkla kommandon med Azure CLI. Den här integreringen tilldelar rollen AcrPull till tjänsthuvudhuvudet som är associerat till AKS-klustret.

## <a name="before-you-begin"></a>Innan du börjar

Dessa exempel kräver:

* **Ägar-** eller **Azure-kontoadministratörsrollen** i **Azure-prenumerationen**
* Azure CLI version 2.0.73 eller senare

Om du vill undvika att behöva en **administratörsroll för ägare** eller **Azure-konto** kan du konfigurera ett tjänsthuvudnamn manuellt eller använda ett befintligt tjänsthuvudnamn för att autentisera ACR från AKS. Mer information finns i [ACR-autentisering med tjänsthuvudnamn](../container-registry/container-registry-auth-service-principal.md) eller [Autentisera från Kubernetes med pull-hemlighet](../container-registry/container-registry-auth-kubernetes.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Skapa ett nytt AKS-kluster med ACR-integrering

Du kan ställa in AKS- och ACR-integrering under det första skapandet av AKS-klustret.  För att ett AKS-kluster ska kunna interagera med ACR används **ett huvudnamn för** Tjänsten Azure. Med följande CLI-kommando kan du auktorisera en befintlig ACR i din prenumeration och konfigurerar lämplig **ACRPull-roll** för tjänstens huvudnamn. Ange giltiga värden för dina parametrar nedan.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Du kan också ange ACR-namnet med hjälp av ett ACR-resurs-ID, som har följande format:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Det här steget kan ta flera minuter att slutföra.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurera ACR-integrering för befintliga AKS-kluster

Integrera en befintlig ACR med befintliga AKS-kluster genom att ange giltiga värden för **acr-name** eller **acr-resource-id** enligt nedan.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

Eller

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Du kan också ta bort integreringen mellan en ACR och ett AKS-kluster med följande

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

eller

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Arbeta med ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importera en bild till ACR

Importera en bild från dockerhubben till ACR genom att köra följande:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuera exempelavbildningen från ACR till AKS

Se till att du har rätt AKS-autentiseringsuppgifter

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Skapa en fil som heter **acr-nginx.yaml** som innehåller följande:

```yaml
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
```

Kör sedan den här distributionen i AKS-klustret:

```console
kubectl apply -f acr-nginx.yaml
```

Du kan övervaka distributionen genom att köra:

```console
kubectl get pods
```

Du borde ha två löparkapslar.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
