---
title: Använda tillgänglighets zoner i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar ett kluster som distribuerar noder över tillgänglighets zoner i Azure Kubernetes service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596818"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Skapa ett Azure Kubernetes service-kluster (AKS) som använder tillgänglighets zoner

Ett Azure Kubernetes service-kluster (AKS) distribuerar resurser, till exempel noder och lagring över logiska delar av den underliggande Azure-beräknings infrastrukturen. Den här distributions modellen ser till att noderna körs i separata uppdaterings-och fel domäner i ett enda Azure-datacenter. AKS-kluster som distribueras med detta standard beteende ger en hög tillgänglighets nivå för att skydda mot ett maskin varu fel eller ett planerat underhålls evenemang.

AKS-kluster kan distribueras mellan tillgänglighets zoner för att ge en högre tillgänglighets nivå för dina program. Dessa zoner är fysiskt separata data Center inom en specifik region. När kluster komponenterna distribueras över flera zoner kan ditt AKS-kluster tolerera ett fel i någon av dessa zoner. Dina program och hanterings åtgärder fortsätter att vara tillgängliga även om ett helt data Center har problem.

Den här artikeln visar hur du skapar ett AKS-kluster och distribuerar nodens komponenter över tillgänglighets zoner.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.76 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Begränsningar och region tillgänglighet

AKS-kluster kan för närvarande skapas med tillgänglighets zoner i följande regioner:

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

Följande begränsningar gäller när du skapar ett AKS-kluster med hjälp av tillgänglighets zoner:

* Du kan bara aktivera tillgänglighets zoner när klustret skapas.
* Det går inte att uppdatera inställningarna för tillgänglighets zonen när klustret har skapats. Du kan inte heller uppdatera ett befintligt, icke-tillgänglighets zon kluster för att använda tillgänglighets zoner.
* Du kan inte inaktivera tillgänglighets zoner för ett AKS-kluster när det har skapats.
* Den Node-storlek (VM-SKU) som väljs måste vara tillgänglig i alla tillgänglighets zoner.
* Kluster med aktiverade tillgänglighets zoner kräver användning av Azures standard belastnings utjämning för distribution mellan zoner.
* Du måste använda Kubernetes-version 1.13.5 eller senare för att kunna distribuera standard belastnings utjämning.

AKS-kluster som använder tillgänglighets zoner måste använda Azure Load Balancer *standard* SKU, vilket är standardvärdet för belastnings Utjämnings typen. Den här typen av belastnings utjämning kan bara definieras i klustrets skapande tid. Mer information och begränsningarna för standard Load Balancer finns i begränsningar för [Azure Load Balancer standard SKU][standard-lb-limitations]: er.

### <a name="azure-disks-limitations"></a>Begränsningar för Azure disks

Volymer som använder Azure Managed disks är för närvarande inte zonindelade resurser. Poddar som har schemalagts om i en annan zon än den ursprungliga zonen kan inte återansluta sina tidigare diskar. Vi rekommenderar att du kör tillstånds lösa arbets belastningar som inte kräver beständig lagring som kan komma mellan zonindelade-problem.

Om du måste köra tillstånds känsliga arbets belastningar använder du bismakar och tolererar i pod-specifikationerna för att instruera Kubernetes Scheduler att skapa poddar i samma zon som dina diskar. Du kan också använda nätverksbaserad lagring som Azure Files som kan kopplas till poddar när de är schemalagda mellan zoner.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Översikt över tillgänglighets zoner för AKS-kluster

Tillgänglighets zoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zoner – redundanta tjänster replikerar dina program och data över tillgänglighets zoner för att skydda från enskilda platser.

Mer information finns i [Vad är tillgänglighets zoner i Azure?][az-overview].

AKS-kluster som distribueras med hjälp av tillgänglighets zoner kan distribuera noder över flera zoner inom en enda region. Till exempel kan ett kluster i regionen *USA, östra 2* skapa noder i alla tre tillgänglighets zoner i *USA, östra 2*. Den här distributionen av AKS kluster resurser ger bättre kluster tillgänglighet eftersom de är elastiska till fel i en speciell zon.

![AKS Node-fördelning över tillgänglighets zoner](media/availability-zones/aks-availability-zones.png)

