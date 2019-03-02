---
title: 'Azure Service Fabric övervakning och diagnostik: översikt | Microsoft Docs'
description: Läs mer om övervakning och diagnostik för Azure Service Fabric-kluster, program och tjänster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: bb4ffe959fd3b973f55b08908ea603839222365d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243270"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervakning och diagnostik för Azure Service Fabric

Den här artikeln innehåller en översikt över övervakning och diagnostik för Azure Service Fabric. Övervakning och diagnostik är kritiska för utveckling, testning och distribution av arbetsbelastningar i alla miljöer i molnet. Exempelvis kan du spåra hur dina program används kan de åtgärder som vidtas av Service Fabric-plattformen, resursanvändningen med prestandaräknare och den övergripande hälsan för klustret. Du kan använda den här informationen för att diagnostisera och åtgärda problem och förebygga att de uppstår i framtiden. Följande avsnitt förklarar kortfattat varje område av Service Fabric monitoring för att överväga för produktionsarbetsbelastningar. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Programövervakning
Programövervakning spårar hur funktioner och komponenter i ditt program används. Du vill övervaka dina program för att se till att problem som påverkar användare fångas. Ansvar för programövervakning av finns på de användare som utvecklar ett program och tjänster eftersom det är unikt för affärslogiken i programmet. Övervaka dina program kan vara användbart i följande scenarier:
* Hur mycket trafik har drabbats av mitt program? -Du behöver skala dina tjänster för att uppfylla ligger hos eller adress potentiella flaskhalsar i programmet?
* Är min tjänst till tjänst-anrop lyckas och spårade?
* Vilka åtgärder vidtas av användare av mitt program? – Samla in telemetri hjälper framtida funktionsutveckling och bättre diagnostik för programfel
* Mitt program som utlöste ett ohanterat undantag? 
* Vad som händer i de tjänster som körs i min behållare?

