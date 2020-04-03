---
title: Konfigurera kubenet-nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du konfigurerar kubenet -nätverk (basic) i Azure Kubernetes Service (AKS) för att distribuera ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 119265efa7b6504f3faf2e89cb68b9e9bd70bf9f
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617251"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Använda kubenet-nätverk med dina egna IP-adressintervall i Azure Kubernetes Service (AKS)

Som standard använder AKS-kluster [kubenet][kubenet]och ett virtuellt Azure-nätverk och undernät skapas åt dig. Med *kubenet*får noder en IP-adress från Azures virtuella nätverksundernät. Poddar får en IP-adress från ett annat logiskt adressutrymme än det virtuella Azure-nätverkets undernät för noderna. NAT (Network Address Translation) konfigureras sedan så att poddarna kan komma åt resurser i det virtuella Azure-nätverket. Källans IP-adress för trafiken är NAT'd till nodens primära IP-adress. Den här metoden minskar avsevärt antalet IP-adresser som du behöver reservera i nätverksutrymmet för poddar att använda.

Med [CNI (Azure Container Networking Interface)][cni-networking]får varje pod en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unika i hela nätverksutrymmet och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som den stöder. Motsvarande antal IP-adresser per nod reserveras sedan på framsidan för den noden. Den här metoden kräver mer planering och leder ofta till IP-adressutmattning eller behovet av att återskapa kluster i ett större undernät när programmets krav växer.

Den här artikeln visar hur du använder *kubenet-nätverk* för att skapa och använda ett virtuellt nätverksundernät för ett AKS-kluster. Mer information om nätverksalternativ och överväganden finns i [Nätverksbegrepp för Kubernetes och AKS][aks-network-concepts].

## <a name="prerequisites"></a>Krav

