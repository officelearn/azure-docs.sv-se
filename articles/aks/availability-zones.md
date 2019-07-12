---
title: Använda Tillgänglighetszoner i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar ett kluster som distribuerar noder mellan tillgänglighetszoner i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840685"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Förhandsgranskning – skapa ett kluster i Azure Kubernetes Service (AKS) som använder Tillgänglighetszoner

Ett kluster i Azure Kubernetes Service (AKS) distribuerar resurser som noder och lagring över logiska delar av de underliggande Azure compute-infrastruktur. Denna distributionsmodell ser till att noderna körs i separata domäner i en enda Azure-datacenter som uppdaterings- och feldomäner. AKS-kluster som distribueras med den här standardfunktionen ger hög tillgänglighet för att skydda mot ett maskinvarufel eller planerat underhåll.

För att ge högre tillgänglighet för dina program, kan AKS-kluster distribueras i olika tillgänglighetszoner. Zonerna är fysiskt separata datacenter i en viss region. När klustret komponenterna distribueras över flera zoner, kan AKS-klustret tolerera fel på något av dessa zoner. Dina program och hanteringsåtgärder fortsätter att vara tillgängliga även om ett helt datacenter har ett problem.

Den här artikeln visar hur du skapar ett AKS-kluster och distribuera komponenter för noden i olika tillgänglighetszoner. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning, delta i. De tillhandahålls för att samla in feedback och buggar från vår community. I förhandsversionen kan är inte dessa funktioner avsedda för användning i produktion. Funktioner i offentliga förhandsversioner omfattas ”bästa prestanda” support. Hjälp från teamen för AKS-teknisk support är tillgänglig under kontorstid Pacific tidszon (Stillahavstid) endast. Mer information finns i följande supportartiklar:
>
> * [AKS supportprinciper][aks-support-policies]
> * [Vanliga frågor om Azure-Support][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.66 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för aks-förhandsversion

Om du vill skapa AKS-kluster som använder tillgänglighetszoner, måste den *aks-förhandsversion* CLI tilläggsversion 0.4.1 eller högre. Installera den *förhandsversionen av aks* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] kommandot::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Registrera funktionen flaggor för din prenumeration

