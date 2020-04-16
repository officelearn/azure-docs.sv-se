---
title: Använda en standardbelastningsutjämnare för SKU
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder en belastningsutjämnare med en Standard SKU för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c1d2c0e48394fbde1b595ae4b405d84f437dc5e4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392819"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Använda en standardbelastningsutjämnarvägning i Azure Kubernetes Service (AKS)

Om du vill ge åtkomst till program `LoadBalancer` via Kubernetes-tjänster av typen Azure Kubernetes Service (AKS) kan du använda en Azure Load Balancer. En belastningsutjämnare som körs på AKS kan användas som intern eller extern belastningsutjämnare. En intern belastningsutjämnare gör en Kubernetes-tjänst endast tillgänglig för program som körs i samma virtuella nätverk som AKS-klustret. En extern belastningsutjämnare tar emot en eller flera offentliga IPs för inträngning och gör en Kubernetes-tjänst tillgänglig externt med hjälp av offentliga IPs.

Azure Load Balancer är tillgängligt i två SKU : *- Basic* och *Standard*. Som standard *Standard* används standard-SKU när du skapar ett AKS-kluster. Med hjälp av en *Standard* SKU-belastningsutjämnare finns ytterligare funktioner och funktioner, till exempel en större backend-poolstorlek och tillgänglighetszoner. Det är viktigt att du förstår skillnaderna mellan *standard-* och *grundläggande* belastningsutjämnare innan du väljer vilken du vill använda. När du har skapat ett AKS-kluster kan du inte ändra belastningsutjämnaren SKU för det klustret. Mer information om *basic-* och standardsaku:erna finns i [Jämförelse med Azure load balancer SKU][azure-lb-comparison]. *Standard*

