---
title: Översikt över övervakning och diagnostik i Azure Service Fabric
description: Lär dig mer om övervakning och diagnostik för Azure Service Fabric-kluster, program och tjänster.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282489"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervakning och diagnostik för Azure Service Fabric

Den här artikeln innehåller en översikt över övervakning och diagnostik för Azure Service Fabric. Övervakning och diagnostik är avgörande för att utveckla, testa och distribuera arbetsbelastningar i alla molnmiljöer. Du kan till exempel spåra hur dina program används, de åtgärder som vidtas av Service Fabric-plattformen, resursutnyttjandet med prestandaräknare och klustrets allmänna hälsotillstånd. Du kan använda den här informationen för att diagnostisera och korrigera problem och förhindra att de inträffar i framtiden. De närmaste avsnitten kommer kortfattat att förklara varje område av Service Fabric-övervakning att tänka på för produktionsarbetsbelastningar. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Programövervakning
Programövervakning spårar hur funktioner och komponenter i ditt program används. Du vill övervaka dina program för att se till att problem som påverkar användare fångas. Ansvaret för applikationsövervakning är att användarna utvecklar ett program och dess tjänster eftersom det är unikt för affärslogiken i ditt program. Övervakning av dina program kan vara användbart i följande scenarier:
* Hur mycket trafik upplever mitt program? - Behöver du skala dina tjänster för att möta användarnas krav eller ta itu med en potentiell flaskhals i din ansökan?
* Lyckas och spåras min service till servicesamtal?
* Vilka åtgärder vidtas av användarna av mitt program? - Insamling av telemetri kan styra framtida funktionsutveckling och bättre diagnostik för applikationsfel
* Är min ansökan kasta ohanterade undantag? 
* Vad händer inom de tjänster som körs i mina behållare?

