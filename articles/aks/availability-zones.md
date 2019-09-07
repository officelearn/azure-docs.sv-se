---
title: Använda Tillgänglighetszoner i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar ett kluster som distribuerar noder över tillgänglighets zoner i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 690d22eadf37a24b4679ce10838074533ac65fcb
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390075"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>För hands version – skapa ett Azure Kubernetes service-kluster (AKS) som använder Tillgänglighetszoner

Ett Azure Kubernetes service-kluster (AKS) distribuerar resurser, till exempel noder och lagring över logiska delar av den underliggande Azure-beräknings infrastrukturen. Den här distributions modellen ser till att noderna körs i separata uppdaterings-och fel domäner i ett enda Azure-datacenter. AKS-kluster som distribueras med detta standard beteende ger en hög tillgänglighets nivå för att skydda mot ett maskin varu fel eller ett planerat underhålls evenemang.

AKS-kluster kan distribueras mellan tillgänglighets zoner för att ge en högre tillgänglighets nivå för dina program. Dessa zoner är fysiskt separata data Center inom en specifik region. När kluster komponenterna distribueras över flera zoner kan ditt AKS-kluster tolerera ett fel i någon av dessa zoner. Dina program och hanterings åtgärder fortsätter att vara tillgängliga även om ett helt data Center har problem.

Den här artikeln visar hur du skapar ett AKS-kluster och distribuerar nodens komponenter över tillgänglighets zoner. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.66 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill skapa AKS-kluster som använder tillgänglighets zoner behöver du *AKS-Preview CLI-* tillägget version 0.4.1 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-availabilityzonepreview-feature-flag-for-your-subscription"></a>Registrera funktions flaggan AvailabilityZonePreview för din prenumeration

Om du vill skapa ett AKS-kluster som tillgänglighets zoner måste du först aktivera funktions flaggan *AvailabilityZonePreview* i din prenumeration. Registrera funktions flaggan *AvailabilityZonePreview* med [funktions registrerings kommandot AZ][az-feature-register] som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Begränsningar och region tillgänglighet

AKS-kluster kan för närvarande skapas med tillgänglighets zoner i följande regioner:

* USA, östra 2
* Norra Europa
* Sydostasien
* Västra Europa
* Västra USA 2

Följande begränsningar gäller när du skapar ett AKS-kluster med hjälp av tillgänglighets zoner:

* Du kan bara aktivera tillgänglighets zoner när klustret skapas.
* Det går inte att uppdatera inställningarna för tillgänglighets zonen när klustret har skapats. Du kan inte heller uppdatera ett befintligt, icke-tillgänglighets zon kluster för att använda tillgänglighets zoner.
* Du kan inte inaktivera tillgänglighets zoner för ett AKS-kluster när det har skapats.
* Den Node-storlek (VM-SKU) som väljs måste vara tillgänglig i alla tillgänglighets zoner.
* Kluster med aktiverade tillgänglighets zoner kräver användning av Azures standard belastnings utjämning för distribution mellan zoner.
* Du måste använda Kubernetes-version 1.13.5 eller senare för att kunna distribuera standard belastnings utjämning.

AKS-kluster som använder tillgänglighets zoner måste använda Azure Load Balancer *standard* SKU. *Standard-SKU:* n för Azure Load Balancer stöder inte distribution över tillgänglighets zoner. Mer information och begränsningarna för standard Load Balancer finns i begränsningar för [Azure Load Balancer standard SKU][standard-lb-limitations]: er.

### <a name="azure-disks-limitations"></a>Begränsningar för Azure disks

Volymer som använder Azure Managed disks är för närvarande inte zonindelade resurser. Poddar som har schemalagts om i en annan zon än den ursprungliga zonen kan inte återansluta sina tidigare diskar. Vi rekommenderar att du kör tillstånds lösa arbets belastningar som inte kräver beständig lagring som kan komma mellan zonindelade-problem.

Om du måste köra tillstånds känsliga arbets belastningar använder du bismakar och tolererar i pod-specifikationerna för att instruera Kubernetes Scheduler att skapa poddar i samma zon som dina diskar. Du kan också använda nätverksbaserad lagring som Azure Files som kan kopplas till poddar när de är schemalagda mellan zoner.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Översikt över Tillgänglighetszoner för AKS-kluster

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Zoner är unika fysiska platser inom en Azure-region. Varje zon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zoner – redundanta tjänster replikerar dina program och data över Tillgänglighetszoner för att skydda från enskilda platser.

Mer information finns i [Vad är Tillgänglighetszoner i Azure?][az-overview].

AKS-kluster som distribueras med hjälp av tillgänglighets zoner kan distribuera noder över flera zoner inom en enda region. Ett kluster i regionen *USA, östra 2* kan till exempel skapa noder i alla tre tillgänglighets zoner i *USA, östra 2*. Den här distributionen av AKS kluster resurser ger bättre kluster tillgänglighet eftersom de är elastiska till fel i en speciell zon.

![AKS Node-fördelning över tillgänglighets zoner](media/availability-zones/aks-availability-zones.png)

I ett zon avbrott kan noderna ombalanseras manuellt eller med hjälp av klustrets autoskalning. Om en enskild zon blir otillgänglig fortsätter programmen att köras.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Skapa ett AKS-kluster mellan tillgänglighets zoner

När du skapar ett kluster med `--node-zones` kommandot [AZ AKS Create][az-aks-create] definierar parametern vilka zoner som agent-noder distribueras till. AKS Control plan-komponenter för klustret sprids också över zoner i den högsta tillgängliga konfigurationen när du skapar ett kluster som anger `--node-zones` parametern.

Om du inte definierar några zoner för standard agenten när du skapar ett AKS-kluster, kommer AKSs kontroll Plans komponenter för klustret inte använda tillgänglighets zoner. Du kan lägga till ytterligare Node-pooler (för närvarande i för hands version i AKS) med kommandot [AZ AKS nodepool Add][az-aks-nodepool-add] och ange `--node-zones` för de nya agent-noderna, men kontroll Plans komponenterna kvarstår utan tillgänglighets zonens medvetenhet. Du kan inte ändra zon medvetenheten för en Node-pool eller AKS Control plan-komponenterna när de har distribuerats.

I följande exempel skapas ett AKS-kluster med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*. Totalt *3* noder skapas – en agent i zon *1*, en i *2*, och sedan en i *3*. AKS Control plan-komponenterna distribueras också mellan zoner i den högsta tillgängliga konfigurationen eftersom de har definierats som en del av klustrets Create-process.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
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
