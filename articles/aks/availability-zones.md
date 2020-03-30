---
title: Använda tillgänglighetszoner i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar ett kluster som distribuerar noder över tillgänglighetszoner i Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596818"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Skapa ett AKS-kluster (Azure Kubernetes Service) som använder tillgänglighetszoner

Ett AKS-kluster (Azure Kubernetes Service) distribuerar resurser som noder och lagring över logiska avsnitt i den underliggande Azure-beräkningsinfrastrukturen. Den här distributionsmodellen ser till att noderna körs över separata uppdaterings- och feldomäner i ett enda Azure-datacenter. AKS-kluster som distribueras med det här standardbeteendet ger en hög tillgänglighetsnivå för att skydda mot ett maskinvarufel eller en planerad underhållshändelse.

Aks-kluster kan distribueras över tillgänglighetszoner för att ge dina program en högre nivå av tillgänglighet. Dessa zoner är fysiskt separata datacenter inom en viss region. När klusterkomponenterna distribueras över flera zoner kan AKS-klustret tolerera ett fel i en av dessa zoner. Dina program och hanteringsåtgärder fortsätter att vara tillgängliga även om ett helt datacenter har problem.

Den här artikeln visar hur du skapar ett AKS-kluster och distribuerar nodkomponenterna över tillgänglighetszoner.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.76 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Begränsningar och regiontillgänglighet

AKS-kluster kan för närvarande skapas med hjälp av tillgänglighetszoner i följande regioner:

* USA, centrala
* USA, östra 2
* USA, östra
* Frankrike, centrala
* Japan, östra
* Europa, norra
* Sydostasien
* Storbritannien, södra
* Europa, västra
* USA, västra 2

Följande begränsningar gäller när du skapar ett AKS-kluster med hjälp av tillgänglighetszoner:

* Du kan bara aktivera tillgänglighetszoner när klustret skapas.
* Inställningar för tillgänglighetszon kan inte uppdateras när klustret har skapats. Du kan inte heller uppdatera ett befintligt zonkluster som inte är tillgängliga för att använda tillgänglighetszoner.
* Du kan inte inaktivera tillgänglighetszoner för ett AKS-kluster när det har skapats.
* Den nodstorlek (VM SKU) som valts måste vara tillgänglig i alla tillgänglighetszoner.
* Kluster med aktiverade tillgänglighetszoner kräver användning av Azure Standard Load Balancers för distribution mellan zoner.
* Du måste använda Kubernetes version 1.13.5 eller senare för att kunna distribuera standardbelastningsutjämningsmedel.