Det fina med ansökan övervakning är att utvecklare kan använda vad verktyg och ram de vill eftersom det lever inom ramen för din ansökan! Du kan läsa mer om Azure-lösningen för programövervakning med Azure Monitor - Application Insights in [Event analysis med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Vi har också en handledning med hur [du ställer in detta för .NET Applications](service-fabric-tutorial-monitoring-aspnet.md). Den här självstudien går igenom hur du installerar rätt verktyg, ett exempel för att skriva anpassad telemetri i ditt program och visa programdiagnostik och telemetri i Azure-portalen. 


## <a name="platform-cluster-monitoring"></a>Plattformsövervakning (kluster)
En användare har kontroll över vad telemetri kommer från deras program eftersom en användare skriver själva koden, men hur är det med diagnostiken från Service Fabric-plattformen? Ett av Service Fabrics mål är att hålla program motståndskraftiga mot maskinvarufel. Det här målet uppnås genom plattformens systemtjänsters förmåga att identifiera infrastrukturproblem och snabbt redundansarbetsbelastningar till andra noder i klustret. Men i detta särskilda fall, tänk om systemtjänsterna själva har problem? Eller om regler för placering av tjänster överträds när du försöker distribuera eller flytta en arbetsbelastning? Service Fabric tillhandahåller diagnostik för dessa och mer för att se till att du informeras om aktivitet som äger rum i klustret. Några exempelscenarier för klusterövervakning är:

Service Fabric erbjuder en omfattande uppsättning händelser ur lådan. Dessa [Service Fabric-händelser](service-fabric-diagnostics-events.md) kan nås via EventStore eller driftskanalen (händelsekanal som exponeras av plattformen). 

* Service Fabric-händelsekanaler - På Windows är Service Fabric-händelser tillgängliga från `logLevelKeywordFilters` en enda ETW-leverantör med en uppsättning relevanta som används för att välja mellan operativa och data & meddelandekanaler - det är så vi separerar utgående Service Fabric-händelser som ska filtreras efter behov. På Linux kommer Service Fabric-händelser via LTTng och placeras i en lagringstabell, varifrån de kan filtreras efter behov. Dessa kanaler innehåller kurerade, strukturerade händelser som kan användas för att bättre förstå klustrets tillstånd. Diagnostik aktiveras som standard vid tiden för att skapa kluster, som skapar en Azure Storage-tabell där händelserna från dessa kanaler skickas så att du kan fråga i framtiden. 

* EventStore - EventStore är en funktion som erbjuds av plattformen som tillhandahåller Plattformshändelser för Service Fabric som är tillgängliga i Service Fabric Explorer och via REST API. Du kan se en ögonblicksbildvy av vad som händer i klustret för varje entitet, t.ex. Du kan också läsa mer om EventStore på [EventStore Översikt](service-fabric-diagnostics-eventstore.md).    

![EventStore (På plats)](media/service-fabric-diagnostics-overview/eventstore.png)

Diagnostiken som tillhandahålls är i form av en omfattande uppsättning händelser ur lådan. Dessa [Service Fabric-händelser](service-fabric-diagnostics-events.md) illustrerar åtgärder som görs av plattformen på olika entiteter, till exempel noder, program, tjänster, partitioner etc. I det sista scenariot ovan, om en nod skulle `NodeDown` gå ner, skulle plattformen avge en händelse och du kan meddelas omedelbart av ditt övervakningsverktyg val. Andra vanliga `ApplicationUpgradeRollbackStarted` exempel `PartitionReconfigured` är eller under en redundansväxling. **Samma händelser är tillgängliga på både Windows- och Linux-kluster.**

Händelserna skickas via standardkanaler på både Windows och Linux och kan läsas av alla övervakningsverktyg som stöder dessa. Azure Monitor-lösningen är Azure Monitor-loggar. Läs gärna mer om vår [Azure Monitor-loggintegration](service-fabric-diagnostics-event-analysis-oms.md) som innehåller en anpassad instrumentpanel för ditt kluster och några exempelfrågor som du kan skapa aviseringar från. Fler klusterövervakningskoncept finns tillgängliga på [plattformsnivå händelse- och logggenerering](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric-plattformen innehåller en hälsomodell som tillhandahåller utökningsbar hälsorapportering för status för entiteter i ett kluster. Varje nod, program, tjänst, partition, replik eller instans har en kontinuerligt uppdateringsbar hälsostatus. Hälsostatusen kan antingen vara "OK", "Varning" eller "Fel". Tänk på Service Fabric-händelser som verb som utförs av klustret till olika entiteter och hälsotillstånd som ett adjektiv för varje entitet. Varje gång hälsotillståndet för en viss entitet övergår kommer en händelse också att avges. På så sätt kan du ställa in frågor och aviseringar för hälsohändelser i ditt övervakningsverktyg, precis som alla andra händelser. 

Dessutom låter vi även användare åsidosätta hälsotillstånd för entiteter. Om ditt program går igenom en uppgradering och du har valideringstester misslyckas, kan du skriva till Service Fabric Health med hjälp av hälso-API för att ange att ditt program inte längre är felfritt, och Service Fabric kommer automatiskt att återställa uppgraderingen! För mer information om hälsomodellen, kolla in [introduktionen till Service Fabric hälsoövervakning](service-fabric-health-introduction.md)

![SFX-hälsoinstrumentpanel](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Vakthundar
I allmänhet är en watchdog en separat tjänst som kan titta på hälsotillstånd och belastning över tjänster, pingslutpunkter och rapportera hälsotillstånd för allt i klustret. Detta kan bidra till att förhindra fel som inte skulle identifieras baserat på vyn för en enda tjänst. Watchdogs är också ett bra ställe att vara värd för kod som utför avhjälpande åtgärder utan användarinteraktion (till exempel rensa loggfiler i lagring med vissa tidsintervall). Du hittar ett exempel vakthund tjänst genomförandet [här](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Övervakning av infrastruktur (prestanda)
Nu när vi har täckt diagnostiken i ditt program och plattformen, hur vet vi att hårdvaran fungerar som förväntat? Övervakning av den underliggande infrastrukturen är en viktig del av att förstå klustrets tillstånd och resursutnyttjandet. Mätning av systemets prestanda beror på många faktorer som kan vara subjektiva beroende på dina arbetsbelastningar. Dessa faktorer mäts vanligtvis genom prestandaräknare. Dessa prestandaräknare kan komma från en mängd olika källor, inklusive operativsystemet, .NET-ramverket eller själva Service Fabric-plattformen. Vissa scenarier där de skulle vara användbara är

* Använder jag min maskinvara effektivt? Vill du använda din hårdvara på 90% CPU eller 10% CPU. Detta är praktiskt när du skalar klustret eller optimerar programmets processer.
* Kan jag förutsäga infrastrukturproblem proaktivt? - Många problem föregås av plötsliga förändringar (droppar) i prestanda, så att du kan använda prestandaräknare som nätverks-I/O och CPU-användning för att förutsäga och diagnostisera problemen proaktivt.

En lista över prestandaräknare som ska samlas in på infrastrukturnivå finns på [prestandamått](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric tillhandahåller också en uppsättning prestandaräknare för programmeringsmodellerna Reliable Services och Actors. Om du använder någon av dessa modeller kan dessa prestandaräknare information för att säkerställa att dina aktörer snurrar upp och ner korrekt, eller att dina tillförlitliga serviceförfrågningar hanteras tillräckligt snabbt. Mer information finns i [Övervakning för tillförlitlig serviceåterövervakning](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) och [prestandaövervakning för reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Azure Monitor-lösningen för att samla in dessa är Azure Monitor-loggar precis som övervakning på plattformsnivå. Du bör använda [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) för att samla in lämpliga prestandaräknare och visa dem i Azure Monitor-loggar.

## <a name="recommended-setup"></a>Rekommenderad installation
Nu när vi har gått igenom varje område av övervakning och exempel scenarier, här är en sammanfattning av Azure övervakningsverktyg och ställa in behövs för att övervaka alla områden ovan. 

* Programövervakning med [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Klusterövervakning med [diagnostikagent-](service-fabric-diagnostics-event-aggregation-wad.md) och [Azure Monitor-loggar](service-fabric-diagnostics-oms-setup.md)
* Infrastrukturövervakning med [Azure Monitor-loggar](service-fabric-diagnostics-oms-agent.md)

Du kan också använda och ändra exempelarmmallen som finns [här](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) för att automatisera distributionen av alla nödvändiga resurser och agenter. 

## <a name="other-logging-solutions"></a>Andra loggningslösningar

Även om de två lösningar vi rekommenderade, [Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md) och [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) har inbyggd integration med Service Fabric, skrivs många händelser ut via ETW-leverantörer och utbyggs med andra loggningslösningar. Du bör också titta in i [den elastiska stacken](https://www.elastic.co/products) (särskilt om du funderar på att köra ett kluster i en offlinemiljö), [Dynatrace](https://www.dynatrace.com/)eller någon annan plattform som du föredrar. Vi har en lista över integrerade partners finns [här](service-fabric-diagnostics-partners.md).

De viktigaste punkterna för alla plattformar du väljer bör vara hur bekväm du är med användargränssnittet, frågefunktionerna, de anpassade visualiseringarna och instrumentpanelerna som finns tillgängliga och de ytterligare verktyg som de tillhandahåller för att förbättra din övervakningsupplevelse. 

## <a name="next-steps"></a>Nästa steg

* För att komma igång med instrumentering av dina program, se [Händelse- och logggenerering på programnivå](service-fabric-diagnostics-event-generation-app.md).
* Gå igenom stegen för att konfigurera Application Insights för ditt program med [Övervaka och diagnostisera ett ASP.NET Core-program på Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Läs mer om övervakning av plattformen och de händelser som Service Fabric tillhandahåller dig på [plattformsnivå händelse- och logggenerering](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurera Integreringen av Azure Monitor-loggar med Service Fabric vid [konfiguration av Azure Monitor-loggar för ett kluster](service-fabric-diagnostics-oms-setup.md)
* Lär dig hur du konfigurerar Azure Monitor-loggar för övervakning av behållare – [Övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Se exempeldiagnostikproblem och lösningar med Service Fabric för [att diagnostisera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Kolla in andra diagnostikprodukter som integreras med Service Fabric i [Service Fabric diagnostiska partners](service-fabric-diagnostics-partners.md)
* Lär dig mer om allmänna övervakningsrekommendationer för Azure-resurser - [Bästa praxis - Övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 