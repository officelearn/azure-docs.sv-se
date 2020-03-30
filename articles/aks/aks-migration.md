---
title: Migrera till Azure Kubernetes Service (AKS)
description: Migrera till Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624818"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrera till Azure Kubernetes Service (AKS)

Den här artikeln hjälper dig att planera och köra en lyckad migrering till Azure Kubernetes Service (AKS). Den här guiden innehåller information om den aktuella rekommenderade konfigurationen för AKS för att hjälpa dig att fatta viktiga beslut. Den här artikeln täcker inte alla scenarier, och i förekommande fall innehåller artikeln länkar till mer detaljerad information för att planera en lyckad migrering.

Det här dokumentet kan användas för att stödja följande scenarier:

* Migrera ett AKS-kluster som backas upp av [tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) till [skalningsuppsättningar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrera ett AKS-kluster för att använda en [standard SKU-belastningsutjämnare](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migrera från [Azure Container Service (ACS) – går i pension 31 januari 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) till AKS
* Migrera från [AKS-motor](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) till AKS
* Migrera från icke-Azure-baserade Kubernetes-kluster till AKS

När du migrerar, se till att din målkubernetes version är inom det fönster som stöds för AKS. Om du använder en äldre version kanske den inte ligger inom det intervall som stöds och kräver att uppgradera versioner stöds av AKS. Mer information finns i AKS-versioner som stöds av [Kubernetes.](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)

Om du migrerar till en nyare version av Kubernetes läser du [kubernetes version och version skeva supportprincip](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Flera verktyg med öppen källkod kan hjälpa dig med migreringen, beroende på ditt scenario:

* [Velero](https://velero.io/) (Kräver Kubernetes 1,7+)
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli)
* [ReShifter (omskiftare)](https://github.com/mhausenblas/reshifter)

I den här artikeln kommer vi att sammanfatta migreringsinformation för:

> [!div class="checklist"]
> * AKS med standardbelastningsutjämning och skalningsuppsättningar för virtuella datorer
> * Befintliga anslutna Azure-tjänster
> * Säkerställ giltiga kvoter
> * Hög tillgänglighet och affärskontinuitet
> * Överväganden för tillståndslösa ansökningar
> * Överväganden för tillståndskänsliga ansökningar
> * Distribution av klusterkonfigurationen

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS med standardbelastningsutjämning och skalningsuppsättningar för virtuella datorer

AKS är en hanterad tjänst som erbjuder unika funktioner med lägre hanteringskostnader. Som ett resultat av att vara en hanterad tjänst måste du välja från en uppsättning [regioner](https://docs.microsoft.com/azure/aks/quotas-skus-regions) som AKS stöder. Övergången från ditt befintliga kluster till AKS kan kräva att du ändrar dina befintliga program så att de förblir felfria på det AKS-hanterade kontrollplanet.

Vi rekommenderar att du använder AKS-kluster som backas upp av [skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets) för virtuella datorer och [Azure Standard Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) för att säkerställa att du får funktioner som [flera nodpooler,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) [tillgänglighetszoner,](https://docs.microsoft.com/azure/availability-zones/az-overview) [auktoriserade IP-intervall,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [Kluster automatisk skalbar,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [Azure-princip för AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)och andra nya funktioner när de släpps.

AKS-kluster som backas upp av [tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) för virtuella datorer saknar stöd för många av dessa funktioner.

I följande exempel skapas ett AKS-kluster med ennodpool som backas upp av en skalningsuppsättning för virtuella datorer. Den använder en standard belastningsutjämnare. Det gör det också möjligt för kluster automatisk skalbar på nodpoolen för klustret och anger minst *1* och högst *3* noder:

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

När du migrerar kluster kan du ha bifogat externa Azure-tjänster. Dessa kräver inte resursrekreation, men de kräver uppdatering av anslutningar från tidigare till nya kluster för att underhålla funktioner.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Lagringskonto
* Externa databaser

## <a name="ensure-valid-quotas"></a>Säkerställ giltiga kvoter

Eftersom ytterligare virtuella datorer distribueras till din prenumeration under migreringen bör du kontrollera att dina kvoter och gränser är tillräckliga för dessa resurser. Du kan behöva begära en ökning av [vCPU-kvoten](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Du kan behöva begära en ökning för [nätverkskvoter](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) för att säkerställa att du inte uttömmer IPs. Se [nätverk och IP-intervall för AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) för ytterligare information.

Mer information finns i [Azure-prenumerations- och tjänstgränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Om du vill kontrollera dina aktuella kvoter går du till [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)i Azure-portalen, väljer din prenumeration och väljer sedan **Användning + kvoter**.

## <a name="high-availability-and-business-continuity"></a>Hög tillgänglighet och affärskontinuitet

Om ditt program inte kan hantera driftstopp måste du följa metodtips för scenarier för migrering med hög tillgänglighet.  Metodtips för komplex kontinuitetsplanering, haveriberedskap och maximera drifttid ligger utanför det här dokumentets omfattning.  Läs mer om [Metodtips för affärskontinuitet och haveriberedskap i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) om du vill veta mer.

För komplexa program migrerar du vanligtvis med tiden i stället för alla på en gång. Det innebär att de gamla och nya miljöerna kan behöva kommunicera via nätverket. Program som `ClusterIP` tidigare använt tjänster för att `LoadBalancer` kommunicera kan behöva exponeras som typ och skyddas på rätt sätt.

För att slutföra migreringen, du vill peka klienter till de nya tjänsterna som körs på AKS. Vi rekommenderar att du omdirigerar trafik genom att uppdatera DNS så att den pekar på den belastningsutjämnare som sitter framför AKS-klustret.

[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) kan dirigera kunder till önskat Kubernetes-kluster och programinstans.  Traffic Manager är en DNS-baserad trafikbelastningsutjämnare som kan distribuera nätverkstrafik mellan regioner.  För bästa prestanda och redundans kan du styra all programtrafik via Traffic Manager innan den går till AKS-klustret.  I en multicluster-distribution bör kunder ansluta till ett Traffic Manager DNS-namn som pekar på tjänsterna i varje AKS-kluster. Definiera dessa tjänster med hjälp av Traffic Manager-slutpunkter. Varje slutpunkt är *tjänsten belastningsutjämnare IP*. Använd den här konfigurationen för att dirigera nätverkstrafik från Traffic Manager-slutpunkten i en region till slutpunkten i en annan region.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) är ett annat alternativ för routning av trafik för AKS-kluster.  Med Azure Front Door Service kan du definiera, hantera och övervaka global routning av din webbtrafik, genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. 

### <a name="considerations-for-stateless-applications"></a>Överväganden för tillståndslösa ansökningar

Tillståndslös programmigrering är det enklaste fallet. Använd resursdefinitionerna (YAML eller Helm) i det nya klustret, se till att allt fungerar som förväntat och dirigera om trafiken för att aktivera det nya klustret.

### <a name="considerations-for-stateful-applications"></a>Överväganden för tillståndskänsliga ansökningar

Planera försiktigt migreringen av tillståndskänsliga program för att undvika dataförlust eller oväntade driftstopp.

Om du använder Azure Files kan du montera filresursen som en volym i det nya klustret:
* [Montera statiska Azure-filer som en volym](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Om du använder Azure Managed Disks kan du bara montera disken om den inte är ansluten till en virtuell dator:
* [Montera statisk Azure-disk som en volym](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Om ingen av dessa metoder fungerar kan du använda alternativen för säkerhetskopiering och återställning:
* [Velero på Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar kan Azure-filer monteras på flera värdar samtidigt. I AKS-klustret hindrar Azure och Kubernetes dig inte från att skapa en pod som acs-klustret fortfarande använder. För att förhindra dataförlust och oväntat beteende, se till att klustret inte skriver till samma filer samtidigt.

Om programmet kan vara värd för flera repliker som pekar på samma filresurs följer du de tillståndslösa migreringsstegen och distribuerar YAML-definitionerna till det nya klustret. Om inte, innebär en möjlig migreringsmetod följande steg:

* Verifiera att programmet fungerar som det ska.
* Rikta din livetrafik till ditt nya AKS-kluster.
* Koppla från det gamla klustret.

Om du vill börja med en tom resurs och göra en [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) kopia av källdata kan du använda kommandona för att migrera data.


#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Om du migrerar befintliga beständiga volymer till AKS följer du vanligtvis följande steg:

* Quiesce skriver till ansökan. (Det här steget är valfritt och kräver driftstopp.)
* Ta ögonblicksbilder av diskarna.
* Skapa nya hanterade diskar från ögonblicksbilderna.
* Skapa beständiga volymer i AKS.
* Uppdatera pod-specifikationer för att [använda befintliga volymer](https://docs.microsoft.com/azure/aks/azure-disk-volume) i stället för PersistentVolumeClaims (statisk etablering).
* Distribuera ditt program till AKS.
* Verifiera att programmet fungerar som det ska.
* Rikta din livetrafik till ditt nya AKS-kluster.

> [!IMPORTANT]
> Om du väljer att inte quiesce skriver, måste du replikera data till den nya distributionen. Annars missar du de data som skrevs efter att du tog diskögonblicksbilderna.

Vissa verktyg med öppen källkod kan hjälpa dig att skapa hanterade diskar och migrera volymer mellan Kubernetes-kluster:

* [Azure CLI Disk Copy-tillägget](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopierar och konverterar diskar över resursgrupper och Azure-regioner.
* [Azure Kube CLI-tillägget](https://github.com/yaron2/azure-kube-cli) räknar upp ACS Kubernetes-volymer och migrerar dem till ett AKS-kluster.


### <a name="deployment-of-your-cluster-configuration"></a>Distribution av klusterkonfigurationen

Vi rekommenderar att du använder din befintliga PIPELINE för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att distribuera en fungerande konfiguration till AKS. Du kan använda Azure Pipelines för att [skapa och distribuera dina program till AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Klona dina befintliga distributionsuppgifter och se till att pekar på det `kubeconfig` nya AKS-klustret.

Om det inte är möjligt exporterar du resursdefinitioner från ditt befintliga Kubernetes-kluster och tillämpar dem sedan på AKS. Du kan `kubectl` använda för att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Flytta befintliga resurser till en annan region

Du kanske vill flytta AKS-klustret till en [annan region som stöds av AKS][region-availability]. Vi rekommenderar att du skapar ett nytt kluster i den andra regionen och sedan distribuerar dina resurser och program till det nya klustret. Om du har några tjänster som [Azure Dev Spaces][azure-dev-spaces] som körs i AKS-klustret måste du dessutom installera och konfigurera dessa tjänster i klustret i den nya regionen.


I den här artikeln sammanfattade vi migreringsinformation för:

> [!div class="checklist"]
> * AKS med standardbelastningsutjämning och skalningsuppsättningar för virtuella datorer
> * Befintliga anslutna Azure-tjänster
> * Säkerställ giltiga kvoter
> * Hög tillgänglighet och affärskontinuitet
> * Överväganden för tillståndslösa ansökningar
> * Överväganden för tillståndskänsliga ansökningar
> * Distribution av klusterkonfigurationen


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/