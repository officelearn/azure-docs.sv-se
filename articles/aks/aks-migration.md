---
title: Migrera till Azure Kubernetes service (AKS)
description: Migrera till Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624818"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrera till Azure Kubernetes service (AKS)

Den här artikeln hjälper dig att planera och utföra en lyckad migrering till Azure Kubernetes service (AKS). För att hjälpa dig att fatta viktiga beslut innehåller den här guiden information om den aktuella rekommenderade konfigurationen för AKS. Den här artikeln omfattar inte varje scenario, och i förekommande fall innehåller artikeln länkar till mer detaljerad information om hur du planerar en lyckad migrering.

Det här dokumentet kan användas för att hjälpa till att stödja följande scenarier:

* Migrera ett AKS-kluster som backas upp av [tillgänglighets uppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) till [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrera ett AKS-kluster för att använda en [standard-SKU-belastningsutjämnare](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migrera från [Azure Container Service (ACS) – tas ur bruk 31 januari 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) till AKS
* Migrera från [AKS-motorn](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) till AKS
* Migrera från icke-Azure-baserade Kubernetes-kluster till AKS

När du migrerar bör du se till att mål Kubernetes-versionen är i fönstret som stöds för AKS. Om du använder en äldre version kanske den inte ligger inom det intervall som stöds och kräver att uppgraderings versioner stöds av AKS. Mer information finns i [AKS-Kubernetes-versioner som stöds](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) .

Om du migrerar till en nyare version av Kubernetes granskar du [support policyn för Kubernetes version och versions förvrängning](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Flera verktyg med öppen källkod kan hjälpa dig med din migrering, beroende på ditt scenario:

* [Velero](https://velero.io/) (kräver Kubernetes 1.7 +)
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli)
* [Flytta om](https://github.com/mhausenblas/reshifter)

I den här artikeln sammanfattas information om migreringen för:

> [!div class="checklist"]
> * AKS med Standard Load Balancer och Virtual Machine Scale Sets
> * Befintliga anslutna Azure-tjänster
> * Se till att giltiga kvoter
> * Hög tillgänglighet och affärs kontinuitet
> * Överväganden för tillstånds lösa program
> * Överväganden för tillstånds känsliga program
> * Distribution av kluster konfigurationen

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS med Standard Load Balancer och Virtual Machine Scale Sets

AKS är en hanterad tjänst som erbjuder unika funktioner med lägre hanterings kostnader. Som ett resultat av en hanterad tjänst måste du välja från en uppsättning [regioner](https://docs.microsoft.com/azure/aks/quotas-skus-regions) som AKS stöder. Över gången från ditt befintliga kluster till AKS kan kräva att du ändrar dina befintliga program så att de förblir felfria i AKS-hanterade kontroll planet.

Vi rekommenderar att du använder AKS-kluster som backas upp av [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets) och [Azure-standard Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) för att se till att du får funktioner som [flera noder](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview), [auktoriserade IP-intervall](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), [kluster autoskalning](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [Azure policy för AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)och andra nya funktioner när de släpps.

AKS-kluster som backas upp av [tillgänglighets uppsättningar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) saknar stöd för många av dessa funktioner.

I följande exempel skapas ett AKS-kluster med en pool med flera noder som backas upp av en skalnings uppsättning för virtuella datorer. Den använder en standard belastningsutjämnare. Det aktiverar också klustrets autoskalning på nodens nod för klustret och anger minst *1* och högst *3* noder:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Befintliga anslutna Azure-tjänster

När du migrerar kluster kan du ha kopplat externa Azure-tjänster. Dessa kräver inte resurs fritid, men de kommer att behöva uppdatera anslutningar från tidigare till nya kluster för att underhålla funktioner.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Lagringskonto
* Externa databaser

## <a name="ensure-valid-quotas"></a>Se till att giltiga kvoter

Eftersom ytterligare virtuella datorer ska distribueras till din prenumeration under migreringen bör du kontrol lera att kvoterna och gränserna är tillräckliga för dessa resurser. Du kan behöva begära en ökning av [vCPU-kvoten](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Du kan behöva begära en ökning av [nätverks kvoterna](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) för att se till att du inte har några IP-adresser. Mer information finns i [nätverk och IP-intervall för AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) .

Mer information finns i [prenumerations-och tjänst begränsningar i Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Du kan kontrol lera dina aktuella kvoter genom att gå till [bladet prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)i Azure Portal, välja din prenumeration och sedan välja **användning + kvoter**.

## <a name="high-availability-and-business-continuity"></a>Hög tillgänglighet och affärs kontinuitet

Om programmet inte kan hantera nedtid måste du följa bästa praxis för scenarier med migrering av hög tillgänglighet.  Metod tips för komplex planering av affärs kontinuitet, haveri beredskap och maximal drift tid ligger utanför det här dokumentets omfattning.  Läs mer om [metod tips för verksamhets kontinuitet och haveri beredskap i Azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) för att få mer information.

För komplexa program migrerar du normalt över tid i stället för alla samtidigt. Det innebär att de gamla och nya miljöerna kan behöva kommunicera över nätverket. Program som tidigare använde `ClusterIP` tjänster för att kommunicera kan behöva exponeras som typ `LoadBalancer` och skyddas på lämpligt sätt.

För att slutföra migreringen vill du peka klienter till de nya tjänster som körs på AKS. Vi rekommenderar att du omdirigerar trafik genom att uppdatera DNS för att peka på Load Balancer som finns framför ditt AKS-kluster.

[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) kan dirigera kunder till det önskade Kubernetes-klustret och program instansen.  Traffic Manager är en DNS-baserad trafikbelastnings utjämning som kan distribuera nätverks trafik över flera regioner.  För bästa prestanda och redundans ska du dirigera all program trafik genom Traffic Manager innan den går till ditt AKS-kluster.  I en distribution med multikluster bör kunderna ansluta till ett Traffic Manager DNS-namn som pekar på tjänsterna på varje AKS-kluster. Definiera de här tjänsterna genom att använda Traffic Manager-slutpunkter. Varje slut punkt är *IP för tjänst belastnings utjämning*. Använd den här konfigurationen för att dirigera nätverks trafik från Traffic Manager slut punkt i en region till slut punkten i en annan region.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure-tjänsten för front dörr](https://docs.microsoft.com/azure/frontdoor/front-door-overview) är ett annat alternativ för att dirigera trafik för AKS-kluster.  Med Azure Front Door Service kan du definiera, hantera och övervaka global routning av din webbtrafik, genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. 

### <a name="considerations-for-stateless-applications"></a>Överväganden för tillstånds lösa program

Tillstånds lös programmigrering är det enklaste fallet. Tillämpa dina resurs definitioner (YAML eller Helm) på det nya klustret, se till att allt fungerar som förväntat och dirigera om trafik för att aktivera det nya klustret.

### <a name="considerations-for-stateful-applications"></a>Överväganden för tillstånds känsliga program

Planera noggrant migreringen av tillstånds känsliga program för att undvika data förlust eller oväntad stillestånds tid.

Om du använder Azure Files kan du montera fil resursen som en volym i det nya klustret:
* [Montera statisk Azure Files som en volym](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Om du använder Azure Managed Disks kan du bara montera disken om den inte är ansluten till någon virtuell dator:
* [Montera statisk Azure-disk som en volym](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Om ingen av dessa metoder fungerar kan du använda en alternativ för säkerhets kopiering och återställning:
* [Velero på Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar kan Azure Files monteras till flera värdar samtidigt. I ditt AKS-kluster förhindrar Azure och Kubernetes dig inte att skapa en pod som används av ACS-klustret fortfarande. För att förhindra data förlust och oväntade beteenden, se till att klustren inte skriver till samma filer samtidigt.

Om ditt program kan vara värd för flera repliker som pekar på samma fil resurs, följer du stegen för stegvis migrering och distribuerar YAML-definitionerna till det nya klustret. Om inte, så innebär en möjlig migrering följande steg:

* Verifiera att programmet fungerar korrekt.
* Peka din aktiva trafik till ditt nya AKS-kluster.
* Koppla bort det gamla klustret.

Om du vill börja med en tom resurs och göra en kopia av data källan kan du migrera dina data med hjälp av [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) -kommandon.


#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Om du migrerar befintliga beständiga volymer till AKS följer du normalt de här stegen:

* Skrivningar från sidan till programmet. (Det här steget är valfritt och kräver nedtid.)
* Ta ögonblicks bilder av diskarna.
* Skapa nya hanterade diskar från ögonblicks bilderna.
* Skapa beständiga volymer i AKS.
* Uppdatera Pod-specifikationerna för att [använda befintliga volymer](https://docs.microsoft.com/azure/aks/azure-disk-volume) i stället för PersistentVolumeClaims (statisk etablering).
* Distribuera ditt program till AKS.
* Verifiera att programmet fungerar korrekt.
* Peka din aktiva trafik till ditt nya AKS-kluster.

> [!IMPORTANT]
> Om du väljer att inte använda offline-skrivning måste du replikera data till den nya distributionen. Annars saknar du de data som skrevs efter att du tog disk ögonblicks bilderna.

Vissa verktyg med öppen källkod kan hjälpa dig att skapa hanterade diskar och migrera volymer mellan Kubernetes-kluster:

* [Tillägget Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopierar och konverterar diskar över resurs grupper och Azure-regioner.
* [Azure Kube CLI-tillägget](https://github.com/yaron2/azure-kube-cli) räknar upp ACS Kubernetes-volymer och migrerar dem till ett AKS-kluster.


### <a name="deployment-of-your-cluster-configuration"></a>Distribution av kluster konfigurationen

Vi rekommenderar att du använder din befintliga-pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att distribuera en fungerande konfiguration till AKS. Du kan använda Azure-pipelines för att [bygga och distribuera dina program till AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Klona dina befintliga distributions uppgifter och se till att `kubeconfig` pekar på det nya AKS-klustret.

Om detta inte är möjligt exporterar du resurs definitioner från ditt befintliga Kubernetes-kluster och tillämpar dem sedan på AKS. Du kan använda `kubectl` för att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Flytta befintliga resurser till en annan region

Du kanske vill flytta ditt AKS-kluster till en [annan region som stöds av AKS][region-availability]. Vi rekommenderar att du skapar ett nytt kluster i den andra regionen och sedan distribuerar dina resurser och program till det nya klustret. Om du har några tjänster, till exempel [Azure dev Spaces][azure-dev-spaces] som körs på ditt AKS-kluster, måste du också installera och konfigurera tjänsterna i klustret i den nya regionen.


I den här artikeln sammanfattas information om migreringen för:

> [!div class="checklist"]
> * AKS med Standard Load Balancer och Virtual Machine Scale Sets
> * Befintliga anslutna Azure-tjänster
> * Se till att giltiga kvoter
> * Hög tillgänglighet och affärs kontinuitet
> * Överväganden för tillstånds lösa program
> * Överväganden för tillstånds känsliga program
> * Distribution av kluster konfigurationen


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/