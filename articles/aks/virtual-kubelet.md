---
title: Köra virtuella Kubelet i ett Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du använder Virtual Kubelet med Azure Kubernetes service (AKS) för att köra Linux-och Windows-behållare på Azure Container Instances.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613864"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Använda Virtual Kubelet med Azure Kubernetes service (AKS)

Azure Container Instances (ACI) tillhandahåller en värdbaserad miljö för att köra behållare i Azure. När du använder ACI behöver du inte hantera den underliggande beräknings infrastrukturen. Azure hanterar den här hanteringen åt dig. När du kör behållare i ACI debiteras du per sekund för varje behållare som körs.

När du använder den virtuella Kubelet-providern för Azure Container Instances, kan både Linux-och Windows-behållare schemaläggas på en behållar instans som om den är en standardKubernetes-nod. Med den här konfigurationen kan du dra nytta av både funktionerna i Kubernetes och hantering svärdet och kostnads fördelarna med behållar instanser.

> [!NOTE]
> AKS har nu inbyggt stöd för schemaläggning av behållare på ACI, som kallas *virtuella noder*. Dessa virtuella noder stöder för närvarande Linux container instances. Om du behöver schemalägga Windows container instances kan du fortsätta att använda virtuella Kubelet. Annars bör du använda virtuella noder i stället för de manuella virtuella Kubelet-instruktionerna som anges i den här artikeln. Du kan komma igång med virtuella noder med hjälp av [Azure CLI][virtual-nodes-cli] eller [Azure Portal][virtual-nodes-portal].
>
> Virtual Kubelet är ett projekt med experimentell öppen källkod och bör användas. För att bidra, fil problem och läsa mer om virtuella kubelet, se det [virtuella kubelet-GitHub-projektet][vk-github].

## <a name="before-you-begin"></a>Innan du börjar

Det här dokumentet förutsätter att du har ett AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för [Azure Kubernetes service (AKS)][aks-quick-start].

Du måste också ha Azure CLI-version **2.0.65** eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Installera den virtuella Kubelet genom att installera och konfigurera [Helm][aks-helm] i ditt AKS-kluster. Se till att din till-dator har [kon figurer ATS för användning med KUBERNETES RBAC](#for-rbac-enabled-clusters), om det behövs.

### <a name="register-container-instances-feature-provider"></a>Registrera Container Instances funktions leverantör

Om du inte tidigare har använt tjänsten Azure Container Instance (ACI) registrerar du tjänste leverantören med din prenumeration. Du kan kontrol lera statusen för registreringen av ACI-providern med kommandot [AZ Provider List][az-provider-list] , som du ser i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft. ContainerInstance* -providern ska rapportera som *registrerad*, vilket visas i följande exempel på utdata:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Om providern visas som *NotRegistered*registrerar du providern med [AZ-providerns register][az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>För RBAC-aktiverade kluster

Om ditt AKS-kluster är RBAC-aktiverat måste du skapa ett tjänst konto och en roll bindning för användning med till. Mer information finns i [Helm-rollbaserad åtkomst kontroll][helm-rbac]. Skapa ett tjänst konto och en roll bindning genom att skapa en fil med namnet *RBAC-Virtual-kubelet. yaml* och klistra in följande definition:

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

Tillämpa tjänst kontot och bindningen med [kubectl tillämpa][kubectl-apply] och ange din *RBAC-Virtual-kubelet. yaml* -fil, som visas i följande exempel:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Konfigurera Helm för att använda kontot för till-tjänsten:

```console
helm init --service-account tiller
```

Nu kan du fortsätta att installera den virtuella Kubelet i ditt AKS-kluster.

## <a name="installation"></a>Installation

Använd kommandot [AZ AKS install-Connector][aks-install-connector] för att installera virtuella Kubelet. I följande exempel distribueras både Linux-och Windows-anslutaren.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Dessa argument är tillgängliga för kommandot [AZ AKS install-Connector][aks-install-connector] .

| Påstående | Beskrivning | Obligatorisk |
|---|---|:---:|
| `--connector-name` | Namnet på ACI-anslutningen.| Ja |
| `--name` `-n` | Namnet på det hanterade klustret. | Ja |
| `--resource-group` `-g` | Resurs gruppens namn. | Ja |
| `--os-type` | Container instances operativ system typ. Tillåtna värden: Både Linux, Windows. Standard: Linux. | Nej |
| `--aci-resource-group` | Resurs gruppen där ACI-behållar grupper ska skapas. | Nej |
| `--location` `-l` | Platsen för att skapa ACI container Groups. | Nej |
| `--service-principal` | Tjänstens huvud namn som används för autentisering i Azure API: er. | Nej |
| `--client-secret` | Hemlighet som är associerad med tjänstens huvud namn. | Nej |
| `--chart-url` | URL till ett Helm-diagram som installerar ACI-anslutningsprogrammet. | Nej |
| `--image-tag` | Avbildnings tag gen för den virtuella kubelet-behållar avbildningen. | Nej |

## <a name="validate-virtual-kubelet"></a>Verifiera virtuell Kubelet

Om du vill kontrol lera att den virtuella Kubelet har installerats returnerar du en lista med Kubernetes-noder med kommandot [kubectl get Nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Kör Linux-behållare

Skapa en fil med `virtual-kubelet-linux.yaml` namnet och kopiera i följande yaml. Observera att en avsökning och [tolerera][toleration] används för att schemalägga behållaren på noden. [][node-selector]

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

Kör programmet med kommandot [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Använd kommandot [kubectl get poddar][kubectl-get] med `-o wide` argumentet för att mata ut en lista över poddar med den schemalagda noden. Observera att `aci-helloworld` Pod har schemalagts `virtual-kubelet-virtual-kubelet-linux` på noden.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Kör Windows-behållare

Skapa en fil med `virtual-kubelet-windows.yaml` namnet och kopiera i följande yaml. Observera att en avsökning och [tolerera][toleration] används för att schemalägga behållaren på noden. [][node-selector]

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

Kör programmet med kommandot [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Använd kommandot [kubectl get poddar][kubectl-get] med `-o wide` argumentet för att mata ut en lista över poddar med den schemalagda noden. Observera att `nanoserver-iis` Pod har schemalagts `virtual-kubelet-virtual-kubelet-windows` på noden.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Ta bort virtuell Kubelet

Använd kommandot [AZ AKS Remove-Connector][aks-remove-connector] för att ta bort virtuella Kubelet. Ersätt argument värden med namnet på kopplingen, AKS-klustret och kluster resurs gruppen AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Om du stöter på fel när du tar bort båda OS-anslutningarna eller om du bara vill ta bort Windows-eller Linux OS-anslutaren, kan du manuellt ange OS-typen. Lägg till `az aks remove-connector` `Windows` parametern i föregående kommando och ange eller `Linux`. `--os-type`

## <a name="next-steps"></a>Nästa steg

Eventuella problem med den virtuella Kubelet finns i [kända knep och lösningar][vk-troubleshooting]. Om du vill rapportera problem med den virtuella Kubelet [öppnar du ett GitHub-problem][vk-issues].

Läs mer om Virtual Kubelet i det [virtuella Kubelet-GitHub-projektet][vk-github].

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