Den här artikeln förutsätter en grundläggande förståelse av kubernetes och Azure Load Balancer begrepp. Mer information finns i [Kubernetes grundläggande begrepp för Azure Kubernetes Service (AKS)][kubernetes-concepts] och [Vad är Azure Load Balancer?][azure-lb].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0.81 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett AKS-kluster med *Standard* SKU Azure Load Balancer. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Huvudmannen för AKS-klustertjänsten behöver också behörighet att hantera nätverksresurser om du använder ett befintligt undernät eller en befintlig resursgrupp. I allmänhet tilldelar du rollen *Nätverksdeltagare* till tjänsthuvudhuvudet på de delegerade resurserna. I stället för ett tjänsthuvudnamn kan du också använda den systemtilldelade hanterade identiteten för behörigheter. Mer information finns i [Använda hanterade identiteter](use-managed-identity.md). Mer information om behörigheter finns i [Delegera AKS-åtkomst till andra Azure-resurser][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Flytta från en grundläggande SKU-belastningsutjämning till standard-SKU

Om du har ett befintligt kluster med den grundläggande SKU-belastningsutjämningstagaren finns det viktiga beteendeskillnader att notera när du migrerar för att använda ett kluster med Standard SKU Load Balancer.

Att till exempel göra blå/gröna distributioner för att migrera `load-balancer-sku` kluster är vanligt med tanke på vilken typ av kluster som bara kan definieras vid klusterskapande tid. Grundläggande *SKU-belastningsutjämning* använder dock *grundläggande SKU* IP-adresser som inte är kompatibla med *Standard SKU* Load Balancers eftersom de kräver *standard SKU* IP-adresser. När kluster migreras för att uppgradera SKU:er för belastningsutjämning krävs en ny IP-adress med en kompatibel IP-adress SKU.

Mer information om hur du migrerar kluster finns i [vår dokumentation om migreringsöverväganden](aks-migration.md) och visa en lista över viktiga ämnen att tänka på när du migrerar. Nedanstående begränsningar är också viktiga beteendemässiga skillnader att notera när du använder Standard SKU Load Balancers i AKS.

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som *Standard* stöder en belastningsutjämnare med standard-SKU:

* Minst ett offentligt IP- eller IP-prefix krävs för att tillåta utgående trafik från AKS-klustret. Det offentliga IP- eller IP-prefixet krävs också för att upprätthålla anslutningen mellan kontrollplanet och agentnoder samt för att upprätthålla kompatibilitet med tidigare versioner av AKS. Du har följande alternativ för att ange offentliga IP-adresser eller IP-prefix med en *standard* SKU-belastningsutjämnare:
    * Ge dina egna offentliga IPs.
    * Ange dina egna offentliga IP-prefix.
    * Ange ett tal på upp till 100 så att AKS-klustret kan skapa så många offentliga standard-SKU-IP-adresser i samma resursgrupp som skapats som AKS-klustret, som vanligtvis namnges med *MC_* i början. *Standard* AKS tilldelar den offentliga *Standard* IP-adressen till standard-SKU-belastningsutjämnaren. Som standard skapas en offentlig IP automatiskt i samma resursgrupp som AKS-klustret, om ingen offentlig IP, offentligt IP-prefix eller antal IP-adresser har angetts. Du måste också tillåta offentliga adresser och undvika att skapa Azure-princip som förbjuder IP-skapande.
* När du *Standard* använder standard-SKU:n för en belastningsutjämnare måste du använda Kubernetes version *1.13 eller senare*.
* Definiera belastningsutjämnaren SKU kan bara göras när du skapar ett AKS-kluster. Du kan inte ändra belastningsutjämnar-SKU:n när ett AKS-kluster har skapats.
* Du kan bara använda en typ av belastningsutjämnare SKU (Basic eller Standard) i ett enda kluster.
* *Standard* SKU Belastningsutjämnare stöder endast *standard* SKU IP-adresser.

## <a name="use-the-standard-sku-load-balancer"></a>Använd *standardlastbalansen* för SKU

När du skapar ett AKS-kluster *Standard* används som standard SKU-belastningsutjämnaren som standard när du kör tjänster i klustret. [Snabbstart med Azure CLI][aks-quickstart-cli] distribuerar till exempel ett exempelprogram som använder standard-SKU-belastningsutjämnaren. *Standard*

> [!IMPORTANT]
> Offentliga IP-adresser kan undvikas genom att anpassa en användardefinierad väg (UDR). Ange ett AKS-klusters utgående typ som UDR kan hoppa över IP-etablering och serverdelspoolinställningar för AKS-skapade Azure-belastningsutjämnar. Se [ställa in `outboundType` ett kluster till 'userDefinedRouting'.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>Konfigurera belastningsutjämnaren så att den är intern

Du kan också konfigurera belastningsutjämnaren så att den är intern och inte exponera en offentlig IP. Om du vill konfigurera belastningsutjämnaren som intern lägger du till `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` som en anteckning till *LoadBalancer-tjänsten.* Du kan se ett exempel yaml manifest samt mer information om en intern belastningsutjämnare [här][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Skala antalet hanterade offentliga IPs

När du använder en *Standard* SKU-belastningsutjämnare med hanterade utgående offentliga IP-adresser, som skapas som standard, kan du skala antalet hanterade utgående offentliga IP-adresser med hjälp av parametern *load-balancer-managed-ip-count.*

Om du vill uppdatera ett befintligt kluster kör du följande kommando. Den här parametern kan också ställas in vid klusterskapande tid för att ha flera hanterade utgående offentliga IPs.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

I exemplet ovan anges antalet hanterade utgående offentliga IPs till *2* för *myAKSCluster-klustret* i *myResourceGroup*. 

Du kan också använda parametern *load-balancer-managed-ip-count* för att ange det ursprungliga antalet hanterade `--load-balancer-managed-outbound-ip-count` utgående offentliga IP-adresser när du skapar klustret genom att lägga till parametern och ange den till önskat värde. Standardantalet hanterade utgående offentliga IPs är 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Tillhandahålla egna offentliga IPs eller prefix för utgående

När du använder en *Standard* SKU-belastningsutjämnare skapar AKS-klustret automatiskt en offentlig IP i samma resursgrupp som skapats för AKS-klustret och tilldelar den offentliga IP-adressen till *Standard* SKU-belastningsutjämnaren. Du kan också tilldela din egen offentliga IP vid skapande av kluster eller uppdatera ett befintligt klusters belastningsutjämnareegenskaper.

Genom att ta med flera IP-adresser eller prefix kan du definiera flera stödtjänster när du definierar IP-adressen bakom ett enda belastningsutjämnat objekt. Slutpunkten för utgående av specifika noder beror på vilken tjänst de är associerade med.

> [!IMPORTANT]
> Du måste *använda* vanliga vanliga SKU-IPS för att gå ut med *Standard* standard-SKU-enhetens belastningsutjämnare. Du kan verifiera SKU:n för dina offentliga IP-adresser med kommandot [az network public-ip show:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Använd kommandot [az network public-ip show][az-network-public-ip-show] för att lista ID:na för dina offentliga IP-adresser.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Kommandot ovan visar ID:et för *myPublicIP* public IP i resursgruppen *myResourceGroup.*

Använd kommandot *az aks update* med parametern *load-balancer-outbound-ips* för att uppdatera klustret med dina offentliga IPs.

I följande exempel används parametern *load-balancer-outbound-ips* med ID:na från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Du kan också använda offentliga IP-prefix för utgående med *standard* SKU-belastningsutjämnaren. I följande exempel används kommandot [az network public-ip prefix visa][az-network-public-ip-prefix-show] för att lista ID:erna för dina offentliga IP-prefix:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Kommandot Ovan visar ID:et för den offentliga IP-prefixet *myPublicIPPrefix* i resursgruppen *myResourceGroup.*

I följande exempel används parametern *load-balancer-outbound-ip-prefix* med ID:na från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> De offentliga IP-adresserna och IP-prefixen måste finnas i samma region och en del av samma prenumeration som AKS-klustret. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definiera dina egna offentliga IP- eller prefix vid klusterskapande tid

Du kanske vill ta med egna IP-adresser eller IP-prefix för utgående vid klusterskapande tid för att stödja scenarier som vitlistning av utgående slutpunkter. Lägg till samma parametrar som visas ovan i steget att skapa kluster för att definiera dina egna offentliga IP-adresser och IP-prefix i början av ett klusters livscykel.

Använd kommandot *az aks create* med parametern *load-balancer-outbound-ips* för att skapa ett nytt kluster med dina offentliga IPs i början.

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

Använd kommandot *az aks create* med parametern *load-balancer-outbound-ip-prefix* för att skapa ett nytt kluster med dina offentliga IP-prefix i början.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Konfigurera utgående portar och tidsgränsen för inaktivitet

> [!WARNING]
> Följande avsnitt är avsett för avancerade scenarier med större nätverk eller för att åtgärda SNAT-utmattningsproblem med standardkonfigurationerna. Du måste ha en korrekt inventering av tillgänglig kvot för virtuella datorer och IP-adresser innan du ändrar *AllocatedOutboundPorts* eller *IdleTimeoutInMinutes* från deras standardvärde för att upprätthålla felfria kluster.
> 
> Om du ändrar värdena för *AllocatedOutboundPorts* och *IdleTimeoutInMinutes* kan beteendet för den utgående regeln för lastbalansen ändras avsevärt. Granska [reglerna för utgående belastningsutjämnare,][azure-lb-outbound-rules-overview] [belastningsutjämningsutgående regler][azure-lb-outbound-rules]och [utgående anslutningar i Azure][azure-lb-outbound-connections] innan du uppdaterar dessa värden för att till fullo förstå effekten av dina ändringar.

Utgående allokerade portar och deras inaktiva tidsutgångar används för [SNAT][azure-lb-outbound-connections]. Som standard använder *Standard* SKU-belastningsutjämnaren [automatisk tilldelning för antalet utgående portar baserat på serverdelspoolstorlek][azure-lb-outbound-preallocatedports] och en 30-minuters tidsutgång för inaktivitet för varje port. Om du vill se dessa värden använder du [az network lb outbound-rule list][az-network-lb-outbound-rule-list] för att visa den utgående regeln för belastningsutjämnaren:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

De tidigare kommandona listar den utgående regeln för din belastningsutjämnare, till exempel:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Exempelutdata visar standardvärdet för *AllocatedOutboundPorts* och *IdleTimeoutInMinutes*. Värdet 0 för *AllocatedOutboundPorts* anger antalet utgående portar med automatisk tilldelning för antalet utgående portar baserat på serverdelspoolstorlek. Om klustret till exempel har 50 eller färre noder allokeras 1024 portar för varje nod.

Överväg att ändra inställningen *för allokeradeOutboundPorts* eller *IdleTimeoutInMinutes* om du förväntar dig att möta SNAT-utmattning baserat på ovanstående standardkonfiguration. Varje ytterligare IP-adress möjliggör ytterligare 64 000 portar för allokering, men Azure Standard Load Balancer ökar inte automatiskt portarna per nod när fler IP-adresser läggs till. Du kan ändra dessa värden genom att ange parametrar för *belastningsutjämnad-portar* och *belastningsutjämnare-inaktiv-timeout-parametrar.* Ett exempel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Du måste [beräkna önskad kvot][calculate-required-quota] innan du anpassar *allokeradeOutboundPorts* för att undvika anslutnings- eller skalningsproblem. Värdet som du anger för *allocatedOutboundPorts* måste också vara en multipel av 8.

Du kan också använda parametrarna *load-balancer-outbound-ports* och *load-balancer-idle-timeout* när du skapar ett kluster, men du måste också ange antingen *belastningsutjämnad-utgående ip-count,* *load-balancer-outbound-ips*eller *load-balancer-outbound-ip-prefix* också.  Ett exempel:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

När du ändrar *belastningsutjämnare-utgående portar* och *load-balancer-indle-timeout-parametrar* från standard, påverkar det beteendet för belastningsutjämnarens profil, vilket påverkar hela klustret.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Obligatorisk kvot för att anpassa allokeradeOutboundPorts
Du måste ha tillräckligt med utgående IP-kapacitet baserat på antalet virtuella nod-datorer och önskade allokerade utgående portar. Om du vill verifiera att du har tillräckligt med utgående IP-kapacitet använder du följande formel: 
 
*utgående JP* \* 64 000 \> *nodSM:er* \* *önskatUtdeladeOutboundPorts*.
 
Om du till exempel har 3 *nodeVM-moduler*och 50 000 *önskadeOutlocatedBoundPorts*måste du ha minst 3 *utgående IPs*. Vi rekommenderar att du införlivar ytterligare utgående IP-kapacitet utöver vad du behöver. Dessutom måste du ta hänsyn till kluster automatisk skalbar och möjligheten till nodpool uppgraderingar när du beräknar utgående IP-kapacitet. För kluster automatisk skalning, granska den aktuella nodantalet och den maximala nodantalet och använda det högre värdet. För uppgradering, ta hänsyn till ytterligare en nod VM för varje nodpool som tillåter uppgradering.
 
När du ställer in *IdleTimeoutInMinutes* till ett annat värde än standardvärdet på 30 minuter bör du tänka på hur länge dina arbetsbelastningar behöver en utgående anslutning. Tänk också på standardtidsgränsen för en *standard* SKU-belastningsutjämning som används utanför AKS är 4 minuter. Ett *IdleTimeoutInMinutes-värde* som mer exakt återspeglar din specifika AKS-arbetsbelastning kan bidra till att minska SNAT-utmattning som orsakas av att binda upp anslutningar som inte längre används.

## <a name="restrict-access-to-specific-ip-ranges"></a>Begränsa åtkomsten till specifika IP-intervall

NSG (Network Security Group) som är associerad med det virtuella nätverket för belastningsutjämnaren har som standard en regel som tillåter all inkommande extern trafik. Du kan uppdatera den här regeln så att endast specifika IP-intervall för inkommande trafik tillåts. Följande manifest använder *loadBalancerSourceRanges* för att ange ett nytt IP-intervall för inkommande extern trafik:

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

I exemplet ovan uppdateras regeln så att endast inkommande extern trafik från *intervallet MY_EXTERNAL_IP_RANGE.* Mer information om hur du använder den här metoden för att begränsa åtkomsten till belastningsutjämnadtjänsten finns i [Kubernetes-dokumentationen][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes-tjänster i [kubernetes-tjänsternas dokumentation][kubernetes-services].

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
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