En stor fördel om programövervakning av är att utvecklare kan använda vilka verktyg och ramverk som de vill ha eftersom de lagras inom ramen för ditt program! Du kan läsa mer om Azure-lösning för övervakning med Azure Monitor - Application Insights i [händelseanalys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Vi har också en självstudiekurs i hur du [in detta för .NET-program](service-fabric-tutorial-monitoring-aspnet.md). Den här självstudien tar upp hur du installerar rätt verktyg ett exempel för att skriva anpassad telemetri i programmet och visa application diagnostics och telemetri i Azure-portalen. 


## <a name="platform-cluster-monitoring"></a>Övervakning av plattform (kluster)
En användare har kontroll över vilken telemetri kommer från sina program eftersom en användare skriver koden själv, men vad om diagnostiken från Service Fabric-plattformen? Ett av målen för Service Fabric är att underhålla program motståndskraftig mot maskinvarufel. Det här målet uppnås via plattformens system services möjligheten att identifiera problem med infrastruktur och snabbt redundans arbetsbelastningar till andra noder i klustret. Men i detta fall, vad händer om systemtjänster själva har problem med? Eller om du i försök att distribuera eller flytta en arbetsbelastning kan reglerna för placering av tjänster har brutits? Service Fabric tillhandahåller diagnostik för dessa och mer att se till att du informeras om aktiviteter som äger rum i klustret. Några exempelscenarier för klusterövervakning är:

Service Fabric tillhandahåller en omfattande uppsättning händelser direkt ur lådan. Dessa [Service Fabric händelser](service-fabric-diagnostics-events.md) kan nås via EventStore eller användningskanal (händelse kanal som exponeras av plattformen). 

* Service Fabric händelser kanaler - på Windows Service Fabric är tillgängliga från en enda ETW-provider med en uppsättning relevanta `logLevelKeywordFilters` används för att välja mellan drift och Data & Messaging kanaler – detta är det sättet som vi skilja ut utgående Service Fabric-händelser som ska filtreras på efter behov. På Linux, Service Fabric händelser levereras via LTTng och placeras i en lagringstabell från där de kan filtreras efter behov. Dessa kanaler innehålla granskad, strukturerade händelser som kan användas för att bättre förstå datorernas tillstånd på klustret. Diagnostik är aktiverat som standard när kluster skapas, som skapar en Azure Storage-tabell där händelser från dessa kanaler skickas att fråga i framtiden. 

* EventStore - EventStore är en funktion som erbjuds av plattformen som ger Service Fabric-plattformshändelser som är tillgängliga i Service Fabric Explorer och via REST-API. Du kan se en ögonblicksbild vy över vad som händer i klustret för varje entitet t.ex. nod, tjänst, program och utifrån händelsens tidpunkt. Du kan också läsa mer om EventStore på den [över EventStore](service-fabric-diagnostics-eventstore.md).    

![EventStore](media/service-fabric-diagnostics-overview/eventstore.png)

Diagnostik som angetts är i form av en omfattande uppsättning händelser direkt ur lådan. Dessa [Service Fabric händelser](service-fabric-diagnostics-events.md) illustrera åtgärder som utförs av plattform på olika enheter, till exempel noder, program, tjänster, partitioner osv. I det sista exemplet ovan om en nod nedåt, plattformen skulle Generera en `NodeDown` händelse och du kan bli meddelad direkt genom din övervakning verktyg. Andra vanliga exempel är `ApplicationUpgradeRollbackStarted` eller `PartitionReconfigured` under en redundansväxling. **Samma händelser är tillgängliga på både Windows och Linux-kluster.**

Händelserna skickas via standard-kanaler i både Windows och Linux och kan läsas av alla verktyg som stöder dessa. Azure Monitor-lösningen är Azure Monitor-loggar. Gärna veta mer om vår [Azure Monitor loggar integration](service-fabric-diagnostics-event-analysis-oms.md) som innehåller en anpassad operativa instrumentpanel för ditt kluster och vissa exempelfrågor som du kan skapa aviseringar. Fler begrepp för kluster-övervakning finns på [genereringen av plattform på händelse- och log](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric-plattformen innehåller hälsomodellen, vilket ger extensible hälsotillstånd rapportering för status för entiteter i ett kluster. Varje nod, program, tjänst, partition, repliken eller instansen hälsostatusen uppdateras kontinuerligt. Hälsostatus kan antingen vara ”OK”, ”varning” eller ”Error”. Tänk på Service Fabric-händelser som verb som görs av klustret till olika enheter och hälsotillstånd som ett adjektiv för varje entitet. Varje gång hälsotillståndet för en viss enhet övergår ska en händelse också genereras. Det här sättet som du kan ställa in frågor och aviseringar för hälsohändelser i din övervakning verktyg, precis som andra händelser. 

Dessutom kan vi även användare åsidosätta hälsotillstånd för entiteter. Om ditt program genomgår en uppgradering och du har verifieringstester misslyckas, kan du skriva till Service Fabric Health använder hälso-API för att ange ditt program inte längre är felfri och Service Fabric kommer automatiskt återställning uppgraderingen! Mer information om hälsomodellen ta en titt på [introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md)

![Instrumentpanelen för hälsotillstånd för SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
I allmänhet är en watchdog en separat tjänst som kan titta på hälsotillståndet och läsa in i tjänster, ping slutpunkter och rapporten hälsotillstånd för vad som helst i klustret. Detta kan förhindra fel som inte skulle kunna identifieras baserat på vyn för en enskild tjänst. Watchdogs är också ett bra ställe att värdhantera kod som utför korrigerande åtgärder utan interaktion från användaren (till exempel att rensa loggfiler i storage vid vissa intervall). Du hittar en implementering av exemplet watchdog [här](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Övervakning av infrastruktur (prestanda)
Nu när vi har gått igenom diagnostik i ditt program och -plattformen, hur vi vet maskinvaran som fungerar som förväntat? Övervaka din underliggande infrastruktur är en viktig del av förstå tillståndet för klustret och din användning av resurser. Mäta systemets prestanda beror på många faktorer som kan vara högst subjektiv beroende på dina arbetsbelastningar. De här faktorerna mäts vanligtvis via prestandaräknare. Dessa prestandaräknare kan komma från olika källor, inklusive operativsystemet, .NET framework eller själva Service Fabric-plattformen. Vissa scenarier där de skulle vara användbar är

* Jag använda min maskinvara effektivt? Vill du använda din maskinvara på 90% CPU- eller 10% CPU. Detta är praktiskt när skala ditt kluster eller optimera ditt program bearbetar.
* Kan jag förutspå infrastruktur problem proaktivt? – många problem föregås av ändringar (släpper) i prestanda, så du kan använda prestandaräknare som nätverks-i/o- och CPU-användning för att förutsäga diagnostisera problemen proaktivt.

En lista över prestandaräknare som ska samlas in på infrastrukturnivå finns på [prestandamått](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric alsp innehåller en uppsättning av prestandaräknare för Reliable Services och aktörer programmeringsmodeller. Om du använder något av dessa modeller, kan de här prestandaräknarna information för att säkerställa att din aktörer skapa uppåt och nedåt korrekt eller att tillförlitlig tjänst-begäranden hanteras tillräckligt snabbt. Mer information finns i [övervakning för fjärrstyrd tillförlitlig tjänst](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) och [prestandaövervakning för Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Azure Monitor-lösningen att samla in dessa är Azure Monitor-loggar precis som på övervakning av plattformen. Du bör använda den [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) att samla in lämpliga prestandaräknarna och visa dem i Azure Monitor-loggar.

## <a name="recommended-setup"></a>Rekommenderade inställningar
Nu när vi har gått över varje område i scenarier för övervakning och exempel, är här en översikt över Azure övervakningsverktyg och konfigurera krävs för att övervaka alla områden som ovan. 

* Programövervakning med [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Klusterövervakning med [Diagnostikagenten](service-fabric-diagnostics-event-aggregation-wad.md) och [Azure Monitor-loggar](service-fabric-diagnostics-oms-setup.md)
* Övervakning av infrastruktur med [Azure Monitor-loggar](service-fabric-diagnostics-oms-agent.md)

Du kan också använda och ändra exemplet ARM-mallen finns [här](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) att automatisera distributionen av alla nödvändiga resurser och agenter. 

## <a name="other-logging-solutions"></a>Andra lösningar för loggning

Även om de två lösningarna rekommenderar vi, [Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md) och [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) har inbyggd integrering med Service Fabric många händelser skrivs via ETW-leverantörer och börjar utökningsbar med andra lösningar för loggning. Du ska också titta på den [Elastic Stack](https://www.elastic.co/products) (särskilt om du funderar kör ett kluster i en miljö som är offline), [Dynatrace](https://www.dynatrace.com/), eller andra plattformar upp till dig. Vi har en lista med integrerade partnerleverantörer tillgängliga [här](service-fabric-diagnostics-partners.md).

De viktigaste syftena för vilken plattform du väljer bör inkludera hur nöjd du är med användargränssnittet, förfrågningar till funktionerna, anpassade visualiseringar och instrumentpaneler som är tillgängliga och de ytterligare verktyg ger för att förbättra din övervakningsupplevelse. 

## <a name="next-steps"></a>Nästa steg

* Komma igång med instrumentering av dina program, finns i [genereringen av program på händelse- och log](service-fabric-diagnostics-event-generation-app.md).
* Gå igenom stegen för att ställa in Application Insights för ditt program med [övervaka och diagnostisera ett ASP.NET Core-program i Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Läs mer om hur du övervakar plattformen och de händelser som Service Fabric tillhandahåller för [genereringen av plattform på händelse- och log](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurera Azure Monitor-loggar integrering med Service Fabric på [konfigurera Azure Monitor-loggar för ett kluster](service-fabric-diagnostics-oms-setup.md)
* Lär dig hur du ställer in Azure Monitor-loggar för övervakning av behållare – [övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Se exempel diagnostik problem och lösningar med Service Fabric i [diagnostisera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Kolla in andra diagnostik-produkter som integreras med Service Fabric i [diagnostiska Service Fabric-partner](service-fabric-diagnostics-partners.md)
* Lär dig mer om allmänna övervakning rekommendationer för Azure-resurser – [metodtips – övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 