* Det virtuella nätverket för AKS-klustret måste tillåta utgående internetanslutning.
* Skapa inte mer än ett AKS-kluster i samma undernät.
* AKS-kluster får `169.254.0.0/16`inte `172.30.0.0/16` `172.31.0.0/16`använda `192.0.2.0/24` , , eller för Kubernetes tjänstadressintervall.
* Tjänsthuvudhuvudnamnet som används av AKS-klustret måste ha minst [nätverksdeltagare-rollen](../role-based-access-control/built-in-roles.md#network-contributor) i undernätet i det virtuella nätverket. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda rollen inbyggd nätverksdeltagare krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Om du vill använda Windows Server-nodpooler (för närvarande i förhandsversionen i AKS) måste du använda Azure CNI. Det går inte att använda kubenet som nätverksmodell för Windows Server-behållare.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.65 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Översikt över kubenetnätverk med ditt eget undernät

I många miljöer har du definierat virtuella nätverk och undernät med allokerade IP-adressintervall. Dessa virtuella nätverksresurser används för att stödja flera tjänster och program. För att tillhandahålla nätverksanslutning kan AKS-kluster använda *kubenet* (grundläggande nätverk) eller Azure CNI *(avancerade nätverk).*

Med *kubenet*får endast noderna en IP-adress i det virtuella nätverksundernätet. Poddar kan inte kommunicera direkt med varandra. I stället används UDR (User Defined Routing) och IP-vidarebefordran för anslutning mellan poddar över noder. Du kan också distribuera poddar bakom en tjänst som tar emot en tilldelad IP-adress och belastningsutjämnar trafik för programmet. Följande diagram visar hur AKS-noderna tar emot en IP-adress i det virtuella nätverksundernätet, men inte poddar:

![Kubenet-nätverksmodell med AKS-kluster](media/use-kubenet/kubenet-overview.png)

Azure stöder högst 400 vägar i en UDR, så du kan inte ha ett AKS-kluster som är större än 400 noder. AKS [virtual noder][virtual-nodes] och Azure Network Policies stöds inte med *kubenet*.  Du kan använda [Calico Network Policies][calico-network-policies], eftersom de stöds med kubenet.

Med *Azure CNI*tar varje pod emot en IP-adress i IP-undernätet och kan kommunicera direkt med andra poddar och tjänster. Dina kluster kan vara lika stora som det IP-adressintervall som du anger. IP-adressintervallet måste dock planeras i förväg och alla IP-adresser förbrukas av AKS-noderna baserat på det maximala antalet poddar som de kan stödja. Avancerade nätverksfunktioner och scenarier som [virtuella noder][virtual-nodes] eller nätverksprinciper (antingen Azure eller Calico) stöds med *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Tillgänglighet och utmattning av IP-adress

Med *Azure CNI*är ett vanligt problem det tilldelade IP-adressintervallet för litet för att sedan lägga till ytterligare noder när du skalar eller uppgraderar ett kluster. Nätverksteamet kanske inte heller kan utfärda ett tillräckligt stort IP-adressintervall för att stödja dina förväntade programkrav.

Som en kompromiss kan du skapa ett AKS-kluster som använder *kubenet* och ansluter till ett befintligt virtuellt nätverksundernät. Med den här metoden kan noderna ta emot definierade IP-adresser, utan att behöva reservera ett stort antal IP-adresser på framsidan för alla potentiella poddar som kan köras i klustret.

Med *kubenet*kan du använda ett mycket mindre IP-adressintervall och kunna stödja stora kluster och programkrav. Till exempel, även med ett */27* IP-adressintervall, kan du köra ett 20-25 nodkluster med tillräckligt med utrymme för att skala eller uppgradera. Den här klusterstorleken skulle stödja upp till *2 200–2 750* poddar (med högst 110 poddar per nod). Det maximala antalet poddar per nod som du kan konfigurera med *kubenet* i AKS är 110.

Följande grundläggande beräkningar jämför skillnaden i nätverksmodeller:

- **kubenet** - ett enkelt */24* IP-adressintervall kan stödja upp till *251* noder i klustret (varje Azure virtuellt nätverksundernät reserverar de tre första IP-adresserna för hanteringsåtgärder)
  - Det här antalet noder kan stödja upp till *27 610* poddar (med högst 110 kapslar per nod med *kubenet*)
- **Azure CNI** - samma grundläggande */24* undernätsområde kunde bara stödja högst *8* noder i klustret
  - Det här antalet nodor kan bara stödja upp till *240* poddar (med högst 30 poddar per nod med *Azure CNI*)

> [!NOTE]
> Dessa maximum tar inte hänsyn till uppgraderings- eller skalningsåtgärder. I praktiken kan du inte köra det maximala antalet noder som undernäts-IP-adressintervallet stöder. Du måste lämna vissa IP-adresser tillgängliga för användning under uppgraderingsskalans skala.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Virtuella nätverks peering- och ExpressRoute-anslutningar

För att tillhandahålla lokal anslutning kan både *kubenet-* och *Azure-CNI-nätverksansatser* använda [Azure-virtual network peering][vnet-peering] eller [ExpressRoute-anslutningar][express-route]. Planera ip-adressintervallen noggrant för att förhindra överlappning och felaktig trafikroutning. Många lokala nätverk använder till exempel ett *adressintervall på 10.0.0.0/8* som annonseras via ExpressRoute-anslutningen. Vi rekommenderar att du skapar AKS-kluster i Azures virtuella nätverksundernät utanför det här adressintervallet, till exempel *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Välj en nätverksmodell som ska användas

Valet av vilket nätverksinstick som ska användas för AKS-klustret är vanligtvis en balans mellan flexibilitet och avancerade konfigurationsbehov. Följande överväganden hjälper till att beskriva när varje nätverksmodell kan vara den lämpligaste.

Använd *kubenet* när:

- Du har begränsat IP-adressutrymme.
- Det mesta av pod-kommunikationen finns i klustret.
- Du behöver inte avancerade AKS-funktioner som virtuella noder eller Azure Network Policy.  Använd [Calico-nätverksprinciper][calico-network-policies].

Använd *Azure CNI* när:

- Du har tillgängligt IP-adressutrymme.
- Det mesta av pod-kommunikationen är till resurser utanför klustret.
- Du vill inte hantera UDRs.
- Du behöver avancerade AKS-funktioner som virtuella noder eller Azure Network Policy.  Använd [Calico-nätverksprinciper][calico-network-policies].

Mer information som hjälper dig att bestämma vilken nätverksmodell som ska användas finns i [Jämför nätverksmodeller och deras supportomfattning][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Om du vill komma igång med att använda *kubenet* och ditt eget virtuella nätverksundernät skapar du först en resursgrupp med kommandot [az group create.][az-group-create] I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Om du inte har ett befintligt virtuellt nätverk och undernät att använda skapar du dessa nätverksresurser med kommandot [az network vnet create.][az-network-vnet-create] I följande exempel heter det virtuella nätverket *myVnet* med adressprefixet *192.168.0.0/16*. Ett undernät skapas med namnet *myAKSSubnet* med adressprefixet *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Skapa ett tjänsthuvudnamn och tilldela behörigheter

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Tjänstens huvudnamn måste ha behörighet att hantera det virtuella nätverket och undernätet som AKS-noderna använder. Om du vill skapa ett huvudnamn för tjänsten använder du kommandot [az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Följande exempelutdata visar program-ID och lösenord för tjänstens huvudnamn. Dessa värden används i ytterligare steg för att tilldela en roll till tjänstens huvudnamn och sedan skapa AKS-klustret:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Om du vill tilldela rätt delegering i de återstående stegen använder du [az-nätverks-vnet show][az-network-vnet-show] och [az network vnet-undernät visar][az-network-vnet-subnet-show] kommandon för att hämta de resurs-ID som krävs. Dessa resurs-ID:er lagras som variabler och refereras i de återstående stegen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Tilldela nu tjänstens huvudnamn för aks-klusterdeltagarebehörigheterna i det virtuella nätverket med kommandot [az role assignment create.][az-role-assignment-create] *Contributor* Ange din egen * \<appId>* som visas i utdata från föregående kommando för att skapa tjänstens huvudnamn:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Skapa ett AKS-kluster i det virtuella nätverket

Du har nu skapat ett virtuellt nätverk och undernät och skapat och tilldelat behörigheter för ett tjänsthuvudnamn att använda dessa nätverksresurser. Skapa nu ett AKS-kluster i ditt virtuella nätverk och undernät med kommandot [az aks create.][az-aks-create] Definiera din egen * \<tjänsthuvudnamnsappId>* och * \<lösenord>*, som visas i utdata från föregående kommando för att skapa tjänstens huvudnamn.

Följande IP-adressintervall definieras också som en del av klusterskapandeprocessen:

* *--service-cidr* används för att tilldela interna tjänster i AKS-klustret en IP-adress. Det här IP-adressintervallet bör vara ett adressutrymme som inte används någon annanstans i nätverksmiljön. Det här intervallet omfattar alla lokala nätverksintervall om du ansluter eller planerar att ansluta dina virtuella Azure-nätverk med Express Route eller en VPN-anslutning från plats till plats.

* *--dns-service-IP-adressen* ska vara *.10-adressen* för tjänstens IP-adressintervall.

* *--pod-cidr* bör vara ett stort adressutrymme som inte används någon annanstans i nätverksmiljön. Det här intervallet omfattar alla lokala nätverksintervall om du ansluter eller planerar att ansluta dina virtuella Azure-nätverk med Express Route eller en VPN-anslutning från plats till plats.
    * Det här adressintervallet måste vara tillräckligt stort för att rymma antalet noder som du förväntar dig att skala upp till. Du kan inte ändra det här adressintervallet när klustret har distribuerats om du behöver fler adresser för ytterligare noder.
    * Pod-IP-adressintervallet används för att tilldela ett */24-adressutrymme* till varje nod i klustret. I följande exempel *tilldelas den* första noden *10.244.0.0/16 den första noden 10.244.0.0/24*, den andra noden *10.244.1.0/24*och den tredje noden *10.24.2.0/24*. *10.244.0.0/16*
    * När klustret skalas eller uppgraderas fortsätter Azure-plattformen att tilldela ett POD-IP-adressintervall till varje ny nod.
    
* *Med --docker-bridge-adressen* kan AKS-noderna kommunicera med den underliggande hanteringsplattformen. Den här IP-adressen får inte finnas inom klustrets virtuella nätverks-IP-adressintervall och bör inte överlappa andra adressintervall som används i nätverket.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Om du vill aktivera ett AKS-kluster för att inkludera en [Calico-nätverksprincip][calico-network-policies] kan du använda följande kommando.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

När du skapar ett AKS-kluster skapas en nätverkssäkerhetsgrupp och vägtabell. Dessa nätverksresurser hanteras av AKS-kontrollplanet. Nätverkssäkerhetsgruppen associeras automatiskt med de virtuella nätverkskorten på noderna. Flödestabellen associeras automatiskt med det virtuella nätverksundernätet. Regler för nätverkssäkerhetsgrupper och flödestabeller uppdateras automatiskt när du skapar och exponerar tjänster.

## <a name="next-steps"></a>Nästa steg

Med ett AKS-kluster distribuerat i ditt befintliga virtuella nätverksundernät kan du nu använda klustret som vanligt. Komma igång med [att skapa appar med Azure Dev Spaces][dev-spaces] eller använda [Utkast][use-draft]eller distribuera appar [med Helm][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

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
[network-comparisons]: concepts-network.md#compare-network-models
