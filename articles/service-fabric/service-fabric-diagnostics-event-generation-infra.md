---
title: Azure Service Fabric-plattformen nivå övervakning | Microsoft Docs
description: Läs mer om händelser på plattformsnivå och loggar som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cbdbedf32e8a3dad85262f287b27a03df780d95a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393069"
---
# <a name="monitoring-the-cluster"></a>Övervaka klustret

Det är viktigt att övervaka på klusternivå att avgöra om maskinvaran och klustret fungerar som förväntat. Även om Service Fabric kan hålla program som körs under ett maskinvarufel, men du fortfarande behöver diagnostisera om ett fel uppstår i ett program eller i den underliggande infrastrukturen. Även bör du övervaka dina kluster att bättre Planera kapaciteten, att hjälpa till att fatta beslut om att lägga till eller ta bort maskinvara.

Service Fabric visar flera strukturerade plattformshändelser som [Service Fabric händelser](service-fabric-diagnostics-events.md), via EventStore och olika log kanaler out-of the box. 

På Windows, Service Fabric-händelser är tillgängliga från en enda ETW-provider med en uppsättning relevanta `logLevelKeywordFilters` används för att välja mellan drift och Data & Messaging kanaler – detta är det sättet som vi skilja ut utgående Service Fabric-händelser som ska filtreras på efter behov.

* **Operativa** övergripande åtgärder som utförs av Service Fabric och klustret, inklusive händelser för en nod som dyker upp, ett nytt program som ska distribueras eller en uppgradering återställning osv. Se en fullständig lista över händelser [här](service-fabric-diagnostics-event-generation-operational.md).  

* **Operativa – detaljerad**  
Rapporter om hälsotillstånd och belastningsutjämning beslut.

Åtgärden kanalen kan nås via en mängd olika sätt, inklusive ETW/Windows-händelseloggar, den [EventStore](service-fabric-diagnostics-eventstore.md) (tillgänglig på Windows i versioner 6.2 och senare för Windows-kluster). EventStore får du tillgång till ditt kluster händelser på basis av per entitet (entiteter, inklusive kluster, noder, program, tjänster, partitioner, repliker och behållare) och visar dem via REST API: er och Service Fabric-klientbiblioteket. Använda EventStore för att övervaka dina kluster för utveckling/testning, och för att hämta en point-in-time-förståelse för tillståndet för din produktionskluster.

* **Data & meddelanden**  
Kritiska loggar och händelser som genererats i meddelanden (för närvarande endast ReverseProxy) och datasökväg (tillförlitliga tjänster modeller).

* **Data & meddelanden - detaljerad**  
Utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret (den här kanalen har ett mycket stort antal händelser).

Förutom dessa kan finns det två strukturerade EventSource kanaler, samt loggar som vi samlar in för support.

* [Reliable Services-händelser](service-fabric-reliable-services-diagnostics.md)  
Programmering modellen specifika händelser.

* [Reliable Actors-händelser](service-fabric-reliable-actors-diagnostics.md)  
Programmering modellen specifika händelser och prestandaräknare.

* Supportloggar  
Systemloggar som genereras av Service Fabric endast ska användas av oss om att tillhandahålla support.

Dessa olika kanaler täcker det mesta av plattform på loggning som rekommenderas. För att förbättra plattformen på loggning kan investera i bättre förstå hälsomodellen och lägger till anpassade hälsorapporter och lägga till anpassade **prestandaräknare** att skapa en i realtid förståelse om effekten av din tjänster och program i klustret.

