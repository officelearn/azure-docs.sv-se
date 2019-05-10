---
title: Skapa virtuella noder med hjälp av Azure CLI i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure CLI för att skapa ett kluster i Azure Kubernetes Services (AKS) som använder virtuella noder för att köra poddar.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 7631a2d6aef2efedf30c0b9015913c89949d4c29
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506948"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Skapa och konfigurera en Azure Kubernetes Services kluster (AKS) för att använda virtuella noder med Azure CLI

Om du vill skala snabbt arbetsbelastningar för program i ett kluster i Azure Kubernetes Service (AKS), kan du använda virtuella noder. Med virtuella noder har snabb etablering av poddar och betala bara per sekund för sin Utförandetid. Du behöver inte vänta tills Kubernetes-kluster autoskalningen att distribuera VM-beräkningsnoder för att köra de nya poddarna. Virtuella noder stöds endast med Linux-poddar och noder.

Den här artikeln visar hur du skapar och konfigurerar virtuella nätverksresurser och AKS-kluster och aktivera sedan virtuella noder.

## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder aktivera nätverkskommunikationen mellan poddar som körs i ACI och AKS-klustret. För att ge den här kommunikationen, ett virtuellt nätverksundernät skapas och tilldelas delegerade behörigheter. Virtuella noder fungerar bara med AKS-kluster som skapas med hjälp av *avancerade* nätverk. Som standard AKS-kluster skapas med *grundläggande* nätverk. Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och sedan distribuera ett AKS-kluster som använder avancerade nätverk.

