---
title: Använda en standard-SKU-belastningsutjämnare i Azure Kubernetes service (AKS)
description: Lär dig hur du använder en belastningsutjämnare med en standard-SKU för att exponera dina tjänster med Azure Kubernetes service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9633975f53b3e398537067b17a870f621d9a7435
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045062"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Använda en standard-SKU-belastningsutjämnare i Azure Kubernetes service (AKS)

För att ge åtkomst till program via Kubernetes-tjänster av typen `LoadBalancer` i Azure Kubernetes service (AKS) kan du använda en Azure Load Balancer. En belastningsutjämnare som körs på AKS kan användas som en intern eller extern belastningsutjämnare. En intern belastningsutjämnare gör att en Kubernetes-tjänst endast är tillgänglig för program som körs i samma virtuella nätverk som AKS-klustret. En extern belastningsutjämnare tar emot en eller flera offentliga IP-adresser för ingress och gör en Kubernetes-tjänst tillgänglig externt med hjälp av offentliga IP-adresser.

Azure Load Balancer finns i två SKU: er – *Basic* och *standard*. *Standard* -SKU: n används som standard när du skapar ett AKS-kluster. Med en *standard* -SKU-belastningsutjämnare får du ytterligare funktioner och funktioner, till exempel en större storlek och Tillgänglighetszoner för Server delen. Det är viktigt att du förstår skillnaderna mellan *standard* -och *grundläggande* belastningsutjämnare innan du väljer vilken du vill använda. När du har skapat ett AKS-kluster kan du inte ändra SKU för belastnings utjämning för det klustret. Mer information om *Basic* -och *standard* -SKU: er finns i [jämförelse av Azure Load Balancer SKU][azure-lb-comparison].

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes och Azure Load Balancer koncept. Mer information finns i [Kubernetes Core Concepts for Azure Kubernetes service (AKS)][kubernetes-concepts] och [Vad är Azure Load Balancer?][azure-lb].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.0.74 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett AKS-kluster med *standard* -SKU-Azure Load Balancer. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

