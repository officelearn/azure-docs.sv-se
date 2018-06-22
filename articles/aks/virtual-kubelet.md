---
title: Kör virtuella kubelet i ett kluster med Azure Kubernetes Service (AKS)
description: Använd virtuella kubelet om du vill köra Kubernetes behållare på Azure-Behållarinstanser.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: nepeters
ms.openlocfilehash: 0d046970b40f5253b07005ab578035c7c179e7df
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304446"
---
# <a name="virtual-kubelet-with-aks"></a>Virtuella Kubelet med AKS

Azure Container instanser (ACI) ger en miljö som är värdar för att köra behållare i Azure. När du använder ACI, behöver du inte hantera underliggande beräkningsinfrastrukturen, Azure hanterar detta management för dig. När du kör behållare i ACI debiteras med andra för varje behållare som körs.

När du använder virtuella Kubelet providern för Azure-Behållarinstanser, kan Windows- och Linux-behållare schemaläggas på en behållare-instans som om det är en standard Kubernetes-nod. Den här konfigurationen kan du dra nytta av både funktionerna i Kubernetes och hantering av värdet och kostnaden fördelarna behållarinstanser.

> [!NOTE]
> Virtuella Kubelet är ett experiment öppen källkod projekt och ska användas som sådana. Om du vill bidra filen problem och Läs mer om virtuella kubelet finns i [virtuella Kubelet GitHub projekt][vk-github].

Det här dokumentet beskriver Konfigurera virtuella Kubelet för behållarinstanser på en AKS.

## <a name="prerequisite"></a>Krav

Det här dokumentet förutsätter att du har ett AKS-kluster. Om du behöver ett AKS-kluster finns i [Azure Kubernetes Service (AKS) quickstart][aks-quick-start].

Du måste också Azure CLI-versionen **2.0.33** eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

[Helm](https://docs.helm.sh/using_helm/#installing-helm) krävs också för att installera virtuella Kubelet.

## <a name="installation"></a>Installation

Använd den [az aks install-koppling] [ aks-install-connector] kommando för att installera virtuella Kubelet. I följande exempel distribuerar både Linux och Windows-anslutningen.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

De här argumenten är tillgängliga för den `aks install-connector` kommando.

| Argument: | Beskrivning | Krävs |
|---|---|:---:|
| `--connector-name` | Namnet på ACI kopplingen.| Ja |
| `--name` `-n` | Namnet på det hanterade klustret. | Ja |
| `--resource-group` `-g` | Namnet på resursgruppen. | Ja |
| `--os-type` | Typ av behållare instanser operativsystem. Tillåtna värden: båda, Linux, Windows. Standard: Linux. | Nej |
| `--aci-resource-group` | Resursgruppen att skapa grupper för ACI behållare. | Nej |
| `--location` `-l` | Platsen du grupperar ACI behållare. | Nej |
| `--service-principal` | Tjänstens huvudnamn som används för autentisering till Azure API: er. | Nej |
| `--client-secret` | Den hemlighet som är kopplade till tjänstens huvudnamn. | Nej |
| `--chart-url` | URL för ett Helm diagram som installerar ACI koppling. | Nej |
| `--image-tag` | Bildtagg bildens virtuella kubelet behållare. | Nej |

## <a name="validate-virtual-kubelet"></a>Verifiera virtuella Kubelet

Om du vill verifiera att virtuella Kubelet har installerats måste returnera en lista över Kubernetes-noder som använder den [kubectl hämta noder] [ kubectl-get] kommando.

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Kör Linux-behållare

Skapa en fil med namnet `virtual-kubelet-linux.yaml` och kopiera följande YAML. Ersätt den `kubernetes.io/hostname` värdet med namnet på den virtuella Linux-Kubelet-noden. Notera som en [nodeSelector] [ node-selector] och [toleration] [ toleration] används för att schemalägga behållare på noden.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Kör programmet med den [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

Använd den [kubectl hämta skida] [ kubectl-get] kommandot med den `-o wide` argumentet till en lista över skida med noden schemalagda utdata. Observera att den `aci-helloworld` baljor har schemalagts på den `virtual-kubelet-virtual-kubelet-linux` nod.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Kör Windows-behållaren

Skapa en fil med namnet `virtual-kubelet-windows.yaml` och kopiera följande YAML. Ersätt den `kubernetes.io/hostname` värdet med namnet på virtuella Windows-Kubelet-nod. Notera som en [nodeSelector] [ node-selector] och [toleration] [ toleration] används för att schemalägga behållare på noden.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Kör programmet med den [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

Använd den [kubectl hämta skida] [ kubectl-get] kommandot med den `-o wide` argumentet till en lista över skida med noden schemalagda utdata. Observera att den `nanoserver-iis` baljor har schemalagts på den `virtual-kubelet-virtual-kubelet-win` nod.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Ta bort virtuella Kubelet

Använd den [az aks remove-koppling] [ aks-remove-connector] för att ta bort virtuella Kubelet. Ersätt värdet för argumentet med namnet på anslutningen, AKS kluster och AKS klusterresursgrupp.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Nästa steg

Läs mer om virtuella Kubelet på den [virtuella Kubelet Github projet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