Om du inte tidigare har använt ACI registrera tjänstleverantören med din prenumeration. Du kan kontrollera status för ACI providern registrering med den [az provider list] [ az-provider-list] kommandot, som visas i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Den *Microsoft.ContainerInstance* provider ska rapporteras som *registrerad*, vilket visas i följande Exempelutdata:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Om providern visas som *NotRegistered*, registrera providern med hjälp av den [az provider register] [ az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regional tillgänglighet

Följande regioner har stöd för virtuell nod distributioner:

* Australien, östra (Australien)
* Centrala USA (centralus)
* Östra USA (eastus)
* Östra USA 2 (usaöstra2)
* Japan East (japaneast)
* Nordeuropa (europanorra)
* Sydostasien (southeastasia)
* USA, västra centrala (västra)
* Västeuropa (westeurope)
* Västra USA (westus)
* Västra USA 2 (västra USA 2)

## <a name="known-limitations"></a>Kända begränsningar
Funktionen för virtuella noder är kraftigt beroende på ACIS funktionsuppsättning. Följande scenarier stöds inte ännu med virtuella noder

* Med hjälp av tjänstens huvudnamn till pull ACR-avbildningar. [Lösning](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) är att använda [Kubernetes-hemligheter](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Virtuella nätverksbegränsningar](../container-instances/container-instances-vnet.md) inklusive VNet-peering, Kubernetes-nätverksprinciper och utgående trafik till internet med nätverkssäkerhetsgrupper.
* Init behållare
* [Alias för värd](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argument](../container-instances/container-instances-exec.md#restrictions) för exec i ACI
* [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) distribuerar inte poddar till virtuell nod

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell, Välj **prova** från det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du vill installera och använda CLI lokalt kan du ha Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med hjälp av den [az network vnet skapa] [ az-network-vnet-create] kommando. I följande exempel skapas ett virtuellt nätverksnamn *myVnet* med adressprefixet *10.0.0.0/8*, och ett undernät med namnet *myAKSSubnet*. Det här undernätet adressprefix som standard *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Nu skapar du en ytterligare undernät för virtuella noder med hjälp av den [az network vnet-undernät skapa] [ az-network-vnet-subnet-create] kommando. I följande exempel skapas ett undernät med namnet *myVirtualNodeSubnet* med adressprefix *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Du kan skapa ett huvudnamn för tjänsten automatiskt via Azure CLI eller portalen, eller så kan du skapa ett i förväg och tilldela ytterligare behörigheter.

Skapa ett tjänstobjekt med den [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] kommando. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Utdata ser ut ungefär så här:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anteckna värdena för *appId* och *password*. De här värdena används i senare steg.

## <a name="assign-permissions-to-the-virtual-network"></a>Tilldela behörigheter till det virtuella nätverket

Du måste ge behörighet att använda nätverksresurser AKS tjänstens huvudnamn för att tillåta att använda och hantera det virtuella nätverket.

Hämta först det virtuella nätverket resource ID med [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Om du vill ge rätt åtkomsten för AKS-klustret ska använda det virtuella nätverket, skapa en roll tilldelning med den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando. Byt ut `<appId`> och `<vnetId>` mot värdena du antecknade i föregående två steg.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Du distribuerar ett AKS-kluster i AKS-undernät som skapats i föregående steg. Hämta ID för det här undernätet med [az network vnet-undernät show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Använd kommandot [az aks create] [ az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Ersätt `<subnetId>` med ID: T som hämtades i föregående steg, och sedan `<appId>` och `<password>` med den 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Efter flera minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

## <a name="enable-virtual-nodes-addon"></a>Aktivera tillägg för virtuella noder

Om du vill aktivera virtuella noder nu använda den [az aks enable-tillägg] [ az-aks-enable-addons] kommando. I följande exempel används undernätet med namnet *myVirtualNodeSubnet* skapade i föregående steg:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. I det här steget laddar vi ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande Exempelutdata visar den enskilda VM-noden som skapats och sedan noden virtuella för Linux, *virtuella-nod-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempelapp

Skapa en fil med namnet `virtual-node.yaml` och kopiera följande YAML. Så här schemalägger du behållaren på noden och en [nodeSelector] [ node-selector] och [toleration] [ toleration] har definierats.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Kör programmet med den [kubectl gäller] [ kubectl-apply] kommando.

```console
kubectl apply -f virtual-node.yaml
```

Använd den [kubectl hämta poddar] [ kubectl-get] med den `-o wide` argumentet att mata ut en lista över poddar och noden schemalagda. Observera att den `aci-helloworld` pod har schemalagts på den `virtual-node-aci-linux` noden.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Poden tilldelas en intern IP-adress från Azure-nätverksundernät delegerad för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras i Azure Container Registry, [konfigurerar och använder en Kubernetes-hemlighet][acr-aks-secrets]. En aktuell begränsning i virtuella noder är att du inte kan använda integrerade Azure AD-tjänstobjektautentisering. Om du inte använder en hemlighet poddar som schemalagts på virtuella noder gick inte att starta och rapportera felet `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testa virtuell nod pod

Bläddra till demoprogram med en webbklient för att testa pod körs på virtuella noden. Eftersom en intern IP-adress tilldelas i en pod, kan du snabbt testa den här anslutningen från en annan pod på AKS-klustret. Skapa en test-pod och bifogas en terminalsession:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Installera `curl` i en pod med hjälp av `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Nu komma åt adressen för din pod med hjälp av `curl`, till exempel *http://10.241.0.4*. Ange din egen interna IP-adress som visas i föregående `kubectl get pods` kommando:

```console
curl -L http://10.241.0.4
```

Demoprogrammet visas enligt följande komprimerade exempel på utdata:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalsession till din test pod med `exit`. När sessionen avslutas är poden den tagits bort.

## <a name="remove-virtual-nodes"></a>Ta bort virtuella noder

Om du inte längre vill använda virtuella noder kan du inaktivera dem med hjälp av den [az aks disable-tillägg] [ az aks disable-addons] kommando. 

Först tar bort helloworld-pod körs på virtuella noden:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

Följande exempelkommando inaktiverar de virtuella Linux-noderna:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Nu ska du ta bort det virtuella nätverksresurser och resursgruppen:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln var en pod schemalagda på virtuell nod och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänstdistribution och dirigera trafik till din pod via en belastningsutjämnare eller ingress-kontrollant. Mer information finns i [skapa en grundläggande ingress-kontrollant i AKS][aks-basic-ingress].

Virtuella noder är ofta en komponent i en skalning lösning i AKS. Mer information om skalning lösningar finns i följande artiklar:

- [Använda Kubernetes vågrät pod autoskalningen][aks-hpa]
- [Använda Kubernetes-kluster autoskalningen][aks-cluster-autoscaler]
- [Kolla in automatisk skalning-exemplen för virtuella noder][virtual-node-autoscale]
- [Läs mer om Virtual Kubelet-biblioteket för öppen källkod][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
