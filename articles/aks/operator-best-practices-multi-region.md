---
title: Hög tillgänglighet och katastrofåterställning recovery i Azure Kubernetes Service (AKS)
description: Läs en kluster-operator bästa praxis för att uppnå högsta tillgänglighet för dina program, vilket ger hög tillgänglighet och förbereda för katastrofåterställning i Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475164"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Metodtips för företag affärskontinuitet och haveriberedskap i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) viktigare drifttid. AKS ger hög tillgänglighet med hjälp av flera noder i en tillgänglighetsuppsättning. Men dessa flera noder skydda inte datorn från en region-fel. Planera framåt och upprätthålla affärskontinuitet och förbereda för katastrofåterställning för att maximera din drifttid.

Den här artikeln handlar om hur du planerar för affärskontinuitet och haveriberedskap i AKS. Lär dig att:

> [!div class="checklist"]
> * Planera för AKS-kluster i flera regioner.
> * Dirigera trafik mellan flera kluster med hjälp av Azure Traffic Manager.
> * Använda geo-replikering för din avbildning behållarregister.
> * Planera för programtillståndet över flera kluster.
> * Replikera lagring i flera regioner.

## <a name="plan-for-multiregion-deployment"></a>Planera för distribution av multiregion

**Bästa praxis**: När du distribuerar flera AKS-kluster, Välj regioner med AKS och använda länkade regioner.

Ett AKS-kluster distribueras till en enda region. Distribuera programmet till flera AKS-kluster för att skydda datorn från region fel över olika regioner. När du planerar att distribuera AKS-klustret bör du överväga att:

* [**Regiontillgänglighet för AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Väljer du regioner nära användarna. AKS expanderar kontinuerligt till nya regioner.
* [**Parade Azure-regioner**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): För din region, väljer du två regioner som är länkat till varandra. Länkade regioner samordna plattformsuppdateringar och prioritera recovery åtgärder där det behövs.
* **Tjänsttillgänglighet**: Bestäm om din länkade regioner ska vara frekvent/hot, frekvent/varm eller varma/kalla. Vill du köra båda regionerna samtidigt med en region *redo* att starta trafik? Eller vill du ta en region har tid att hämta redo att hantera trafik?

AKS regiontillgänglighet och länkade regioner är gemensamma ersättning. Distribuera dina AKS-kluster i hopparade regioner som är utformade för att hantera haveriberedskap för regionen tillsammans. AKS är till exempel tillgängligt i östra USA och västra USA. Dessa regioner är länkad. Välj de här två regionerna när du skapar en AKS BC/DR-strategi.

När du distribuerar ditt program kan du lägga till ytterligare ett steg till din CI/CD-pipeline för att distribuera till dessa flera AKS-kluster. Om du inte uppdaterar din behållarutvecklings kan program distribueras till endast en av dina regioner och AKS-kluster. Kund-trafik som dirigeras till en sekundär region inte tar emot de senaste uppdateringarna i kod.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använda Azure Traffic Manager kan dirigera trafik

**Bästa praxis**: Azure Traffic Manager kan dirigera kunderna till sin närmaste appinstansen och AKS-kluster och program. För bästa prestanda och redundans kan dirigera all trafik via Traffic Manager innan den försätts i AKS-klustret.

Om du har flera AKS-kluster i olika regioner, kan du använda Traffic Manager för att styra hur trafiken flödar till de program som körs i varje kluster. [Med Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) är en DNS-baserad trafik belastningsutjämnare som kan distribuera trafik mellan regioner. Använd Traffic Manager för att skicka användare baserat på svarstid för klustret eller baserat på geografiskt område.

![AKS med Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Kunder som har ett AKS kluster vanligtvis ansluta till tjänsten IP-Adressen eller DNS-namnet på ett visst program. Kunder bör ansluta till ett Traffic Manager DNS-namn som pekar på tjänster på varje AKS-kluster i en distribution av multicluster. Definiera dessa tjänster med hjälp av Traffic Manager-slutpunkter. Varje slutpunkt är den *IP load balancer-tjänsten*. Du kan använda den här konfigurationen för att dirigera nätverkstrafik från Traffic Manager-slutpunkt i en region till slutpunkten i en annan region.

![Geografisk Routning via Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager utför DNS-sökning och returnerar en användares lämpligaste slutpunkten. Kapslade profiler kan prioritera en primär plats. Användare bör till exempel vanligtvis ansluta till sin närmaste geografiska region. Om det uppstår ett problem i den regionen hjälper Traffic Manager i stället användarna till en sekundär region. Den här metoden garanterar att kunderna kan ansluta till en programinstans även om deras närmaste geografiska region som är tillgänglig.

Information om hur du ställer in slutpunkter och routning finns i [konfigurera geografisk routningsmetod med hjälp av Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Layer 7 programmet routning med Azure ytterdörren Service

Traffic Manager använder DNS (nivå 3) för att forma trafik. [Azure ytterdörren Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) innehåller ett alternativ för routning av HTTP/HTTPS (layer 7). Ytterligare funktioner i Azure ytterdörren Service inkluderar SSL-avslutning, anpassad domän, Brandvägg för webbaserade program, URL-Omskrivningsregler och sessionstillhörighet. Granska din programtrafik behov att förstå vilken lösning som är den lämpligaste.

## <a name="enable-geo-replication-for-container-images"></a>Aktivera geo-replikering för behållaravbildningar

**Bästa praxis**: Store dina behållaravbildningar i Azure Container Registry och geo-replikera registret för varje AKS-region.

Om du vill distribuera och köra dina program i AKS, behöver du ett sätt att lagra och hämta behållaravbildningarna. Container Registry integrerar med AKS, så att den kan på ett säkert sätt lagra dina avbildningar eller Helm-diagram. Container Registry har stöd för multimaster geo-replikering för att automatiskt replikera dina avbildningar till Azure-regioner runtom i världen. 

För att förbättra prestanda och tillgänglighet, att använda geo-replikering för Container Registry för att skapa ett register i varje region där du har ett AKS-kluster. Varje AKS-kluster hämtar sedan behållaravbildningar från lokala behållarregistret i samma region:

![Container Registry geo-replikering för behållaravbildningar](media/operator-best-practices-bc-dr/acr-geo-replication.png)

När du använder geo-replikering för Container Registry för pull-avbildningar från samma region är resultatet:

* **Snabbare**: Du kan hämta avbildningar från nätverksanslutningar med hög hastighet och låg latens för inom samma Azure-region.
* **Mer tillförlitliga**: Om en region inte är tillgänglig hämtar AKS-klustret avbildningar från ett tillgängligt container registry.
* **Billigare**: Det är kostnadsfritt för utgående nätverk mellan datacenter.

GEO-replikering är en funktion i *Premium* SKU-behållarregister. Information om hur du konfigurerar geo-replikering finns i [geo-replikering för Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Ta bort tjänsten ändras från i behållare

**Bästa praxis**: Om möjligt, lagra inte tjänsttillstånd i behållaren. I stället använda en Azure-plattformen som en tjänst (PaaS) som stöder replikering.

*Tjänsten tillstånd* refererar till data i minnet eller på disken som en tjänst kräver att fungera. Tillstånd innehåller datastrukturer och medlemsvariabler som tjänsten läser och skriver. Beroende på hur tjänsten har byggts, kan tillståndet även innehålla filer eller andra resurser som är lagrade på disken. Tillståndet kan exempelvis innehålla filer som en databas som använder för att lagra data och transaktionsloggar.

Tillstånd kan vara antingen externalized eller samordnat med kod som hanterar tillstånd. Du gör normalt tillstånd med hjälp av en databas eller annat datalager som körs på olika datorer över nätverket eller som kör processer på samma dator.

Behållare och mikrotjänster är mest flexibla när de processer som körs i dem inte bibehåller också tillstånd. Eftersom program innehåller nästan alltid vissa tillstånd, använder du en PaaS-lösning som Azure Database för MySQL, Azure Database för PostgreSQL eller Azure SQL Database.

Se följande riktlinjer för att bygga bärbar program:

* [Metodiken som 12 faktorer](https://12factor.net/)
* [Kör ett webbprogram i flera Azure-regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Skapa en migreringsplan för lagring

**Bästa praxis**: Om du använder Azure Storage, förbereda och testa hur du migrerar din lagring från den primära regionen till backup-region.

Dina program kan använda Azure Storage för sina data. Eftersom dina program är fördelade på flera AKS-kluster i olika regioner, behöver du förhindra att lagringen som synkroniseras. Här följer två vanliga sätt att replikera lagring:

* WMI-baserade asynkron replikering
* Programbaserade asynkron replikering

### <a name="infrastructure-based-asynchronous-replication"></a>WMI-baserade asynkron replikering

Dina program kan kräva beständig lagring, även när en pod har tagits bort. Du kan använda beständiga volymer i Kubernetes, för att bevara datalagring. Beständiga volymer är monterad på en nod VM och sedan visas poddarna. Beständiga volymer Följ poddar även om poddarna flyttas till en annan nod i samma kluster.

Replikeringsstrategi som du använder beror på din lagringslösning. Vanliga lagringslösningar som [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [tornet](https://rook.io/docs/rook/master/disaster-recovery.html), och [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) ge sina egna vägledning om haveriberedskap och replikering.

Vanliga strategin är att tillhandahålla en gemensam lagringsplats där programmen kan skriva sina data. Dessa data replikeras sedan över regioner och sedan åt lokalt.

![WMI-baserade asynkron replikering](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Om du använder Azure Managed Disks kan du replikering och DR-lösningar som dessa:

* [Velero på Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Programbaserade asynkron replikering

Kubernetes tillhandahåller för närvarande ingen ursprunglig implementering för programbaserade asynkron replikering. Eftersom behållare och Kubernetes är löst sammansatta, bör en traditionell metod som programmet eller språk fungera. Själva programmen replikera normalt lagringsbegäranden som skrivs sedan till varje kluster underliggande lagring av data.

![Programbaserade asynkron replikering](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln handlar om affärskontinuitet och överväganden kring haveriberedskap för AKS-kluster. Mer information om kluster i AKS finns i de här artiklarna om bästa praxis:

* [Isolering för flera innehavare och kluster][aks-best-practices-cluster-isolation]
* [Grundläggande funktioner som Kubernetes scheduler][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