I ett zon avbrott kan noderna ombalanseras manuellt eller med hjälp av klustrets autoskalning. Om en enskild zon blir otillgänglig fortsätter programmen att köras.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Skapa ett AKS-kluster mellan tillgänglighets zoner

När du skapar ett kluster med kommandot [AZ AKS Create][az-aks-create] definierar parametern `--zones` parametern vilka zoner som agent-noder distribueras till. AKS Control plan-komponenter för klustret sprids också över zoner i den högsta tillgängliga konfigurationen när du definierar `--zones`-parametern vid skapande av klustrets tid.

Om du inte definierar några zoner för standard agenten när du skapar ett AKS-kluster, kommer AKSs kontroll Plans komponenter för klustret inte använda tillgänglighets zoner. Du kan lägga till ytterligare resurspooler med kommandot [AZ AKS nodepool Add][az-aks-nodepool-add] och ange `--zones` för de nya noderna, men kontroll Plans komponenterna är fortfarande inte medvetna om tillgänglighets zonen. Du kan inte ändra zon medvetenheten för en Node-pool eller AKS Control plan-komponenterna när de har distribuerats.

I följande exempel skapas ett AKS-kluster med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*. Totalt *3* noder skapas – en agent i zon *1*, en i *2*, och sedan en i *3*. AKS Control plan-komponenterna distribueras också mellan zoner i den högsta tillgängliga konfigurationen eftersom de har definierats som en del av klustrets Create-process.

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

## <a name="verify-node-distribution-across-zones"></a>Verifiera fördelning av noder mellan zoner

När klustret är klart kan du ange vilken tillgänglighets zon de har distribuerats till i skalnings uppsättningen.

Börja med att hämta autentiseringsuppgifter för AKS-klustret med kommandot [AZ AKS get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd sedan kommandot [kubectl beskriver][kubectl-describe] för att visa en lista över noderna i klustret. Filtrera på *Failure-Domain.beta.Kubernetes.io/Zone* -värdet så som visas i följande exempel:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Följande exempel på utdata visar de tre noder som distribueras i den angivna regionen och tillgänglighets zonerna, till exempel *eastus2-1* för den första tillgänglighets zonen och *eastus2-2* för den andra tillgänglighets zonen:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

När du lägger till ytterligare noder i en agent distribuerar Azure-plattformen automatiskt de underliggande virtuella datorerna i de angivna tillgänglighets zonerna.

Observera att i senare Kubernetes-versioner (1.17.0 och senare) använder AKS den nyare etiketten `topology.kubernetes.io/zone` förutom den föråldrade `failure-domain.beta.kubernetes.io/zone`.

## <a name="verify-pod-distribution-across-zones"></a>Verifiera Pod-distribution mellan zoner

Som dokumenterade på [välkända etiketter, anteckningar och smaker][kubectl-well_known_labels], använder Kubernetes etiketten `failure-domain.beta.kubernetes.io/zone` för att automatiskt distribuera poddar i en replikeringsprovider eller tjänst över olika zoner som är tillgängliga. För att testa detta kan du skala upp klustret från 3 till 5 noder för att verifiera korrekt Pod-spridning:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

När skalnings åtgärden slutförs efter några minuter bör kommandot `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` ge utdata som liknar det här exemplet:

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

Som du kan se har vi nu två ytterligare noder i zon 1 och 2. Du kan distribuera ett program som består av tre repliker. Vi kommer att använda NGINX som exempel:

```console
kubectl run nginx --image=nginx --replicas=3
```

Om du verifierar att noderna där dina poddar körs, ser du att poddar körs på poddar som motsvarar tre olika tillgänglighets zoner. Till exempel med kommandot `kubectl describe pod | grep -e "^Name:" -e "^Node:"` skulle du få utdata som liknar detta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Som du kan se från föregående utdata körs den första Pod på nod 0, som finns i tillgänglighets zonens `eastus2-1`. Den andra Pod körs på nod 2, som motsvarar `eastus2-3`och den tredje i nod 4, i `eastus2-2`. Utan ytterligare konfiguration, Kubernetes spridningen av poddar korrekt i alla tre tillgänglighets zoner.

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller information om hur du skapar ett AKS-kluster som använder tillgänglighets zoner. Mer information om kluster med hög tillgänglighet finns i [metod tips för verksamhets kontinuitet och haveri beredskap i AKS][best-practices-bc-dr].

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
