---
title: Konfigurera kubenet nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du konfigurerar kubenet (grundläggande) nätverk i Azure Kubernetes Service (AKS) att distribuera ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: b80177d17e0dc5a4e54396907ecee61890ec523f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011355"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Använd kubenet nätverk med dina egna IP-adressintervall i Azure Kubernetes Service (AKS)

Som standard AKS-kluster används [kubenet][kubenet], och ett Azure-nätverk och undernät skapas åt dig. Med *kubenet*, noder hämta en IP-adress från det virtuella Azure-undernätet. Poddar ta emot en IP-adress från en logiskt olika adressutrymmen till undernätet för Azure-nätverk av noderna. Network adress translation (NAT) konfigureras sedan så att poddarna kan nå resurser på Azure-nätverket. Källans IP-adress av trafiken är skulle NAT till nodens primära IP-adress. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverk kan poddar att använda.

Med [Azure behållare nätverk gränssnitt (CNI)][cni-networking], varje pod får en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unikt för ditt adressutrymme för nätverket och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som stöds. Det motsvarande antalet IP-adresser per nod reserveras sedan direkt för noden. Den här metoden kräver mer planering och leder ofta till IP-adress överbelastning eller att behöva återskapa kluster i ett större undernät allteftersom dina behov växer.

Den här artikeln visar hur du använder *kubenet* nätverk för att skapa och använda ett virtuellt nätverksundernät för ett AKS-kluster. Mer information om Nätverksalternativ och överväganden finns i [nätverk begrepp för Kubernetes och AKS][aks-network-concepts].

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.56 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Översikt över kubenet nätverk med ditt eget undernät

I många miljöer, har du definierat virtuella nätverk och undernät med allokerade IP-adressintervall. Dessa virtuella nätverksresurser används för att stödja flera tjänster och program. AKS-kluster kan använda för att ge nätverksanslutning, *kubenet* (grundläggande nätverk) eller Azure CNI (*avancerade nätverk*).

Med *kubenet*, endast noderna får en IP-adress i virtuella nätverkets undernät. Poddar kan inte kommunicera direkt med varandra. Användardefinierad routning (UDR) och IP-vidarebefordring används i stället för anslutningen mellan poddar mellan noder. Du kan också distribuera poddar bakom en tjänst som tar emot en tilldelad IP-adress och belastningsutjämnar trafik för programmet. Följande diagram visar hur AKS-nodernas får en IP-adress i virtuella nätverkets undernät, men inte poddarna:

![Kubenet nätverk modell med ett AKS-kluster](media/use-kubenet/kubenet-overview.png)

Azure stöder högst 400 vägar i en UDR så att du inte kan ha ett AKS-kluster som är större än 400 noder. AKS funktioner som [virtuella noder] [ virtual-nodes] eller nätverksprinciper stöds inte med *kubenet*.

Med *Azure CNI*, varje pod tar emot en IP-adress i IP-undernät och kan kommunicera direkt med andra poddar och tjänster. Ditt kluster kan vara så stora som IP-adressintervall som du anger. Men det IP-adressintervallet måste planeras i förväg och alla IP-adresser används av AKS-noder baserat på det maximala antalet poddar som de kan stödja. Avancerade nätverksfunktioner och scenarier som [virtuella noder] [ virtual-nodes] eller nätverksprinciper stöds med *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>IP-adressens tillgänglighet och resursuttömning

Med *Azure CNI*, ett vanligt problem är det tilldelade IP-adressintervallet är för liten för att lägga till fler noder när du skalar eller uppgradera ett kluster. Nätverk-teamet kan också inte att utfärda en tillräckligt stor för IP-adressintervall för att stödja din förväntade programbegäran.

Som en svaghet, kan du skapa ett AKS-kluster som använder *kubenet* och Anslut till ett befintligt undernät för virtuellt nätverk. Den här metoden gör att noderna får definierade IP-adresser, utan att behöva reservera ett stort antal IP-adresser direkt för alla potentiella poddarna som kan köras i klustret.

