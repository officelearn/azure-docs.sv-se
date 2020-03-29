---
title: Checklista för produktion beredskap för Azure Service Fabric
description: Förbered ditt Service Fabric-program och klusterproduktion genom att följa metodtips.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376808"
---
# <a name="production-readiness-checklist"></a>Checklista för produktionsberedskap

Är ditt program och kluster redo att ta produktionstrafik? Att köra och testa ditt program och ditt kluster betyder inte nödvändigtvis att det är redo att gå i produktion. Se till att programmet och klustret fungerar smidigt genom att gå igenom följande checklista. Vi rekommenderar starkt att alla dessa objekt checkas av. Självklart kan du välja att använda alternativa lösningar för en viss radartikel (till exempel dina egna diagnostikramverk).


## <a name="prerequisites-for-production"></a>Förutsättningar för produktion
1. Metodtips för Azure Service Fabric: [Programdesign,](./service-fabric-best-practices-applications.md) [Säkerhet,](./service-fabric-best-practices-security.md) [Nätverk,](./service-fabric-best-practices-networking.md) [Kapacitetsplanering och skalning,](./service-fabric-best-practices-capacity-scaling.md) [Infrastruktur som kod](./service-fabric-best-practices-infrastructure-as-code.md)samt övervakning och [diagnostik](./service-fabric-best-practices-monitoring.md). 
1. Implementera säkerhetskonfigurationen för Reliable Actors om du använder programmeringsmodellen Actors
1. Skapa en dedikerad primär nodtyp för systemtjänster för kluster med mer än 20 kärnor eller 10 noder. Lägg till [placeringsbegränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) för att reservera den primära nodtypen för systemtjänster.
1. Använd en D2v2 eller högre SKU för den primära nodtypen. Vi rekommenderar att du väljer en SKU med minst 50 GB hårddiskkapacitet.
1. Produktionskluster måste vara [säkra](service-fabric-cluster-security.md). Ett exempel på hur du konfigurerar ett säkert kluster finns i den här [klustermallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Använd vanliga namn för certifikat och undvik att använda självsignerade certifikat.
1. Lägg till [resursbegränsningar för behållare och tjänster](service-fabric-resource-governance.md)så att de inte förbrukar mer än 75 % av nodresurserna. 
1. Förstå och ställ in [hållbarhetsnivån](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Silver eller högre hållbarhetsnivå rekommenderas för nodtyper som kör tillståndskänsliga arbetsbelastningar. Den primära nodtypen bör ha en hållbarhetsnivå inställd på Silver eller högre.
1. Förstå och välj [tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) för nodtypen. Silver eller högre tillförlitlighet rekommenderas.
1. Läs in och skala testa dina arbetsbelastningar för att identifiera [kapacitetskrav](service-fabric-cluster-capacity.md) för klustret. 
1. Dina tjänster och program övervakas och programloggar genereras och lagras, med aviseringar. Se till exempel [Lägga till loggning i ditt Service Fabric-program](service-fabric-how-to-diagnostics-log.md) och [Övervakarbehållare med Azure Monitor-loggar](service-fabric-diagnostics-oms-containers.md).
1. Klustret övervakas med aviseringar (till exempel med [Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Den underliggande infrastrukturen för skalningsuppsättning för virtuella datorer övervakas med aviseringar (till exempel med [Azure Monitor-loggar](service-fabric-diagnostics-oms-agent.md).
1. Klustret har [alltid primära och sekundära certifikat](service-fabric-cluster-security-update-certs-azure.md) (så att du inte blir utelåst).
1. Underhåll separata kluster för utveckling, mellanlagring och produktion. 
1. [Programuppgraderingar](service-fabric-application-upgrade.md) och [klusteruppgraderingar](service-fabric-tutorial-upgrade-cluster.md) testas först i utvecklings- och mellanlagringskluster. 
1. Inaktivera automatiska uppgraderingar i produktionskluster och aktivera den för utvecklings- och mellanlagringskluster (återställning efter behov). 
1. Upprätta ett återställningspunktsmål (RPO) för din tjänst och konfigurera en [haveriberedskapsprocess](service-fabric-disaster-recovery.md) och testa den.
1. Planera för [skalning av](service-fabric-cluster-scaling.md) klustret manuellt eller programmässigt.
1. Planera för [att korrigera](service-fabric-patch-orchestration-application.md) klusternoderna. 
1. Upprätta en CI/CD-pipeline så att de senaste ändringarna testas kontinuerligt. Till exempel med Hjälp av [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) eller [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Testa din utveckling & mellanlagringskluster under belastning med [felanalystjänsten](service-fabric-testability-overview.md) och framkalla kontrollerat [kaos](service-fabric-controlled-chaos.md). 
1. Planera för [att skala](service-fabric-concepts-scalability.md) dina program. 


Om du använder programmeringsmodellen Service Fabric Reliable Services eller Reliable Actors måste följande objekt bockas av:
1. Uppgradera program under lokal utveckling för att kontrollera att din `RunAsync` tjänstkod respekterar annulleringstoken i metoden och stänger anpassade kommunikationsavlyssnare.
1. Undvik [vanliga fallgropar](service-fabric-work-with-reliable-collections.md) när du använder tillförlitliga samlingar.
1. Övervaka .NET CLR-minnesprestandaräknare när du kör belastningstester och kontrollera om det finns högbelastningshastigheter för skräpinsamling eller skenande heap-tillväxt.
1. Underhåll offlinebackup av [reliable services och reliable actors](service-fabric-reliable-services-backup-restore.md) och testa återställningsprocessen.
1. Antalet instanser av primär nodeType-instans bör helst vara lika med det minimum som krävs för tillförlitlighetsnivån för kluster. villkor när det är lämpligt att överskrida nivån minimum inkluderar: tillfälligt när vertikalt skala din primära NodeTypes Virtual Machine Scale Set SKU.

## <a name="optional-best-practices"></a>Metodtips för bästa val

Även om ovanstående förteckningar är förutsättningar att gå i produktion, bör följande poster också beaktas:
1. Anslut till [hälsomodellen Service Fabric](service-fabric-health-introduction.md) för att utöka den inbyggda hälsoutvärderingen och rapporteringen.
1. Distribuera en anpassad watchdog som övervakar ditt program och rapporterar [belastning](service-fabric-cluster-resource-manager-metrics.md) för [resursbalansering](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Windows-kluster för tjänstinfrastruktur](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuera ett Linux-kluster för service fabric](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