För att kunna dra nytta av de här loggarna, rekommenderas att lämna ”diagnostik” aktiverat när klustret skapas i Azure Portal. Genom att aktivera diagnostik, när klustret distribueras kan Windows Azure-diagnostik kan bekräfta de drift, Reliable Services och Reliable actors-kanaler och lagra data som beskrivs ytterligare i [sammanfatta händelser med Azure Diagnostik](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabrics hälsa och load rapportering

Service Fabric har sin egen hälsomodellen som beskrivs i detalj i de här artiklarna:

- [Introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md)
- [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Lägg till anpassade hälsorapporter i Service Fabric](service-fabric-report-health.md)
- [Visa hälsorapporter i Service Fabric](service-fabric-view-entities-aggregated-health.md)

Övervakning av hälsotillstånd är viktigt att flera aspekter av driften av en tjänst, särskilt under en uppgradering av programmet. När varje uppgraderingsdomän för tjänsten har uppgraderats måste uppgraderingsdomänen klara hälsokontroller innan distributionen går vidare till nästa uppgraderingsdomän. Om OK hälsostatus inte uppnås, återställs distributionen, så att programmet finns kvar i ett känt OK tillstånd. Även om vissa kunder kan påverkas innan tjänsterna återställs, drabbas de flesta kunder inte av problemet. Dessutom inträffar en upplösning relativt snabbt utan att behöva vänta tills åtgärden från en mänsklig operatör. De mer hälsokontroller av slutpunkter som ingår i din kod mer robust du din tjänst är att distributionsproblem.

En annan aspekt av tjänstehälsa rapporterar mått från tjänsten. Mått som är viktiga i Service Fabric eftersom de avser att balansera Resursanvändning. Mått kan också vara ett tecken på filsystemets hälsa. Exempelvis kan du kanske ett program som har många tjänster och varje instans rapporterar en begäranden per sekund (RPS)-mått. Om en tjänst använder fler resurser än en annan tjänst, flyttar Service Fabric-tjänstinstanser hela klustret, att underhålla även resursutnyttjande. En mer detaljerad förklaring av hur resursanvändningen fungerar, se [hantera resursförbrukning och läsa in i Service Fabric med mått](service-fabric-cluster-resource-manager-metrics.md).

Mått också kan ge dig insyn i hur tjänsten fungerar. Du kan använda mått med tiden för att kontrollera att tjänsten fungerar inom förväntade parametrar. Till exempel om trender visar att genomsnittliga RPS kl. 9 måndag morgon är 1 000, kan sedan du konfigurera en hälsorapport som varnar dig om RPS är under 500 eller högre än 1 500. Allt kanske perfekt bra, men det kan vara värt att titta på att säkerställa att dina kunder vill ha en bra upplevelse. Din tjänst kan definiera en uppsättning mått som rapporteras för health kontrollen, men som inte påverkar resource fördelningen av klustret. Gör detta genom att ange tjänstmåttets vikt till noll. Vi rekommenderar att du börjar alla mått med en vikt på noll och inte ökar vikten tills du är säker på att du förstår hur viktning måtten påverkar resurser för klustret.

> [!TIP]
> Använd inte för många viktad mått. Det kan vara svårt att förstå varför instanser av tjänsten flyttas för belastningsutjämning. Några mått kan vara till stor hjälp!

All information som kan tyda på hälsotillstånd och prestanda för ditt program är en kandidat för mått och hälsorapporter. **En CPU-prestandaräknare vet du hur noden används, men den säger inte du en viss tjänst är felfri, eftersom flera tjänster kan köras på en enda nod.** Men, mått som RPS, artiklar som ska bearbetas, och alla svarstid för begäran kan indikerar tillståndet för en specifik tjänst.

## <a name="service-fabric-support-logs"></a>Loggar för Service Fabric-stöd

Om du behöver kontakta Microsoft-supporten om du behöver hjälp med ditt Azure Service Fabric-kluster är stöd loggarna nästan alltid krävs. Om klustret finns i Azure, support loggar konfigureras automatiskt och som samlas in som en del av ett kluster. Loggar lagras i ett dedikerat lagringskonto i resursgruppen för ditt kluster. Storage-konto inte har ett fast namn, men i kontot du se blob-behållare och tabeller med namn som inleds med *fabric*. Information om hur du konfigurerar loggsamlingar för ett fristående kluster finns i [skapa och hantera ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md) och [konfigurationsinställningarna för en fristående Windows-kluster](service-fabric-cluster-manifest.md). För fristående Service Fabric-instanser och skickas loggarna till en lokal filresurs. Du är **krävs** ha dessa loggar för support, men de är inte avsedda att användas av vem som helst utanför Microsoft customer support-teamet.

## <a name="measuring-performance"></a>Mäta prestanda

Måttet prestanda för ditt kluster hjälper dig att förstå hur det är kan hantera belastningen och drive beslut om skalning av klustret (Mer information om att skala ett kluster [på Azure](service-fabric-cluster-scale-up-down.md), eller [lokala](service-fabric-cluster-windows-server-add-remove-nodes.md)). Prestandadata är också användbart jämfört med åtgärder som du eller dina program och tjänster kan ha tagit, när du analyserar loggar i framtiden. 

En lista över prestandaräknare som samlar in när du använder Service Fabric finns i [prestandaräknare i Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Här följer två vanliga sätt som du kan konfigurera insamling av prestandadata för klustret:

* **Med hjälp av en agent**  
Detta är det bästa sättet för insamling av prestanda från en dator, eftersom agenter har vanligtvis en lista över möjliga prestandamått som kan samlas in och det är en relativt enkel process att välja de mått som du vill samla in eller ändra. Läs om Azure Monitor erbjuder Azure Monitor-loggar i Service Fabric [Azure Monitor loggar integration](service-fabric-diagnostics-event-analysis-oms.md) och [ställa in Log Analytics-agenten](../log-analytics/log-analytics-windows-agent.md) vill veta mer om Log Analytics-agenten som är en sådan övervakningsagenten som kan hämta prestandadata för virtuella datorer klustret och distribueras behållare.

* **Prestandaräknare som Azure Table Storage**  
Du kan också skicka prestandamått till samma tabellagring som händelserna. Detta kräver ändra Azure Diagnostics-konfiguration och välj lämplig prestandaräknare från de virtuella datorerna i klustret och att avläsa docker statistik om du planerar att distribuera alla behållare. Läs om hur du konfigurerar [prestandaräknare i WAD](service-fabric-diagnostics-event-aggregation-wad.md) i Service Fabric att ställa in samling med prestandaräknare.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Service Fabric [Azure Monitor loggar integration](service-fabric-diagnostics-event-analysis-oms.md) att samla in diagnostik för klustret och skapa egna förfrågningar och aviseringar
* Lär dig mer om Service Fabric i inbyggda diagnostiska upplevelse, den [EventStore](service-fabric-diagnostics-eventstore.md)
* Går igenom några [vanliga scenarier för diagnostiska](service-fabric-diagnostics-common-scenarios.md) i Service Fabric
