---
title: Konfigurera Azure CNI-nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du konfigurerar Azure CNI-nätverk (avancerat) i Azure Kubernetes Service (AKS), inklusive distribution av ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 6f194cb97850fcb24e4789ac0ba39b6f03d99e6e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617386"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurera Azure CNI-nätverk i Azure Kubernetes Service (AKS)

Som standard använder AKS-kluster [kubenet][kubenet]och ett virtuellt nätverk och undernät skapas åt dig. Med *kubenet*får noder en IP-adress från ett virtuellt nätverksundernät. Nat (Network Address Translation) konfigureras sedan på noderna och poddar får en IP-adress "dold" bakom nod-IP-adressen. Den här metoden minskar antalet IP-adresser som du behöver reservera i nätverksutrymmet för poddar som ska användas.

Med [CNI (Azure Container Networking Interface)][cni-networking]får varje pod en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unika i hela nätverksutrymmet och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som den stöder. Motsvarande antal IP-adresser per nod reserveras sedan på framsidan för den noden. Den här metoden kräver mer planering och leder ofta till IP-adressutmattning eller behovet av att återskapa kluster i ett större undernät när programmets krav växer.

Den här artikeln visar hur du använder *Azure CNI-nätverk* för att skapa och använda ett virtuellt nätverksundernät för ett AKS-kluster. Mer information om nätverksalternativ och överväganden finns i [Nätverksbegrepp för Kubernetes och AKS][aks-network-concepts].

## <a name="prerequisites"></a>Krav

