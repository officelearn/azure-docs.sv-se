---
title: Skapa virtuella noder med Azure CLI i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure CLI för att skapa ett AKS-kluster (Azure Kubernetes Services) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 31e8b5aceb356ca1415419650a9df3070462bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475535"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Skapa och konfigurera ett AKS-kluster (Azure Kubernetes Services) för att använda virtuella noder med Azure CLI

Om du snabbt vill skala programarbetsbelastningar i ett AKS-kluster (Azure Kubernetes Service) kan du använda virtuella noder. Med virtuella noder har du snabb etablering av poddar och betalar bara per sekund för deras körningstid. Du behöver inte vänta på kubernetes kluster automatiskskalning för att distribuera VM-beräkningsnoder för att köra ytterligare poddar. Virtuella noder stöds endast med Linux-poddar och noder.

Den här artikeln visar hur du skapar och konfigurerar virtuella nätverksresurser och AKS-kluster och aktiverar sedan virtuella noder.

## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i ACI och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett virtuellt nätverksundernät och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med hjälp av *avancerade* nätverk. Som standard skapas AKS-kluster med *grundläggande* nätverk. Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och distribuerar sedan ett AKS-kluster som använder avancerade nätverk.

Om du inte tidigare har använt ACI registrerar du tjänsteleverantören med din prenumeration. Du kan kontrollera status för ACI-providerregistreringen med kommandot [az provider list,][az-provider-list] som visas i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance-providern* ska rapportera som *registrerad*, vilket visas i följande exempelutdata:

```output
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Om leverantören visar som *NotRegistered*registrerar du leverantören med hjälp av [det az-leverantörsregistret][az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regional tillgänglighet

Följande regioner stöds för virtuella noddistributioner:

* Östra Australien (australieniska)
* Centrala USA (centralus)
* Östlig US (eastus)
* Östra US 2 (eastus2)
* Östra Japan (japaneast)
* Norra Europa (northeurope)
* Sydostasien (sydostliga)
* Västra central-US (westcentralus)
* Västeuropa (westeurope)
* Västra US (westus)
* Västra US 2 (westus2)

## <a name="known-limitations"></a>Kända begränsningar
Funktionen Virtuella noder är starkt beroende av ACI:s funktionsuppsättning. Följande scenarier stöds ännu inte med virtuella noder

* Använda tjänstens huvudnamn för att hämta ACR-bilder. [Lösning](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) är att använda [Kubernetes hemligheter](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Begränsningar för virtuella nätverk,](../container-instances/container-instances-vnet.md) inklusive VNet-peering, Kubernetes-nätverksprinciper och utgående trafik till internet med nätverkssäkerhetsgrupper.
* Init behållare
* [Värdalias](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argument](../container-instances/container-instances-exec.md#restrictions) för exec i ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) distribuerar inte poddar till den virtuella noden
* [Windows Server-noder (för närvarande i förhandsversion i AKS)](windows-container-cli.md) stöds inte tillsammans med virtuella noder. Du kan använda virtuella noder för att schemalägga Windows Server-behållare utan att windows servernoder behöver användas i ett AKS-kluster.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du **Prova det** i det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/bash](https://shell.azure.com/bash)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här artikeln Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create.][az-network-vnet-create] I följande exempel skapas ett virtuellt nätverksnamn *myVnet* med ett adressprefix *på 10.0.0.0/8*och ett undernät med namnet *myAKSSubnet*. Adressprefixet för det här undernätet är som standard *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Skapa nu ytterligare ett undernät för virtuella noder med kommandot [az network vnet subnet create.][az-network-vnet-subnet-create] I följande exempel skapas ett undernät med namnet *myVirtualNodeSubnet* med adressprefixet *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Du kan skapa ett huvudnamn för tjänsten automatiskt via Azure CLI eller portalen, eller så kan du skapa ett i förväg och tilldela ytterligare behörigheter.

Skapa ett huvudnamn för tjänsten med kommandot [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Utdata ser ut ungefär så här:

```output
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

Om du vill att klustret ska kunna använda och hantera det virtuella nätverket måste du ge AKS-tjänsten huvudnamn rätt behörighet att använda nätverksresurserna.