Med *kubenet*, du kan använda ett mycket mindre IP-adressintervall och att kunna stödja stora kluster och behov. Till exempel ännu med en */27* IP-adressintervall, du kan köra ett kluster med 20-25 noder med tillräckligt med utrymme för att skala eller uppgradera. Den här klusterstorleken stöder upp till *2200 2,750* poddar (med standard högst 110 poddar per nod).

Följande grundläggande beräkningar jämför skillnaden i nätverket modeller:

- **kubenet** – en enkel */24* IP-adressintervall har stöd för upp till *251* noder i klustret (varje Azure-nätverksundernät reserverar de första tre IP-adresserna för hanteringsåtgärder)
  - Det här antalet kan ha stöd för upp till *27,610* poddar (med högst standard 110 poddar per nod med *kubenet*)
- **Azure CNI** -den samma basic */24* intervall för undernät kan endast ha stöd för högst *8* noder i klustret
  - Den här nodantal kan endast ha stöd för upp till *240* poddar (med högst standard 30 poddar per nod med *Azure CNI*)

> [!NOTE]
> Dessa maximum inte tar uppgradera eller skalningsåtgärder. Du kan inte köra det maximala antalet noder som har stöd för IP-adressintervall för undernätet i praktiken. Du måste lämna vissa IP-adresser som är tillgängliga för användning under skalan för åtgärder för uppgradering.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering av virtuella nätverk och ExpressRoute-anslutningar

För lokala anslutningar både *kubenet* och *Azure CNI* nätverk metoder kan använda [Azure virtuell nätverkspeering] [ vnet-peering]eller [ExpressRoute-anslutningar][express-route]. Planera dina IP-adressintervall noggrant för att förhindra överlappning och routning av nätverkstrafik som felaktig. Till exempel många lokala nätverk använder en *10.0.0.0/8* adressintervall som annonseras via ExpressRoute-anslutning. Vi rekommenderar att du skapar AKS-kluster i Azure-nätverk undernät utanför den här adressintervall, till exempel *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Välj en modell för nätverket att använda

Valet av vilka nätverk-plugin-programmet för din AKS-kluster är vanligtvis en balans mellan flexibilitet och avancerad konfigurationsbehov. Följande överväganden hjälpa disposition när varje nätverk modell kan vara det lämpligaste.

Använd *kubenet* när:

- Du har begränsad IP-adressutrymme.
- De flesta pod-kommunikationen är i klustret.
- Du behöver inte avancerade funktioner, till exempel virtuella noder eller nätverksprincip.

Använd *Azure CNI* när:

- Du har tillgängliga IP-adressutrymme.
- De flesta av pod-kommunikation är att resurser utanför klustret.
- Du vill inte hantera de udr: er.
- Du behöver avancerade funktioner, till exempel virtuella noder eller nätverksprincip.

> [!NOTE]
> Kuberouter gör det möjligt att aktivera principen för nätverk när du använder kubenet och kan installeras som en daemonset i ett AKS-kluster. Var medveten om kube-router är fortfarande i betaversioner och inget stöd erbjuds av Microsoft för projektet.

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Du kommer igång med hjälp av *kubenet* och egna undernät för virtuellt nätverk först skapa en resurs med den [az gruppen skapa] [ az-group-create] kommando. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Om du inte har ett befintligt virtuellt nätverk och undernät som ska använda, skapa dessa nätverksresurser med hjälp av den [az network vnet skapa] [ az-network-vnet-create] kommando. I följande exempel visas det virtuella nätverket namnet *myVnet* med adressprefix *10.0.0.0/8*. Ett undernät skapas med ett namn *myAKSSubnet* med adressprefixet *10.240.0.0/16*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Skapa ett tjänstobjekt och tilldela behörigheter

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Tjänstens huvudnamn måste ha behörighet att hantera det virtuella nätverket och undernätet med AKS-noder. Du kan skapa ett huvudnamn för tjänsten med den [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] kommando:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Följande Exempelutdata visar program-ID och lösenord för tjänstens huvudnamn. De här värdena används i senare steg för att tilldela en roll till tjänstens huvudnamn och sedan skapa AKS-klustret:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Om du vill tilldela rätt delegeringar i de återstående stegen använder den [az network vnet show] [ az-network-vnet-show] och [az network vnet-undernät show] [ az-network-vnet-subnet-show] kommandon för att hämta nödvändiga resurs-ID: N. Dessa resurs-ID lagras som variabler och refereras till i de återstående stegen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Nu tilldela tjänstens huvudnamn för AKS-klustret *deltagare* behörigheter på virtuellt nätverk med hjälp av den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando. Ange din egen  *\<appId >* som visas i utdata från det föregående kommandot för att skapa tjänstens huvudnamn:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Skapa ett AKS-kluster i det virtuella nätverket

