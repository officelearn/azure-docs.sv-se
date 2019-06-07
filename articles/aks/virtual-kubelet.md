---
title: Kör Virtual Kubelet i ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder Virtual Kubelet med Azure Kubernetes Service (AKS) för att köra Linux- och Windows-behållare på Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: cc0c3becf21cb54b97a88e9ba35b38308af81a85
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475423"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Använda Virtual Kubelet med Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) ger en värdmiljö för behållare som körs i Azure. När du använder ACI, behöver inte hantera den underliggande beräkningsinfrastrukturen, Azure hanterar det här hjälper dig. När du kör behållare i ACI, debiteras du per sekund för varje behållare som körs.

När du använder Virtual Kubelet-providern för Azure Container Instances, kan både Linux och Windows-behållare schemaläggas på en behållarinstans som om det är en standard Kubernetes-nod. Den här konfigurationen kan du dra nytta av funktionerna i Kubernetes såväl management värde och kostnaden fördelen med behållarinstanser.

> [!NOTE]
> AKS har nu inbyggt stöd för schemaläggning av behållare i ACI som kallas *virtuella noder*. Dessa virtuella noder har för närvarande stöd för Linux-behållare-instanser. Om du vill schemalägga behållarinstanser för Windows kan fortsätta du använda Virtual Kubelet. I annat fall bör du använda virtuella noder i stället för manuell Virtual Kubelet instruktionerna som anges i den här artikeln. Du kan komma igång med virtuella noder med hjälp av den [Azure CLI] [ virtual-nodes-cli] eller [Azure-portalen][virtual-nodes-portal].
>
> Virtual Kubelet är en experimentell öppenkällkodsprojekt och bör användas som sådana. För att bidra, problem med Files, och Läs mer om virtual kubelet finns i den [Virtual Kubelet GitHub-projektet][vk-github].

## <a name="before-you-begin"></a>Innan du börjar

Det här dokumentet förutsätter att du har ett AKS-kluster. Om du behöver ett AKS-kluster finns i den [Snabbstart för Azure Kubernetes Service (AKS)][aks-quick-start].

Du måste också Azure CLI version **2.0.65** eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill installera Virtual Kubelet, installera och konfigurera [Helm] [ aks-helm] AKS-klustret. Se till att din Tiller [konfigurerats för användning med Kubernetes RBAC](#for-rbac-enabled-clusters), om det behövs.

### <a name="register-container-instances-feature-provider"></a>Registrera providern för Container Instances-funktionen

Om du inte tidigare har använt tjänsten Azure Container Instance (ACI) kan du registrera tjänstleverantören med din prenumeration. Du kan kontrollera status för ACI providern registrering med den [az provider list] [ az-provider-list] kommandot, som visas i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Den *Microsoft.ContainerInstance* provider ska rapporteras som *registrerad*, vilket visas i följande Exempelutdata:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Om providern visas som *NotRegistered*, registrera providern med hjälp av den [az provider register] [ az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>För RBAC-aktiverade kluster

Om AKS-klustret är RBAC-aktiverad, måste du skapa ett tjänstkonto och rollen bindning för användning med Tiller. Mer information finns i [Helm rollbaserad åtkomstkontroll][helm-rbac]. Om du vill skapa ett tjänstkonto och rollen bindning, skapa en fil med namnet *rbac virtuella kubelet.yaml* och klistra in följande definition:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Tillämpa tjänstkontot och bindning med [kubectl gäller] [ kubectl-apply] och ange din *rbac virtuella kubelet.yaml* filen enligt i följande exempel:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Konfigurera Helm för att använda kontot tiller:

```console
helm init --service-account tiller
```

Du kan nu fortsätta att installera Virtual Kubelet i AKS-klustret.

## <a name="installation"></a>Installation

Använd den [az aks install-connector] [ aks-install-connector] kommando för att installera Virtual Kubelet. I följande exempel har distribuerat både Linux och Windows-anslutningsapp.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

De här argumenten är tillgängliga för den [az aks install-connector] [ aks-install-connector] kommando.

| Argument: | Beskrivning | Obligatoriskt |
|---|---|:---:|
| `--connector-name` | Namnet på ACI-Anslutningsappens.| Ja |
| `--name` `-n` | Namnet på det hanterade klustret. | Ja |
| `--resource-group` `-g` | Namnet på resursgruppen. | Ja |
| `--os-type` | Container instances typ av operativsystem. Tillåtna värden: Both, Linux, Windows. standard: Linux. | Nej |
| `--aci-resource-group` | Den resursgrupp där du kan skapa grupper för ACI-behållare. | Nej |
| `--location` `-l` | Platsen som används för att skapa ACI-behållargrupper. | Nej |
| `--service-principal` | Tjänstens huvudnamn som används för autentisering i Azure API: er. | Nej |
| `--client-secret` | Hemligheten som är associerad med tjänstens huvudnamn. | Nej |
| `--chart-url` | URL för ett Helm-diagram som installerar ACI-Anslutningsappens. | Nej |
| `--image-tag` | Bildtagg behållaravbildningens virtual kubelet. | Nej |

## <a name="validate-virtual-kubelet"></a>Verifiera Virtual Kubelet

För att verifiera att Virtual Kubelet har installerats kan returnera en lista med Kubernetes-noder med den [kubectl få noder] [ kubectl-get] kommando:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Kör Linux-behållare

Skapa en fil med namnet `virtual-kubelet-linux.yaml` och kopiera följande YAML. Anteckna som en [nodeSelector] [ node-selector] och [toleration] [ toleration] som används för att schemalägga behållaren på noden.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Kör programmet med den [kubectl skapa] [ kubectl-create] kommando.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Använd den [kubectl hämta poddar] [ kubectl-get] med den `-o wide` argumentet att mata ut en lista över poddar med schemalagda noden. Observera att den `aci-helloworld` pod har schemalagts på den `virtual-kubelet-virtual-kubelet-linux` noden.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Kör Windows-behållare

Skapa en fil med namnet `virtual-kubelet-windows.yaml` och kopiera följande YAML. Anteckna som en [nodeSelector] [ node-selector] och [toleration] [ toleration] som används för att schemalägga behållaren på noden.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Kör programmet med den [kubectl skapa] [ kubectl-create] kommando.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Använd den [kubectl hämta poddar] [ kubectl-get] med den `-o wide` argumentet att mata ut en lista över poddar med schemalagda noden. Observera att den `nanoserver-iis` pod har schemalagts på den `virtual-kubelet-virtual-kubelet-windows` noden.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Ta bort Virtual Kubelet

Använd den [az aks remove-connector] [ aks-remove-connector] till att ta bort Virtual Kubelet. Ersätt argumentvärden med namnet på anslutningen, AKS-kluster och resursgruppen för AKS-kluster.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Om du stöter på fel som tar bort båda OS-kopplingar eller vill du ta bort bara Windows eller Linux OS-anslutningen kan ange du manuellt OS-typen. Lägg till den `--os-type` parametern till den tidigare `az aks remove-connector` kommandot och ange `Windows` eller `Linux`.

## <a name="next-steps"></a>Nästa steg

Möjligt problem med Virtual Kubelet finns det [kända paketberoenden och lösningar][vk-troubleshooting]. För att anmäla problem med Virtual Kubelet [öppna ett GitHub-ärende][vk-issues].

Läs mer om Virtual Kubelet på den [Virtual Kubelet GitHub-projektet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