Om du vill skapa ett AKS-kluster som tillgänglighetszoner, först aktivera några flaggor för funktionen på din prenumeration. -Kluster använder en VM-skalningsuppsättning hantera distributionen och konfigurationen av Kubernetes-noderna. Den *standard* SKU: N för Azure load balancer krävs också för att tillhandahålla återhämtning för nätverkskomponenter att dirigera trafik till klustret. Registrera den *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, och *VMSSPreview* funktionen flaggar med hjälp av den [az funktionen registrera][az-feature-register] kommandot som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion i en prenumeration kan du inte för närvarande avregistrera den funktionen. När du aktiverar vissa funktioner i förhandsversion, kan standardinställningar användas för alla AKS-kluster som skapas i prenumerationen. Inte aktivera förhandsversionsfunktioner för produktion-prenumerationer. Använd en separat prenumeration för att testa funktioner och samla in feedback.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan][az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Begränsningar och regiontillgänglighet

AKS-kluster kan för närvarande skapas med hjälp av tillgänglighetszoner i följande regioner:

* USA, östra 2
* Norra Europa
* Sydostasien
* Västra Europa
* Västra USA 2

Följande begränsningar gäller när du skapar ett AKS-kluster med hjälp av tillgänglighetszoner:

* Du kan bara aktivera tillgänglighetszoner när klustret har skapats.
* Inställningar för tillgänglighet zon kan inte uppdateras när klustret har skapats. Du kan också uppdatera en befintliga, icke-tillgänglighet zon kluster för att använda tillgänglighetszoner.
* Du kan inte inaktivera tillgänglighetszoner för ett AKS-kluster när det har skapats.
* Nodstorlek (VM SKU) har valt måste vara tillgänglig för samtliga tillgänglighetszoner.
* Kluster med tillgänglighet zoner aktiverat kräver användning av Azure Standard belastningsutjämnare för distribution i flera zoner.
* Du måste använda Kubernetes version 1.13.5 eller högre för att distribuera Standard belastningsutjämnare.

AKS-kluster som använder tillgänglighetszoner måste använda Azure load balancer *standard* SKU. Standard *grundläggande* SKU: N för Azure load balancer stöder inte distribution över tillgänglighetszoner. För mer information och begränsningarna i standard-belastningsutjämnare kan se [Azure load balancer standard SKU begränsningar i förhandsversionen][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Begränsningar för Azure-diskar

Volymer som använder Azure managed disks är för närvarande inte zonindelade resurser. Poddar schemaläggas i en annan zon från sina ursprungliga zon kan inte ansluta sina tidigare diskarna. Vi rekommenderar att du kör tillståndslösa arbetsbelastningar som inte kräver beständig lagring som kan stöta på zonindelad utfärdar.

Om du måste köra tillståndskänsliga arbetsbelastningar Använd taints och tolerations i din pod-specifikationer som talar om scheduler Kubernetes skapar poddarna i samma zon som dina diskar. Alternativt använda network-baserad lagring, till exempel Azure-filer som kan bifogas med poddar som de är schemalagda mellan zoner.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Översikt över Tillgänglighetszoner för AKS-kluster

Tillgänglighetszoner är en hög tillgänglighet erbjudande som skyddar dina program och data från datacenter havererar. Zoner är unika, fysiska platser inom en Azure-region. Varje zon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zonredundant tjänster replikera dina program och data i olika Tillgänglighetszoner och skydda från single punkter av fel.

Mer information finns i [vad är Tillgänglighetszoner i Azure?][az-overview].

AKS-kluster som distribueras med hjälp av tillgänglighetszoner kan distribuera noder över flera zoner inom en enda region. Till exempel ett kluster i den *östra USA 2* region kan skapa noder i alla tre tillgänglighetszoner i *östra USA 2*. Den här distributionen av AKS klusterresurser förbättrar tillgängligheten för klustret när de är motståndskraftig mot fel på en viss zon.

![AKS noden fördelningen mellan tillgänglighetszoner](media/availability-zones/aks-availability-zones.png)

I en zon avbrott noderna kan vara genomförs manuellt eller använda kluster autoskalningen. Om en enskild zon blir otillgänglig kan fortsätter dina program att köras.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Skapa ett AKS-kluster i olika tillgänglighetszoner

När du skapar ett kluster som använder den [az aks skapa][az-aks-create] kommandot, den `--node-zones` parametern definierar vilka zoner agentnoder distribueras i. Plan komponenter för AKS-kontroll för klustret fördelas mellan zoner i konfiguration för högsta tillgänglighet även när du skapar ett kluster att ange den `--node-zones` parametern.

Om du inte definierar alla zoner för agentpoolen standard när du skapar ett AKS-kluster, plan komponenter för AKS-kontroll för klustret inte att använda tillgänglighetszoner. Du kan lägga till ytterligare nodpooler (för närvarande i förhandsversion i AKS) med hjälp av den [az aks nodepool lägga till][az-aks-nodepool-add] kommandot och ange `--node-zones` för dessa nya agentnoder, men kontrollen plan komponenter förblir utan tillgänglighetszon medvetenhet. Du kan inte ändra zon medvetenhet för en nodpool eller AKS styra plan komponenter när de har distribuerats.

I följande exempel skapas ett AKS-kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Totalt *3* noder skapas – en agent i zonen *1*, en i *2*, och sedan en i *3*. AKS kontroll plan komponenterna också distribueras i flera zoner i konfiguration för högsta tillgänglighet eftersom de har definierats som en del av klustret skapas.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Det tar några minuter att skapa AKS-kluster.

## <a name="verify-node-distribution-across-zones"></a>Kontrollera noden distribution i flera zoner

När klustret är klart, lista agentnoder i skaluppsättningen för att se vilka de har distribuerats i tillgänglighetszon.

Hämta först AKS-kluster-autentiseringsuppgifter med hjälp av den [aaz aks get-credentials][az-aks-get-credentials] kommando:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd sedan den [kubectl beskriver][kubectl-describe] kommando för att lista noder i klustret. Filtrera efter den *failure-domain.beta.kubernetes.io/zone* värde som visas i följande exempel:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Följande Exempelutdata visar de tre noderna distribuerade i regionen och tillgänglighetszoner, till exempel *usaöstra2-1* för den första tillgänglighetszon och *usaöstra2 2* för andra tillgänglighetszon:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

När du lägger till ytterligare noder i en agentpool distribuerar Azure-plattformen automatiskt de underliggande virtuella datorerna i de angivna tillgänglighetszonerna.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du skapar ett AKS-kluster som använder tillgänglighetszoner. Överväganden i kluster med hög tillgänglighet finns i [bästa praxis för företag affärskontinuitet och haveriberedskap i AKS][best-practices-bc-dr].

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