Du har nu skapat ett virtuellt nätverk och undernät, och skapas och behörigheter för ett huvudnamn för tjänsten för att kunna använda dessa nätverksresurser. Nu skapa ett AKS-kluster i ditt virtuella nätverk och undernät med hjälp av den [az aks skapa] [ az-aks-create] kommando. Definiera dina egna huvudnamn för tjänsten  *\<appId >* och  *\<lösenord >*, vilket visas i utdata från det föregående kommandot för att skapa tjänstens huvudnamn.

Följande IP-adressintervall också definieras som en del av klustret skapa processen:

* Den *--service-cidr* används för att tilldela en IP-adress för interna tjänster i AKS-kluster. Den här IP-adressintervallet ska vara ett adressutrymme som inte används i din nätverksmiljö. Detta inkluderar alla lokala nätverksintervall om du ansluter eller planerar att ansluta dina Azure-nätverk med hjälp av Express Route eller en plats-till-plats-VPN-anslutningar.

* Den *--dns-tjänst-IP-* adress ska vara den *.10* -adressen för ditt service IP-adressintervall.

* Den *--pod-cidr* ska vara ett stort adressutrymme som inte används i din nätverksmiljö. Detta inkluderar alla lokala nätverksintervall om du ansluter eller planerar att ansluta dina Azure-nätverk med hjälp av Express Route eller en plats-till-plats-VPN-anslutning.
    * Den här adressintervallet måste vara tillräckligt stor för att anpassa antalet noder som du förväntar dig att skala upp till. Du kan inte ändra den här adressintervall när klustret distribueras om du behöver fler adresser för ytterligare noder.
    * Pod IP-adressintervall som används för att tilldela en */24* adressutrymme till varje nod i klustret. I följande exempel visas den *--pod-cidr* av *192.168.0.0/16* tilldelar den första noden *192.168.0.0/24*, den andra noden *192.168.1.0/24*, och den tredje nod *192.168.2.0/24*.
    * Som klustret skalas eller uppgraderingar kan fortsätter Azure-plattformen att tilldela en pod IP-adressintervall till varje ny nod.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

## <a name="associate-network-resources-with-the-node-subnet"></a>Associera nätverksresurser med nod-undernät

När du skapar ett AKS-kluster, skapas en network security group och dirigera tabell. Dessa nätverksresurser hanteras av AKS kontrollplanet och uppdateras när du skapar och exponera tjänster. Koppla tabellen network security group och dirigera till undernät för virtuellt nätverk enligt följande:

```azurecli-interactive
# Get the MC_ resource group for the AKS cluster resources
MC_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)

# Get the route table for the cluster
ROUTE_TABLE=$(az network route-table list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Get the network security group
NODE_NSG=$(az network nsg list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Update the subnet to associate the route table and network security group
az network vnet subnet update \
    --route-table $ROUTE_TABLE \
    --network-security-group $NODE_NSG \
    --ids $SUBNET_ID
```

## <a name="next-steps"></a>Nästa steg

Med ett AKS-kluster som distribueras till din befintliga undernät för virtuellt nätverk, kan du nu använda klustret som vanligt. Kom igång med [att skapa appar med Azure Dev blanksteg] [ dev-spaces] eller [med Draft][use-draft], eller [distribuera appar med Helm] [use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