AKS-kluster tjänstens huvud namn behöver också behörighet att hantera nätverks resurser om du använder ett befintligt undernät eller en befintlig resurs grupp. I allmänhet tilldelar du rollen *nätverks deltagare* till tjänstens huvud namn på de delegerade resurserna. Mer information om behörigheter finns i [delegera AKS-åtkomst till andra Azure-resurser][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Flytta från en Basic SKU-Load Balancer till standard-SKU: n

Om du har ett befintligt kluster med Basic-SKU Load Balancer, finns det viktiga skillnader att tänka på när du migrerar till att använda ett kluster med standard-SKU: n Load Balancer.

Om du till exempel gör blå/gröna distributioner för att migrera kluster, är en vanlig metod att `load-balancer-sku` typen för ett kluster endast kan definieras i klustrets skapande tid. *Basic SKU* -belastningsutjämnare använder dock *Basic SKU* IP-adresser som inte är kompatibla med *standardsku* : er för SKU: er, eftersom de kräver *standard-SKU* IP-adresser. När du migrerar kluster för att uppgradera Load Balancer SKU: er krävs en ny IP-adress med en kompatibel IP-SKU.

Mer information om hur du migrerar kluster finns i [vår dokumentation om migrering](acs-aks-migration.md) för att se en lista över viktiga ämnen som du bör tänka på när du migrerar. Begränsningarna nedan är också viktiga skillnader när du antecknar när du använder standard-SKU: er i AKS.

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder en belastningsutjämnare med *standard* -SKU: n:

* Minst en offentlig IP-adress eller ett IP-prefix krävs för att tillåta utgående trafik från AKS-klustret. Det offentliga IP-eller IP-prefixet krävs också för att upprätthålla anslutningen mellan kontroll planet och agent-noder och för att upprätthålla kompatibilitet med tidigare versioner av AKS. Du kan välja mellan följande alternativ för att ange offentliga IP-adresser eller IP-prefix med en *standard* -SKU-belastningsutjämnare:
    * Ange dina egna offentliga IP-adresser.
    * Ange egna offentliga IP-prefix.
    * Ange ett tal upp till 100 för att tillåta att AKS-klustret skapar att många *standard* -SKU offentliga IP-adresser i samma resurs grupp som skapats som AKS-kluster, som vanligt vis heter med *MC_* i början. AKS tilldelar den offentliga IP-adressen till *standard* -SKU-belastningsutjämnaren. Som standard skapas en offentlig IP-adress automatiskt i samma resurs grupp som AKS-klustret, om ingen offentlig IP, ett offentligt IP-prefix eller antal IP-adresser anges. Du måste också tillåta offentliga adresser och undvika att skapa Azure Policy som tillåter att IP skapas.
* När du använder *standard* -SKU: n för en belastningsutjämnare måste du använda Kubernetes version *1,13 eller senare*.
* Du kan bara definiera belastningsutjämnare-SKU: n när du skapar ett AKS-kluster. Du kan inte ändra SKU: n för belastningsutjämnaren efter att ett AKS-kluster har skapats.
* Du kan bara använda en typ av SKU för belastningsutjämnare (Basic eller standard) i ett enda kluster.
* *Standard* SKU load Balances stöder endast IP-adresser för *standard* -SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Använd *standard* -SKU: n för belastningsutjämnare

När du skapar ett AKS-kluster används *standard* -SKU: n som standard när du kör tjänster i klustret. [Snabb starten med Azure CLI][aks-quickstart-cli] distribuerar till exempel ett exempel program som använder *standard* -SKU-belastningsutjämnaren. 

## <a name="configure-the-load-balancer-to-be-internal"></a>Konfigurera belastningsutjämnaren så att den är intern

Du kan också konfigurera belastningsutjämnaren så att den är intern och inte exponera en offentlig IP-adress. Om du vill konfigurera belastningsutjämnaren som intern lägger du till `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` som en anteckning till *Loadbalancer* -tjänsten. Du kan se ett exempel på yaml-manifest och mer information om en intern belastningsutjämnare [här][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Skala antalet hanterade offentliga IP-adresser

När du använder en *standard* -SKU för belastningsutjämnare med hanterade utgående offentliga IP-adresser, som skapas som standard, kan du skala antalet hanterade utgående offentliga IP-adresser med hjälp av parametern *Load-Balancer-Managed-IP-Count* .

Kör följande kommando för att uppdatera ett befintligt kluster. Den här parametern kan också ställas in i klustret Create-Time för att ha flera hanterade utgående offentliga IP-adresser.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Exemplet ovan anger antalet hanterade utgående offentliga IP-adresser till *2* för *myAKSCluster* -klustret i *myResourceGroup*. 

Du kan också använda parametern *belastningsutjämnare-hanterade-IP-antal* för att ange det ursprungliga antalet hanterade offentliga IP-adresser när du skapar klustret genom att lägga till `--load-balancer-managed-outbound-ip-count`-parametern och ange det till önskat värde. Standardvärdet för hanterade utgående offentliga IP-adresser är 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Ange egna offentliga IP-adresser eller prefix för utgående trafik

När du använder en *standard* -SKU-BELASTNINGSUTJÄMNARE skapar AKS-klustret automatiskt en offentlig IP-adress i samma resurs grupp som skapats för AKS-klustret och tilldelar den offentliga IP-adressen till *standard* -SKU-belastningsutjämnaren. Alternativt kan du tilldela din egen offentliga IP-adress när klustret skapas, eller så kan du uppdatera ett befintligt klusters egenskaper för belastningsutjämnare.

Genom att ta med flera IP-adresser eller prefix kan du definiera flera tjänster för säkerhets kopiering när du definierar IP-adressen bakom ett enda belastnings Utjämnings objekt. Den utgående slut punkten för vissa noder beror på vilken tjänst de är associerade med.

> [!IMPORTANT]
> Du måste använda *standard* SKU offentliga IP: er för utgående med din *standard* -SKU. Du kan kontrol lera SKU: er för dina offentliga IP-adresser med hjälp av kommandot [AZ Network Public-IP show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Använd kommandot [AZ Network Public-IP show][az-network-public-ip-show] för att visa ID: n för dina offentliga IP-adresser.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Kommandot ovan visar ID: t för *myPublicIP* offentliga IP i resurs gruppen *myResourceGroup* .

Använd kommandot *AZ AKS Update* med parametern *Load-Balancer-utgående-IP* för att uppdatera ditt kluster med dina offentliga IP-adresser.

I följande exempel används parametern *Load-Balancer-utgående-IP* med ID: n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Du kan också använda offentliga IP-prefix för utgående trafik med SKU: n för *standard* -SKU. I följande exempel används kommandot [AZ Network Public-IP prefixet show][az-network-public-ip-prefix-show] för att visa en lista med ID: n för dina offentliga IP-prefix:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Kommandot ovan visar ID: t för det offentliga IP-prefixet *myPublicIPPrefix* i resurs gruppen *myResourceGroup* .

I följande exempel används parametern *Load-Balancer-utgående-IP-prefix* med ID: n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> De offentliga IP-adresserna och IP-prefixen måste finnas i samma region och ingå i samma prenumeration som ditt AKS-kluster. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definiera en egen offentlig IP-adress eller prefix i klustrets skapande tid

Du kanske vill ta med dina egna IP-adresser eller IP-prefix för utgående trafik när klustret skapas för att ge stöd för scenarier som vit listning utgående slut punkter. Lägg till samma parametrar som visas ovan i steget Skapa kluster för att definiera egna offentliga IP-adresser och IP-prefix i början av ett klusters livs cykel.

Använd kommandot *AZ AKS Create* med parametern *Load-Balancer-utgående-IP* för att skapa ett nytt kluster med dina offentliga IP-adresser vid start.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Använd kommandot *AZ AKS Create* med parametern *Load-Balancer-utgående-IP-prefix* för att skapa ett nytt kluster med dina offentliga IP-prefix vid starten.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>Visa utgående regel för belastningsutjämnaren

Om du vill visa den utgående regeln som skapats i belastningsutjämnaren använder du [AZ Network lb utgående regel List][az-network-lb-outbound-rule-list] och anger resurs gruppen för AKS för ditt-kluster:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Föregående kommandon visar utgående regel för belastningsutjämnaren, till exempel:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

I exempel resultatet är *AllocatedOutboundPorts* 0. Värdet för *AllocatedOutboundPorts* innebär att SNAT-port tilldelningen återgår till automatisk tilldelning baserat på storleken på backend-poolen. Mer information finns i [Load Balancer utgående regler][azure-lb-outbound-rules] och [utgående anslutningar i Azure][azure-lb-outbound-connections] .

## <a name="restrict-access-to-specific-ip-ranges"></a>Begränsa åtkomsten till vissa IP-intervall

Nätverks säkerhets gruppen (NSG) som är associerad med det virtuella nätverket för belastningsutjämnaren har som standard en regel för att tillåta all inkommande extern trafik. Du kan uppdatera den här regeln för att endast tillåta vissa IP-intervall för inkommande trafik. I följande manifest används *loadBalancerSourceRanges* för att ange ett nytt IP-intervall för inkommande extern trafik:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

Exemplet ovan uppdaterar regeln för att endast tillåta inkommande extern trafik från *MY_EXTERNAL_IP_RANGEs* intervallet. Mer information om hur du använder den här metoden för att begränsa åtkomsten till belastningsutjämnaren finns i [Kubernetes-dokumentationen][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes Services i [dokumentationen för Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
