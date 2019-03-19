---
title: Operatorn metodtips – hög tillgänglighet och katastrofåterställning i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för högsta tillgänglighet för dina program att tillhandahålla hög tillgänglighet och förbereda för disaster recovery situationer i Azure Kubernetes Services (AKS)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098635"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Metodtips för företag affärskontinuitet och haveriberedskap i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) viktigare drifttid. AKS ger hög tillgänglighet med hjälp av flera noder i en tillgänglighetsuppsättning. Dessa flera noder skydda inte dig från ett fel region. Maximera din drifttid genom att implementera vissa kontinuitet för företag och funktioner för katastrofåterställning.

Den här bästa praxis som artikeln handlar om överväganden som hjälper dig att planera för företag affärskontinuitet och haveriberedskap i AKS. Lär dig att:

> [!div class="checklist"]
> * Planera för AKS-kluster i flera regioner
> * Dirigera trafik mellan flera kluster med Azure Traffic Manager
> * Använda geo-replikering för din avbildning för behållarregister
> * Planera för programtillståndet över flera kluster
> * Replikera lagring i flera regioner

## <a name="plan-for-multi-region-deployment"></a>Planera för distribution i flera regioner

**Bästa praxis riktlinjer** – när du distribuerar flera AKS-kluster, Välj regioner med AKS och använda länkade regioner.

Ett AKS-kluster distribueras till en enda region. Distribuera programmet till flera AKS-kluster för att skydda dig mot fel region, över olika regioner. När du planerar vilka regioner för att distribuera ditt AKS-kluster gäller följande:

* [AKS regiontillgänglighet](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Väljer du regioner nära användarna. AKS utökar kontinuerligt till nya regioner.
* [Azure länkade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * För din region, väljer du två regioner som är länkat till varandra. Dessa regioner samordna plattformsuppdateringar och prioritera recovery åtgärder där det behövs.
* Tillgänglighet servicenivå (frekvent/Hot, frekvent/varmt, varma/kalla)
  * Vill du köra båda regionerna samtidigt med en region *redo* att börja leverera trafik eller en region som behöver tid att hämta redo att hantera trafik.

AKS regiontillgänglighet och länkade regioner är gemensamma beräkningen. Distribuera dina AKS-kluster i hopparade regioner som är utformade för att hantera haveriberedskap för regionen tillsammans. Till exempel AKS är tillgängliga i *USA, östra* och *västra USA*. Dessa regioner är också kopplad. De här två regionerna skulle rekommenderas när du skapar en AKS BC/DR-strategi.

När du distribuerar ditt program måste du också lägga till ytterligare ett steg till din CI/CD-pipeline för att distribuera till dessa flera AKS-kluster. Om du inte uppdaterar din behållarutvecklings, distribueras distribution av program bara till en av dina regioner och AKS-kluster. Kund-trafik som dirigeras till en sekundär region inte tar emot de senaste uppdateringarna i kod.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använda Azure Traffic Manager kan dirigera trafik

**Bästa praxis riktlinjer** – Azure Traffic Manager kan dirigera kunderna till sin närmaste appinstansen och AKS-kluster och program. För bästa prestanda och redundans kan dirigera all trafik via Traffic Manager innan du fortsätter till AKS-klustret.

Med flera AKS-kluster i olika regioner, som du vill kontrollera hur trafiken dirigeras till de program som körs i varje kluster. [Med Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) är en DNS-baserad trafik belastningsutjämnare som kan distribuera trafik mellan regioner. Du kan dirigera användare baserat på svarstid för klustret eller baserat på geografiskt område.

![AKS med Azure Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Med ett enda AKS-kluster, kunder vanligtvis ansluta till den *Service IP* eller DNS-namnet på ett visst program. Kunder bör ansluta till ett Traffic Manager DNS-namn som pekar på tjänster på varje AKS-kluster i en distribution med flera kluster. Dessa tjänster definieras med hjälp av Traffic Manager-slutpunkter. Varje slutpunkt är den *IP för tjänsten Load Balancer*. Den här konfigurationen kan du dirigera nätverkstrafik från Traffic Manager-slutpunkt i en region till slutpunkten i en annan region.

![Geografisk routning med Azure Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager används för att utföra DNS-sökning och returnera den lämpligaste slutpunkten för en användare. Kapslade profiler kan användas med prioritet för en primär plats. Till exempel att en användare i första hand ska ansluta till sin närmaste geografiska region. Om det uppstår ett problem i den regionen dirigeras Traffic Manager i stället inte till en sekundär region. Den här metoden gör att kunder kan alltid ansluta till en programinstans, även om deras närmaste geografiska region som är tillgänglig.

Stegvisa instruktioner för hur du konfigurerar dessa slutpunkter och routning, se [konfigurera geografisk routningsmetod som med Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Layer 7 programmet routning med Azure ytterdörren

Med Azure Traffic Manager använder DNS (nivå 3) för att forma trafik. [Azure ytterdörren (för närvarande i förhandsversion)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) innehåller ett alternativ för routning av HTTP/HTTPS (layer 7). Ytterligare funktioner för ytterdörren inkluderar SSL-avslutning, anpassad domän, Brandvägg för webbaserade program, URL-Omskrivningsregler och Sessionstillhörighet.

Granska din programtrafik behov att förstå vilken lösning som är den lämpligaste.

## <a name="enable-geo-replication-for-container-images"></a>Aktivera geo-replikering för behållaravbildningar

**Bästa praxis riktlinjer** -Store dina behållaravbildningar i Azure Container Registry och geo-replikera registret för varje AKS-region.

Om du vill distribuera och köra dina program i AKS, behöver du ett sätt att lagra och hämta behållaravbildningarna. Azure Container Registry (ACR) kan integreras med AKS för säker lagring av dina behållaravbildningar eller Helm-diagram. ACR stöder flera huvudservrar geo-replikering för att automatiskt replikera dina avbildningar till Azure-regioner runtom i världen. Använd ACR geo-replikering för att förbättra prestanda och tillgänglighet, skapa ett register i varje region där du har ett AKS-kluster. Varje AKS-kluster hämtar sedan behållaravbildningar från det lokala ACR-registret i samma region:

![Azure Container Registry geo-replikering för behållaravbildningar](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Fördelarna med att använda ACR geo-replikering inkluderar följande:

* **Det går snabbare att hämta avbildningar från samma region.** Du hämtar bilder från hög hastighet med låg latens nätverksanslutningar inom samma Azure-region.
* **Hämta avbildningar från samma region är mer tillförlitlig.** Om en region inte är tillgänglig hämtar avbildningen från en annan ACR-registret som finns kvar i AKS-klustret.
* **Hämta avbildningar från samma region är billigare.** Det är kostnadsfritt för utgående nätverk mellan datacenter.

GEO-replikering är en funktion i *Premium* SKU ACR-register. Stegvisa instruktioner för hur du konfigurerar replikering finns [geo-replikering för Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Ta bort tjänsten ändras från i behållare

**Bästa praxis riktlinjer** – där det är möjligt, lagra inte tjänsttillstånd i behållaren. Använd i stället Azure PaaS-tjänster som har stöd för replikering av flera regioner.

Tjänsttillstånd refererar till data i minnet eller på disken som en tjänst kräver att fungera. Tillstånd innehåller datastrukturer och medlemsvariabler som tjänsten läser och skriver. Beroende på hur tjänsten har byggts, innefatta tillståndet också filer eller andra resurser som är lagrade på disken. Till exempel filerna en databas använder att lagra data och transaktionsloggar.

Tillstånd kan vara antingen externalized eller samordnat med koden som manipulera tillståndet. Externalization av tillståndet görs normalt med hjälp av en databas eller annat datalager som körs på olika datorer över nätverket eller processer på samma dator.

Behållare och mikrotjänster är mest flexibla när de processer som körs i dem inte sparar tillstånd. När dina program nästan alltid innehålla vissa tillstånd, måste du använda en plattform som en tjänst-lösning som Azure Database för MySQL/Postgres eller Azure SQL.

Mer information om att skapa program som är mer portabel, finns följande riktlinjer:

* [The Twelve-Factor App Methodology](https://12factor.net/).
* [Kör ett webbprogram i flera Azure-regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Skapa en migreringsplan för lagring

**Bästa praxis riktlinjer** – om du använder Azure Storage, förbereda och testa migrera lagringen från primärt till regionen säkerhetskopiering.

Dina program kan använda Azure Storage för sina data. När dina program är fördelade på flera AKS-kluster i olika regioner, måste du förhindra att lagringen som synkroniseras. Två vanliga sätt för att replikera lagring omfattar följande metoder:

* Programbaserade asynkron replikering
* WMI-baserade asynkron replikering

### <a name="infrastructure-based-asynchronous-replication"></a>WMI-baserade asynkron replikering

Dina program kan kräva beständig lagring, även när en pod har tagits bort. Du kan använda beständiga volymer i Kubernetes, för att bevara datalagring. Volymerna beständiga är monterad till VM-noden och sedan visas poddarna. Beständiga volymer Följ poddar, även om en pod flyttas till en annan nod i samma kluster.

Replikering strategier kan skilja sig beroende på lösningen lagringsanvändning. Vanliga lagringslösningar som [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [tornet](https://rook.io/docs/rook/master/disaster-recovery.html), och [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) alla har sina egna vägledning.

Den centrala metoden är en gemensam tidpunkt för lagring för program att skriva sina data. Dessa data replikeras sedan över regioner och sedan åt lokalt.

![WMI-baserade asynkron replikering](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Om du använder Azure Managed Disks är tillgängliga replikering och DR-lösningar med någon av följande metoder:

* [Ark i Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Programbaserade asynkron replikering

Det finns för närvarande inga Kubernetes-interna implementeringen för programbaserade asynkron replikering. En traditionell metod som programmet eller språk bör fungera med löst kopplade karaktär behållare och Kubernetes. Den centrala metoden är för själva programmen att replikera lagringsbegäranden som skrivs sedan till varje kluster underliggande lagring av data.

![Programbaserade asynkron replikering](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på affärskontinuitet och haveriberedskap överväganden i AKS-kluster. Mer information om kluster i AKS finns i följande metoder:

* [Isolering för flera innehavare och kluster][aks-best-practices-cluster-isolation]
* [Grundläggande funktioner som Kubernetes scheduler][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
