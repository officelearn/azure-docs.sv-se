---
title: Hög tillgänglighet och haveriberedskap i Azure Kubernetes Service (AKS)
description: Lär dig en klusteroperatörs bästa praxis för att uppnå maximal drifttid för dina program, tillhandahålla hög tillgänglighet och förbereda för haveriberedskap i Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 3c1f0bb715b3c3bf9b3a3350ab11e26834aa84c8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528649"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Metodtips för kontinuitet och haveriberedskap i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) blir programdrifttid viktigt. AKS ger hög tillgänglighet genom att använda flera noder i en tillgänglighetsuppsättning. Men dessa flera noder skyddar inte ditt system från ett regionfel. För att maximera din drifttid, planera i förväg för att upprätthålla kontinuiteten i verksamheten och förbereda sig för haveriberedskap.

Den här artikeln fokuserar på hur du planerar för affärskontinuitet och haveriberedskap i AKS. Lär dig att:

> [!div class="checklist"]
> * Planera för AKS-kluster i flera regioner.
> * Dirigera trafik över flera kluster med hjälp av Azure Traffic Manager.
> * Använd georeplikering för behållaravbildningsregister.
> * Planera för programtillstånd i flera kluster.
> * Replikera lagring i flera regioner.

## <a name="plan-for-multiregion-deployment"></a>Planera för distribution i flera regioner

**Bästa praxis:** När du distribuerar flera AKS-kluster väljer du regioner där AKS är tillgängligt och använder parade regioner.

Ett AKS-kluster distribueras till en enda region. För att skydda ditt system från regionfel distribuerar du ditt program till flera AKS-kluster i olika regioner. När du planerar var du ska distribuera AKS-klustret bör du tänka på följande:

* [**AKS-regionens tillgänglighet**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Välj regioner nära användarna. AKS expanderar kontinuerligt till nya regioner.
* [**Azure-parade regioner**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): För ditt geografiska område väljer du två regioner som är ihopkopplade med varandra. Parade regioner samordnar plattformsuppdateringar och prioriterar återställningsinsatser där det behövs.
* **Tjänstens tillgänglighet**: Bestäm om dina parade områden ska vara varma/varma, varma/varma eller varma/kalla. Vill du köra båda regionerna samtidigt, med en region *redo* att börja betjäna trafik? Eller vill du att en region ska ha tid att göra sig redo att betjäna trafiken?

AKS-regionens tillgänglighet och parade regioner är en gemensam faktor. Distribuera dina AKS-kluster i parade regioner som är utformade för att hantera regionkatastrofåterställning tillsammans. Aks är till exempel tillgängligt i östra USA och västra USA. Dessa regioner är parade. Välj dessa två regioner när du skapar en AKS BC/DR-strategi.

När du distribuerar ditt program lägger du till ytterligare ett steg i CI/CD-pipelinen för att distribuera till dessa flera AKS-kluster. Om du inte uppdaterar dina distributionspipelines kan program distribueras till endast en av dina regioner och AKS-kluster. Kundtrafik som dirigeras till en sekundär region får inte de senaste koduppdateringarna.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använda Azure Traffic Manager för att dirigera trafik

**Bästa praxis:** Azure Traffic Manager kan dirigera kunder till deras närmaste AKS-kluster och programinstans. För bästa prestanda och redundans kan du styra all programtrafik via Traffic Manager innan den går till AKS-klustret.

Om du har flera AKS-kluster i olika regioner använder du Traffic Manager för att styra hur trafiken flödar till de program som körs i varje kluster. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) är en DNS-baserad trafikbelastningsutjämningsfaktor som kan distribuera nätverkstrafik mellan regioner. Använd Traffic Manager för att dirigera användare baserat på klustersvarstid eller baserat på geografi.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Kunder som har ett enda AKS-kluster ansluter vanligtvis till tjänstens IP- eller DNS-namn för ett visst program. I en multicluster-distribution bör kunder ansluta till ett Traffic Manager DNS-namn som pekar på tjänsterna i varje AKS-kluster. Definiera dessa tjänster med hjälp av Traffic Manager-slutpunkter. Varje slutpunkt är *tjänsten belastningsutjämnare IP*. Använd den här konfigurationen för att dirigera nätverkstrafik från Traffic Manager-slutpunkten i en region till slutpunkten i en annan region.

