---
title: Kör Virtual Kubelet i ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder Virtual Kubelet med Azure Kubernetes Service (AKS) för att köra Linux- och Windows-behållare på Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 2730ab1d909ead0431f0dd7fd0061d3080834296
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443740"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Använda Virtual Kubelet med Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) ger en värdmiljö för behållare som körs i Azure. När du använder ACI, behöver inte hantera den underliggande beräkningsinfrastrukturen, Azure hanterar det här hjälper dig. När du kör behållare i ACI, debiteras du per sekund för varje behållare som körs.

När du använder Virtual Kubelet-providern för Azure Container Instances, kan både Linux och Windows-behållare schemaläggas på en behållarinstans som om det är en standard Kubernetes-nod. Den här konfigurationen kan du dra nytta av funktionerna i Kubernetes såväl management värde och kostnaden fördelen med behållarinstanser.

> [!NOTE]
> Virtual Kubelet är en experimentell öppenkällkodsprojekt och bör användas som sådana. För att bidra, problem med Files, och Läs mer om virtual kubelet finns i den [Virtual Kubelet GitHub-projektet][vk-github].

Den här dokumentet beskriver konfigurera Virtual Kubelet för container instances på ett AKS.

## <a name="prerequisite"></a>Krav

Det här dokumentet förutsätter att du har ett AKS-kluster. Om du behöver ett AKS-kluster finns i den [Snabbstart för Azure Kubernetes Service (AKS)][aks-quick-start].

Du måste också Azure CLI version **2.0.33** eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Installera Virtual Kubelet [Helm](https://docs.helm.sh/using_helm/#installing-helm) krävs också.

### <a name="for-rbac-enabled-clusters"></a>För RBAC-aktiverade kluster

Om AKS-klustret är RBAC-aktiverad, måste du skapa ett tjänstkonto och rollen bindning för användning med Tiller. Mer information finns i [Helm rollbaserad åtkomstkontroll][helm-rbac].

En *ClusterRoleBinding* måste även skapas för Virtual Kubelet. Skapa en bindning kan skapa en fil med namnet *rbac-virtualkubelet.yaml* och klistra in följande definition:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: virtual-kubelet
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
```

Tillämpa bindning med [kubectl gäller] [ kubectl-apply] och ange din *rbac-virtualkubelet.yaml* filen enligt i följande exempel:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/virtual-kubelet created
```

Du kan nu fortsätta att installera Virtual Kubelet i AKS-klustret.

## <a name="installation"></a>Installation

Använd den [az aks install-connector] [ aks-install-connector] kommando för att installera Virtual Kubelet. I följande exempel har distribuerat både Linux och Windows-anslutningsapp.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

De här argumenten är tillgängliga för den `aks install-connector` kommando.

| Argument: | Beskrivning | Krävs |
|---|---|:---:|
| `--connector-name` | Namnet på ACI-Anslutningsappens.| Ja |
| `--name` `-n` | Namnet på det hanterade klustret. | Ja |
| `--resource-group` `-g` | Namnet på resursgruppen. | Ja |
| `--os-type` | Container instances typ av operativsystem. Tillåtna värden: båda, Linux, Windows. Standard: Linux. | Nej |
| `--aci-resource-group` | Den resursgrupp där du kan skapa grupper för ACI-behållare. | Nej |
| `--location` `-l` | Platsen som används för att skapa ACI-behållargrupper. | Nej |
| `--service-principal` | Tjänstens huvudnamn som används för autentisering i Azure API: er. | Nej |
| `--client-secret` | Hemligheten som är associerad med tjänstens huvudnamn. | Nej |
| `--chart-url` | URL för ett Helm-diagram som installerar ACI-Anslutningsappens. | Nej |
| `--image-tag` | Bildtagg behållaravbildningens virtual kubelet. | Nej |

## <a name="validate-virtual-kubelet"></a>Verifiera Virtual Kubelet

För att verifiera att Virtual Kubelet har installerats kan returnera en lista med Kubernetes-noder med den [kubectl få noder] [ kubectl-get] kommando.

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Kör Linux-behållare

Skapa en fil med namnet `virtual-kubelet-linux.yaml` och kopiera följande YAML. Ersätt den `kubernetes.io/hostname` värdet med namnet på noden Linux Virtual Kubelet. Anteckna som en [nodeSelector] [ node-selector] och [toleration] [ toleration] som används för att schemalägga behållaren på noden.

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

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Använd den [kubectl hämta poddar] [ kubectl-get] med den `-o wide` argumentet att mata ut en lista över poddar med schemalagda noden. Observera att den `aci-helloworld` pod har schemalagts på den `virtual-kubelet-virtual-kubelet-linux` noden.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Köra Windows-behållare

Skapa en fil med namnet `virtual-kubelet-windows.yaml` och kopiera följande YAML. Ersätt den `kubernetes.io/hostname` värdet med namnet på noden Windows Virtual Kubelet. Anteckna som en [nodeSelector] [ node-selector] och [toleration] [ toleration] som används för att schemalägga behållaren på noden.

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

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Använd den [kubectl hämta poddar] [ kubectl-get] med den `-o wide` argumentet att mata ut en lista över poddar med schemalagda noden. Observera att den `nanoserver-iis` pod har schemalagts på den `virtual-kubelet-virtual-kubelet-win` noden.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Ta bort Virtual Kubelet

Använd den [az aks remove-connector] [ aks-remove-connector] till att ta bort Virtual Kubelet. Ersätt argumentvärden med namnet på anslutningen, AKS-kluster och resursgruppen för AKS-kluster.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Virtual Kubelet på den [Virtual Kubelet Github projet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