AKS-kluster som använder tillgänglighetszoner måste använda Standard *SKU* för Azure load balancer, som är standardvärdet för belastningsutjämnartypen. Den här belastningsutjämnadstypen kan bara definieras vid klusterskapande tid. Mer information och begränsningarna för standardbelastningsutjämnaren finns i [Standard-SKU-begränsningar för Azure load balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Begränsningar för Azure-diskar

Volymer som använder Azure-hanterade diskar är för närvarande inte zonresurser. Poddar som schemaläggs om i en annan zon än den ursprungliga zonen kan inte återansluta sina tidigare diskar. Vi rekommenderar att du kör tillståndslösa arbetsbelastningar som inte kräver beständig lagring som kan uppstå i zonproblem.

Om du måste köra tillståndskänsliga arbetsbelastningar använder du taints och tolerations i pod-specifikationerna för att tala om för Kubernetes-schemaläggaren att skapa poddar i samma zon som dina diskar. Du kan också använda nätverksbaserad lagring, till exempel Azure-filer som kan kopplas till poddar när de schemaläggs mellan zoner.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Översikt över tillgänglighetszoner för AKS-kluster

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från datacenterfel. Zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zonuppsagda tjänster replikerar dina program och data över tillgänglighetszoner för att skydda mot enstaka felpunkter.

Mer information finns [i Vad är tillgänglighetszoner i Azure?][az-overview].

AKS-kluster som distribueras med hjälp av tillgänglighetszoner kan distribuera noder över flera zoner inom en enda region. Ett kluster i regionen *Östra USA 2* kan till exempel skapa noder i alla tre tillgänglighetszonerna i *östra USA 2*. Den här fördelningen av AKS-klusterresurser förbättrar klustertillgängligheten eftersom de är motståndskraftiga mot fel på en viss zon.

![AKS-noddistribution över tillgänglighetszoner](media/availability-zones/aks-availability-zones.png)

I ett zonutfälle kan noderna balanseras om manuellt eller använda klusterautomatskalningen. Om en enskild zon blir otillgänglig fortsätter dina program att köras.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Skapa ett AKS-kluster över tillgänglighetszoner

När du skapar ett kluster med kommandot `--zones` [az aks create][az-aks-create] definierar parametern vilka zoner agentnoder som distribueras till. AKS-kontrollplankomponenterna för klustret är också spridda över zoner i den högsta tillgängliga konfigurationen när du definierar parametern `--zones` vid skapande av kluster.

Om du inte definierar några zoner för standardagentpoolen när du skapar ett AKS-kluster, kommer AKS-kontrollplankomponenterna för klustret inte att använda tillgänglighetszoner. Du kan lägga till ytterligare nodpooler med kommandot az `--zones` [aks nodepool add][az-aks-nodepool-add] och ange för de nya noderna, men kontrollplankomponenterna förblir utan medvetenhet om tillgänglighetszon. Du kan inte ändra zonmedvetenhet för en nodpool eller AKS-kontrollplankomponenter när de har distribuerats.

I följande exempel skapas ett AKS-kluster med namnet *myAKSCluster* i resursgruppen *myResourceGroup*. Totalt *3* noder skapas - en agent i zon *1*, en i *2*och sedan en i *3*. AKS-kontrollplankomponenterna är också fördelade över zoner i den högsta tillgängliga konfigurationen eftersom de definieras som en del av klusterskapandeprocessen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Det tar några minuter att skapa AKS-klustret.

## <a name="verify-node-distribution-across-zones"></a>Verifiera noddistribution mellan zoner

När klustret är klart anger du agentnoderna i skalningsuppsättningen för att se vilken tillgänglighetszon de distribueras i.

Hämta först AKS-klusterautentiseringsuppgifterna med kommandot [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd sedan kommandot [kubectl describe][kubectl-describe] för att lista noderna i klustret. Filtrera på *failure-domain.beta.kubernetes.io/zone* värde som visas i följande exempel:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Följande exempelutdata visar de tre noderna som distribueras över den angivna regionen och tillgänglighetszonerna, till exempel *eastus2-1* för den första tillgänglighetszonen och *eastus2-2* för den andra tillgänglighetszonen:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

När du lägger till ytterligare noder i en agentpool distribuerar Azure-plattformen automatiskt de underliggande virtuella datorerna över de angivna tillgänglighetszonerna.

Observera att i nyare Kubernetes-versioner (1.17.0 och senare) `topology.kubernetes.io/zone` använder AKS den `failure-domain.beta.kubernetes.io/zone`nyare etiketten utöver den inaktuella .

## <a name="verify-pod-distribution-across-zones"></a>Verifiera pod-distribution mellan zoner

Kubernetes används `failure-domain.beta.kubernetes.io/zone` etiketten i [Välkända etiketter, anteckningar och taints][kubectl-well_known_labels]för att automatiskt distribuera poddar i en replikeringsstyrenhet eller tjänst över de olika zoner som är tillgängliga. För att testa detta kan du skala upp klustret från 3 till 5 noder för att verifiera korrekt pod spridning:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

När skalningsåtgärden är klar efter `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` några minuter bör kommandot ge ett utdata som liknar det här exemplet:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Som ni kan se har vi nu två ytterligare noder i zonerna 1 och 2. Du kan distribuera ett program som består av tre repliker. Vi kommer att använda NGINX som exempel:

```console
kubectl run nginx --image=nginx --replicas=3
```

Om du kontrollerar att noder där poddarna körs ser du att poddar körs på poddar som motsvarar tre olika tillgänglighetszoner. Till exempel med `kubectl describe pod | grep -e "^Name:" -e "^Node:"` kommandot skulle du få en utdata som liknar denna:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Som du kan se från föregående utdata körs den första podden på nod `eastus2-1`0, som finns i tillgänglighetszonen . Den andra pod körs på nod 2, vilket motsvarar `eastus2-3`, och den `eastus2-2`tredje i nod 4, i . Utan någon ytterligare konfiguration sprider Kubernetes poddarna korrekt över alla tre tillgänglighetszonerna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skapar ett AKS-kluster som använder tillgänglighetszoner. Mer information om kluster med hög tillgänglig tillgång finns [i Metodtips för affärskontinuitet och haveriberedskap i AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