![Geografisk routning via Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager utför DNS-sökning och returnerar en användares lämpligaste slutpunkt. Kapslade profiler kan prioritera en primär plats. Användare bör till exempel i allmänhet ansluta till sin närmaste geografiska region. Om den regionen har problem dirigerar Traffic Manager i stället användarna till en sekundär region. Den här metoden säkerställer att kunder kan ansluta till en programinstans även om deras närmaste geografiska region inte är tillgänglig.

Information om hur du ställer in slutpunkter och routning finns i [Konfigurera den geografiska trafikroutningsmetoden med hjälp av Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Layer 7-programroutning med Azure Front Door Service

Traffic Manager använder DNS (lager 3) för att forma trafik. [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) tillhandahåller ett HTTP/HTTPS-routningsalternativ (layer 7). Ytterligare funktioner i Azure Front Door Service inkluderar TLS-avslutning, anpassad domän, brandvägg för webbprogram, URL-omskrivning och sessionstillhörighet. Granska behoven hos din programtrafik för att förstå vilken lösning som är mest lämplig.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Koppla samman regioner med global virtuell nätverks peering

Om klustren behöver prata med varandra kan anslutning av båda virtuella nätverk till varandra uppnås genom [virtuell nätverks peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Den här tekniken kopplar samman virtuella nätverk till varandra och ger hög bandbredd i Microsofts stamnät, även mellan olika geografiska regioner.

En förutsättning för att peer de virtuella nätverk där AKS kluster körs är att använda standard Load Balancer i AKS-klustret, så att Kubernetes tjänster kan nås över den virtuella nätverks peering.

## <a name="enable-geo-replication-for-container-images"></a>Aktivera georeplikering för behållaravbildningar

**Bästa praxis:** Lagra behållaravbildningar i Azure Container Registry och geo-replikera registret till varje AKS-region.

Om du vill distribuera och köra dina program i AKS behöver du ett sätt att lagra och hämta behållaravbildningarna. Container Registry integreras med AKS, så att den säkert kan lagra dina behållaravbildningar eller Helm-diagram. Container Registry stöder georeplikering med flera hanterare för att automatiskt replikera dina avbildningar till Azure-regioner runt om i världen. 

Om du vill förbättra prestanda och tillgänglighet använder du Georeplikering av behållarregistret för att skapa ett register i varje region där du har ett AKS-kluster. Varje AKS-kluster hämtar sedan behållaravbildningar från det lokala behållarregistret i samma region:

![Georeplikering av behållareregister för behållaravbildningar](media/operator-best-practices-bc-dr/acr-geo-replication.png)

När du använder Georeplikering av behållarregistret för att hämta avbildningar från samma region blir resultatet:

* **Snabbare**: Du hämtar bilder från nätverksanslutningar med låg latens med låg hastighet inom samma Azure-region.
* **Mer tillförlitlig**: Om en region inte är tillgänglig hämtar AKS-klustret avbildningarna från ett tillgängligt behållarregister.
* **Billigare:** Det finns ingen nätverksutgående avgift mellan datacenter.

Geo-replikering är en funktion i *Premium* SKU-behållarregister. Information om hur du konfigurerar geo-replikering finns i [Geo-replikering av behållareregister](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Ta bort servicetillstånd från behållare inuti

**Bästa praxis**: Lagra inte servicetillstånd i behållaren om det är möjligt. Använd i stället en Azure-plattform som en tjänst (PaaS) som stöder multiregionreplikering.

*Tjänsttillstånd* avser de minnes- eller diskdata som en tjänst behöver för att fungera. Tillståndet innehåller de datastrukturer och medlemsvariabler som tjänsten läser och skriver. Beroende på hur tjänsten är skapad kan tillståndet även innehålla filer eller andra resurser som lagras på disken. Tillståndet kan till exempel innehålla de filer som en databas använder för att lagra data och transaktionsloggar.

Tillstånd kan antingen externaliseras eller samlokaliseras med koden som manipulerar tillståndet. Vanligtvis externaliserar du tillstånd med hjälp av en databas eller annat datalager som körs på olika datorer över nätverket eller som tar på processen på samma dator.

Behållare och mikrotjänster är mest motståndskraftiga när de processer som körs inuti dem inte behåller tillstånd. Eftersom program nästan alltid innehåller vissa tillstånd använder du en PaaS-lösning som Azure Database for MySQL, Azure Database for PostgreSQL eller Azure SQL Database.

Så här skapar du bärbara program:

* [Den 12-faktor app metodik](https://12factor.net/)
* [Köra ett webbprogram i flera Azure-regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Skapa en lagringsmigreringsplan

**Bästa praxis:** Om du använder Azure Storage förbereder och testar du hur du migrerar lagringsutrymmet från den primära regionen till säkerhetskopieringsregionen.

Dina program kan använda Azure Storage för sina data. Eftersom dina program är spridda över flera AKS-kluster i olika regioner måste du synkronisera lagringen. Här är två vanliga sätt att replikera lagring:

* Infrastrukturbaserad asynkron replikering
* Programbaserad asynkron replikering

### <a name="infrastructure-based-asynchronous-replication"></a>Infrastrukturbaserad asynkron replikering

Dina program kan kräva beständig lagring även efter att en pod har tagits bort. I Kubernetes kan du använda beständiga volymer för att bevara datalagring. Beständiga volymer monteras på en virtuell nod-VM och exponeras sedan för poddar. Beständiga volymer följer poddar även om poddar flyttas till en annan nod i samma kluster.

Vilken replikeringsstrategi du använder beror på din lagringslösning. Vanliga lagringslösningar som [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph,](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/) [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)och [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) ger sin egen vägledning om haveriberedskap och replikering.

Den typiska strategin är att tillhandahålla en gemensam lagringsplats där program kan skriva sina data. Dessa data replikeras sedan mellan regioner och sedan nås lokalt.

![Infrastrukturbaserad asynkron replikering](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Om du använder Azure Managed Disks kan du välja replikerings- och DR-lösningar som dessa:

* [Velero på Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Programbaserad asynkron replikering

Kubernetes tillhandahåller för närvarande ingen inbyggd implementering för programbaserad asynkron replikering. Eftersom behållare och Kubernetes är löst kopplade, bör alla traditionella program eller språk tillvägagångssätt fungera. Vanligtvis replikerar programmen själva lagringsbegäranden, som sedan skrivs till varje klusters underliggande datalagring.

![Programbaserad asynkron replikering](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på kontinuitet och katastrofåterställningsöverväganden för AKS-kluster. Mer information om klusteråtgärder i AKS finns i följande artiklar om metodtips:

* [Multitenancy och klusterisolering][aks-best-practices-cluster-isolation]
* [Grundläggande kubernetes-schemaläggare][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