* Det virtuella nätverket för AKS-klustret måste tillåta utgående internetanslutning.
* AKS-kluster får `169.254.0.0/16`inte `172.30.0.0/16` `172.31.0.0/16`använda `192.0.2.0/24` , , eller för Kubernetes tjänstadressintervall.
* Tjänsthuvudhuvudnamnet som används av AKS-klustret måste ha minst [behörigheten Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor) i undernätet i det virtuella nätverket. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda rollen inbyggd nätverksdeltagare krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* I stället för ett tjänsthuvudnamn kan du använda den systemtilldelade hanterade identiteten för behörigheter. Mer information finns i [Använda hanterade identiteter](use-managed-identity.md).
* Undernätet som tilldelats AKS-nodpoolen kan inte vara ett [delegerat undernät](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Planera IP-adressering för ditt kluster

Kluster som konfigurerats med Azure CNI-nätverk kräver ytterligare planering. Storleken på det virtuella nätverket och dess undernät måste ta hänsyn till antalet poddar som du planerar att köra och antalet noder för klustret.

IP-adresser för poddar och klustrets noder tilldelas från det angivna undernätet i det virtuella nätverket. Varje nod är konfigurerad med en primär IP-adress. Som standard är 30 ytterligare IP-adresser förkonfigurerade av Azure CNI som har tilldelats poddar som schemalagts på noden. När du skalar ut klustret konfigureras varje nod på samma sätt med IP-adresser från undernätet. Du kan också visa de [maximala poddar per nod](#maximum-pods-per-node).

> [!IMPORTANT]
> Antalet IP-adresser som krävs bör innehålla överväganden för uppgradering och skalning. Om du anger att IP-adressintervallet bara stöder ett fast antal noder kan du inte uppgradera eller skala klustret.
>
> - När du **uppgraderar** AKS-klustret distribueras en ny nod i klustret. Tjänster och arbetsbelastningar börjar köras på den nya noden och en äldre nod tas bort från klustret. Den här rullande uppgraderingsprocessen kräver minst ett ytterligare block med IP-adresser för att vara tillgängliga. Din nodräkning `n + 1`är då .
>   - Det här övervägandet är särskilt viktigt när du använder Windows Server-nodpooler (för närvarande i förhandsversion i AKS). Windows Server-noder i AKS installerar inte Windows-uppdateringar automatiskt, utan du utför en uppgradering på nodpoolen. Den här uppgraderingen distribuerar nya noder med den senaste Windows Server 2019-basnodavbildningen och säkerhetskorrigeringarna. Mer information om hur du uppgraderar en Windows Server-nodpool finns [i Uppgradera en nodpool i AKS][nodepool-upgrade].
>
> - När du **skalar** ett AKS-kluster distribueras en ny nod i klustret. Tjänster och arbetsbelastningar börjar köras på den nya noden. Ditt IP-adressintervall måste ta hänsyn till hur du kanske vill skala upp antalet noder och poddar som klustret kan stödja. Ytterligare en nod för uppgraderingsåtgärder bör också inkluderas. Din nodräkning `n + number-of-additional-scaled-nodes-you-anticipate + 1`är då .

Om du förväntar dig att dina noder ska köra det maximala antalet poddar och regelbundet förstöra och distribuera poddar, bör du också ta hänsyn till några ytterligare IP-adresser per nod. Dessa ytterligare IP-adresser tar hänsyn till det kan ta några sekunder för en tjänst som ska tas bort och IP-adressen släpps för en ny tjänst som ska distribueras och hämta adressen.

IP-adressplanen för ett AKS-kluster består av ett virtuellt nätverk, minst ett undernät för noder och poddar och ett Kubernetes-tjänstadressintervall.

| Adressintervall / Azure-resurs | Gränser och storlek |
| --------- | ------------- |
| Virtuellt nätverk | Det virtuella Azure-nätverket kan vara lika stort som /8, men är begränsat till 65 536 konfigurerade IP-adresser. |
| Undernät | Måste vara tillräckligt stor för att rymma noder, poddar och alla Kubernetes- och Azure-resurser som kan etableras i klustret. Om du till exempel distribuerar en intern Azure Load Balancer allokeras dess frontend-IPs från klustrets undernät, inte offentliga IPs. Undernätsstorleken bör också ta hänsyn till uppgraderingsåtgärder eller framtida skalningsbehov.<p />Så här beräknar du den *minsta* undernätsstorleken inklusive ytterligare en nod för uppgraderingsåtgärder:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exempel för ett 50 `(51) + (51  * 30 (default)) = 1,581` nodkluster: (/21 eller större)<p/>Exempel för ett 50 nodkluster som även innehåller etablering `(61) + (61 * 30 (default)) = 1,891` för att skala upp ytterligare 10 noder: (/21 eller större)<p>Om du inte anger ett maximalt antal poddar per nod när du skapar klustret anges det maximala antalet poddar per nod till *30*. Det minsta antalet IP-adresser som krävs baseras på det värdet. Om du beräknar dina minimikrav på IP-adress på ett annat högsta värde läser du [hur du konfigurerar det maximala antalet poddar per nod](#configure-maximum---new-clusters) för att ange det här värdet när du distribuerar klustret. |
| Kubernetes Service-adressintervall | Det här intervallet bör inte användas av något nätverkselement på eller ansluten till det här virtuella nätverket. Serviceadressen CIDR måste vara mindre än /12. Du kan återanvända det här intervallet över olika AKS-kluster. |
| IP-adress för Kubernetes DNS-tjänst | IP-adress inom kubernetes tjänstadressintervall som ska användas av identifiering av klustertjänst (kube-dns). Använd inte den första IP-adressen i adressintervallet, till exempel 0,1. Den första adressen i undernätet används för adressen *kubernetes.default.svc.cluster.local.* |
| Docker-bryggadress | Docker-bryggnätverksadressen representerar den standardmässiga *docker0*-bryggnätverksadress som finns i alla Docker-installationer. *Docker0-brygga* används inte av AKS-kluster eller poddar själva, men du måste ange den här adressen så att den fortsätter att stödja scenarier som *docker build* inom AKS-klustret. Det krävs för att välja en CIDR för Docker-bryggnätsadressen eftersom Docker annars väljer ett undernät automatiskt som kan stå i konflikt med andra CIDRs. Du måste välja ett adressutrymme som inte kolliderar med resten av CIDR:erna i dina nätverk, inklusive klustrets tjänst CIDR och pod CIDR. Standard på 172.17.0.1/16. Du kan återanvända det här intervallet över olika AKS-kluster. |

## <a name="maximum-pods-per-node"></a>Maximal poddar per nod

Det maximala antalet poddar per nod i ett AKS-kluster är 250. Standard *maximalt* antal poddar per nod varierar mellan *kubenet-* och *Azure CNI-nätverk* och metoden för klusterdistribution.

| Distributionsmetod | Kubenet standard | Standard för Azure CNI | Konfigurerbar vid distribution |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ja (upp till 250) |
| Resource Manager-mall | 110 | 30 | Ja (upp till 250) |
| Portalen | 110 | 30 | Inga |

### <a name="configure-maximum---new-clusters"></a>Konfigurera maximalt - nya kluster

Du kan bara konfigurera det maximala antalet poddar per nod *vid klusterdistributionstid*. Om du distribuerar med Azure CLI eller med en Resource Manager-mall kan du ange det maximala poddar per nodvärde så högt som 250.

Ett lägsta värde för maximala poddar per nod tillämpas för att garantera utrymme för systemkapslar som är viktiga för klusterhälsan. Det lägsta värde som kan ställas in för maximal pods per nod är 10 om och endast om konfigurationen av varje nodpool har plats för minst 30 poddar. Om du till exempel anger de maximala poddarna per nod till minst 10 krävs att varje enskild nodpool har minst 3 noder. Det här kravet gäller även för varje ny nodpool som skapats, så om 10 definieras som maximal poddar per nod måste varje efterföljande nodpool ha minst 3 noder.

| Nätverk | Minimum | Maximal |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet (på) | 10 | 110 |

> [!NOTE]
> Minimivärdet i tabellen ovan tillämpas strikt av AKS-tjänsten. Du kan inte ställa in ett maxPods-värde som är lägre än det minsta som visas som gör det kan hindra klustret från att starta.

* **Azure CLI**: `--max-pods` Ange argumentet när du distribuerar ett kluster med kommandot [az aks create.][az-aks-create] Det maximala värdet är 250.
* **Resurshanterarens mall** `maxPods` : Ange egenskapen i [Objektet ManagedClusterAgentPoolProfile] när du distribuerar ett kluster med en Resource Manager-mall. Det maximala värdet är 250.
* **Azure-portal:** Du kan inte ändra det maximala antalet poddar per nod när du distribuerar ett kluster med Azure-portalen. Azure CNI-nätverkskluster är begränsade till 30 poddar per nod när du distribuerar med Azure-portalen.

### <a name="configure-maximum---existing-clusters"></a>Konfigurera maximalt - befintliga kluster

Du kan inte ändra den maximala podden per nod i ett befintligt AKS-kluster. Du kan bara justera numret när du distribuerar klustret.

## <a name="deployment-parameters"></a>Distributionsparametrar

När du skapar ett AKS-kluster kan följande parametrar konfigureras för Azure CNI-nätverk:

**Virtuellt nätverk:** Det virtuella nätverk som du vill distribuera Kubernetes-klustret till. Om du vill skapa ett nytt virtuellt nätverk för klustret väljer du *Skapa nytt* och följer stegen i avsnittet *Skapa virtuellt nätverk.* Information om begränsningar och kvoter för ett virtuellt Azure-nätverk finns i [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Undernät**: Undernätet i det virtuella nätverket där du vill distribuera klustret. Om du vill skapa ett nytt undernät i det virtuella nätverket för klustret väljer du *Skapa nytt* och följer stegen i avsnittet *Skapa undernät.* För hybridanslutning bör adressintervallet inte överlappa med andra virtuella nätverk i din miljö.

**Kubernetes tjänstadressintervall:** Det här är den uppsättning virtuella IP-adresser som Kubernetes tilldelar interna [tjänster][services] i klustret. Du kan använda ett privat adressintervall som uppfyller följande krav:

* Får inte finnas inom klustrets virtuella nätverks-IP-adressintervall
* Får inte överlappa med andra virtuella nätverk som klustret virtuella nätverk peers
* Får inte överlappa med lokala IPs
* Får inte ligga inom `169.254.0.0/16` `172.30.0.0/16`intervallen , , `172.31.0.0/16`eller`192.0.2.0/24`

Även om det är tekniskt möjligt att ange ett tjänstadressintervall inom samma virtuella nätverk som klustret, rekommenderas inte detta. Oförutsägbart beteende kan uppstå om överlappande IP-intervall används. Mer information finns i avsnittet [Vanliga frågor](#frequently-asked-questions) och svar i den här artikeln. Mer information om Kubernetes-tjänster finns i [Tjänster][services] i Kubernetes-dokumentationen.

**Kubernetes DNS-tjänst IP-adress:** IP-adressen för klustrets DNS-tjänst. Den här adressen måste ligga inom *Kubernetes Service-adressintervallet*. Använd inte den första IP-adressen i adressintervallet, till exempel 0,1. Den första adressen i undernätet används för adressen *kubernetes.default.svc.cluster.local.*

**Docker Bridge-adress**: Docker-bryggnätsadressen representerar *standardåtkomstens* bryggnätsadress som finns i alla Docker-installationer. *Docker0-brygga* används inte av AKS-kluster eller poddar själva, men du måste ange den här adressen så att den fortsätter att stödja scenarier som *docker build* inom AKS-klustret. Det krävs för att välja en CIDR för Docker-bryggnätsadressen eftersom Docker annars väljer ett undernät automatiskt som kan stå i konflikt med andra CIDRs. Du måste välja ett adressutrymme som inte kolliderar med resten av CIDR:erna i dina nätverk, inklusive klustrets tjänst CIDR och pod CIDR.

## <a name="configure-networking---cli"></a>Konfigurera nätverk - CLI

När du skapar ett AKS-kluster med Azure CLI kan du också konfigurera Azure CNI-nätverk. Använd följande kommandon för att skapa ett nytt AKS-kluster med Azure CNI-nätverk aktiverat.

Hämta först undernätsresurs-ID:t för det befintliga undernätet som AKS-klustret ska anslutas till:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Använd kommandot [az aks][az-aks-create] `--network-plugin azure` create med argumentet för att skapa ett kluster med avancerade nätverk. Uppdatera `--vnet-subnet-id` värdet med det id-kort i undernät som samlats in i föregående steg:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Konfigurera nätverk – portal

Följande skärmbild från Azure-portalen visar ett exempel på att konfigurera dessa inställningar när AKS-kluster skapas:

![Avancerad nätverkskonfiguration i Azure-portalen][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Följande frågor och svar gäller för Azure CNI-nätverkskonfigurationen. **Azure CNI**

* *Kan jag distribuera virtuella datorer i mitt klusterundernät?*

  Nej. Det går inte att distribuera virtuella datorer i undernätet som används av Kubernetes-klustret. Virtuella datorer kan distribueras i samma virtuella nätverk, men i ett annat undernät.

* *Kan jag konfigurera nätverksprinciper per pod?*

  Ja, Kubernetes nätverksprincip är tillgänglig i AKS. Information om hur du kommer igång finns i [Säker trafik mellan poddar med hjälp av nätverksprinciper i AKS][network-policy].

* *Kan det maximala antalet poddar distribueras till en nod som kan konfigureras?*

  Ja, när du distribuerar ett kluster med Azure CLI eller en Resource Manager-mall. Se [Maximalt antal poddar per nod](#maximum-pods-per-node).

  Du kan inte ändra det maximala antalet poddar per nod i ett befintligt kluster.

* *Hur konfigurerar jag ytterligare egenskaper för undernätet som jag skapade när AKS-kluster skapades? Till exempel tjänstslutpunkter.*

  Den fullständiga listan över egenskaper för det virtuella nätverket och undernäten som du skapar under AKS-klusterskapande kan konfigureras på standardsidan för konfiguration av virtuella nätverk i Azure-portalen.

* *Kan jag använda ett annat undernät i mitt virtuella klusternätverk för* **Kubernetes tjänstadressintervall?**

  Det rekommenderas inte, men den här konfigurationen är möjlig. Tjänstadressintervallet är en uppsättning virtuella VIP-adresser som Kubernetes tilldelar interna tjänster i klustret. Azure Networking har ingen insyn i tjänstens IP-intervall för Kubernetes-klustret. På grund av bristen på insyn i klustrets tjänstadressintervall är det möjligt att senare skapa ett nytt undernät i klustrets virtuella nätverk som överlappar serviceadressintervallet. Om en sådan överlappning inträffar kan Kubernetes tilldela en tjänst en IP som redan används av en annan resurs i undernätet, vilket orsakar oförutsägbara beteenden eller fel. Genom att se till att du använder ett adressintervall utanför klustrets virtuella nätverk kan du undvika den här överlappningsrisken.

## <a name="next-steps"></a>Nästa steg

Läs mer om nätverk i AKS i följande artiklar:

- [Använda en statisk IP-adress med Azure Kubernetes Service (AKS) belastningsutjämnare](static-ip.md)
- [Använda en intern belastningsutjämnare med Azure Container Service (AKS)](internal-lb.md)

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- [Skapa en ingående styrenhet med en dynamisk offentlig IP och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingående styrenhet med en statisk offentlig IP och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS-motor

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] är ett projekt med öppen källkod som genererar Azure Resource Manager-mallar som du kan använda för att distribuera Kubernetes-kluster på Azure.

Kubernetes-kluster som skapats med AKS Engine stöder både [kubenet-][kubenet] och [Azure CNI-plugins.][cni-networking] Därför stöds båda nätverksscenarierna av AKS Engine.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