Först får du det virtuella nätverksresurs-ID:et med [az-nätverksvnätsvisning:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Om du vill bevilja rätt åtkomst för AKS-klustret att använda det virtuella nätverket skapar du en rolltilldelning med kommandot [az role assignment create.][az-role-assignment-create] Byt ut `<appId`> och `<vnetId>` mot värdena du antecknade i föregående två steg.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Du distribuerar ett AKS-kluster till AKS-undernätet som skapats i ett tidigare steg. Hämta ID:et för det här undernätet med [az-nätverksnätsnät visar:][az-network-vnet-subnet-show]

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Ersätt `<subnetId>` med det ID som erhölls `<appId>` `<password>` i föregående steg och sedan och med 

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

## <a name="enable-virtual-nodes-addon"></a>Aktivera addon för virtuella noder

Om du vill aktivera virtuella noder använder du nu kommandot [az aks enable-addons.][az-aks-enable-addons] I följande exempel används undernätet *myVirtualNodeSubnet* som skapats i ett tidigare steg:

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

Följande exempelutdata visar den enda VM-noden som skapats och sedan den virtuella noden för Linux, *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempelapp

Skapa en `virtual-node.yaml` fil med namnet och kopiera i följande YAML. Om du vill schemalägga behållaren på noden definieras en [nodSelector][node-selector] och [toleration.][toleration]

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

Kör programmet med kommandot [kubectl apply.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Använd kommandot [kubectl get pods][kubectl-get] med `-o wide` argumentet att mata ut en lista över poddar och den schemalagda noden. Observera att `aci-helloworld` podden har `virtual-node-aci-linux` schemalagts på noden.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Podden tilldelas en intern IP-adress från Azures virtuella nätverksundernät som delegerats för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras i Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet][acr-aks-secrets]. En aktuell begränsning av virtuella noder är att du inte kan använda integrerad Azure AD-tjänsthuvudtillståndsautentisering. Om du inte använder en hemlighet kan poddar som schemalagts `HTTP response status code 400 error code "InaccessibleImage"`på virtuella noder inte starta och rapportera felet .

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella nodpodden

Om du vill testa podden som körs på den virtuella noden bläddrar du till demoprogrammet med en webbklient. Eftersom podden tilldelas en intern IP-adress kan du snabbt testa den här anslutningen från en annan pod i AKS-klustret. Skapa en testkapsel och bifoga en terminalsession till den:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
```

Installera `curl` i pod `apt-get`med:

```console
apt-get update && apt-get install -y curl
```

Nu tillgång till adressen `curl`till din *http://10.241.0.4*pod med , till exempel . Ange din egen interna IP-adress som visas i föregående `kubectl get pods` kommando:

```console
curl -L http://10.241.0.4
```

Demoprogrammet visas, som visas i följande komprimerade exempelutdata:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalsessionen till testenheten med `exit`. När sessionen är avslutad är podden den borttagna.

## <a name="remove-virtual-nodes"></a>Ta bort virtuella noder

Om du inte längre vill använda virtuella noder kan du inaktivera dem med kommandot [az aks disable-addons.][az aks disable-addons] 

Om det behövs [https://shell.azure.com](https://shell.azure.com) går du till för att öppna Azure Cloud Shell i din webbläsare.

Ta först bort helloworld pod som körs på den virtuella noden:

```console
kubectl delete -f virtual-node.yaml
```

Följande exempelkommando inaktiverar virtuella Linux-noder:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Ta nu bort de virtuella nätverksresurserna och resursgruppen:

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

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har en pod schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänstdistribution och dirigera trafik till din pod via en belastningsutjämnare eller ingående styrenhet. Mer information finns [i Skapa en grundläggande ingress-styrenhet i AKS][aks-basic-ingress].

Virtuella noder är ofta en komponent i en skalningslösning i AKS. Mer information om skalningslösningar finns i följande artiklar:

- [Använda kubernetes horisontella pod autoscaler][aks-hpa]
- [Använda Kubernetes-kluster automatisk skalning][aks-cluster-autoscaler]
- [Kolla in exemplet för automatisk skalning för virtuella noder][virtual-node-autoscale]
- [Läs mer om Virtual Kubelet open source-bibliotek][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

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
