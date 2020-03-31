---
title: Övervakning av Azure Service Fabric-plattformsnivå
description: Lär dig mer om händelser och loggar på plattformsnivå som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376944"
---
# <a name="monitoring-the-cluster"></a>Övervakning av klustret

Det är viktigt att övervaka på klusternivå för att avgöra om maskinvaran och klustret beter sig som förväntat. Även om Service Fabric kan hålla program som körs under ett maskinvarufel, men du måste fortfarande diagnostisera om ett fel uppstår i ett program eller i den underliggande infrastrukturen. Du bör också övervaka dina kluster för att bättre planera för kapacitet, vilket hjälper till i beslut om att lägga till eller ta bort maskinvara.

Service Fabric exponerar flera strukturerade plattformshändelser, som [Service Fabric-händelser,](service-fabric-diagnostics-events.md)via EventStore och olika loggkanaler utanför boxen. 

På Windows är Service Fabric-händelser tillgängliga från en enda `logLevelKeywordFilters` ETW-leverantör med en uppsättning relevanta som används för att välja mellan operativa och data & meddelandekanaler - det är så vi separerar utgående Service Fabric-händelser som ska filtreras efter behov.

* **Operativa** Åtgärder på hög nivå som utförs av Service Fabric och klustret, inklusive händelser för en nod som kommer upp, ett nytt program som distribueras eller en återställning av uppgradering, etc. Se hela listan över händelser [här](service-fabric-diagnostics-event-generation-operational.md).  

* **Operativ - detaljerad**  
Hälsorapporter och beslut om belastningsutjämning.

Driftkanalen kan nås på en mängd olika sätt, inklusive ETW/Windows-händelseloggar, [EventStore](service-fabric-diagnostics-eventstore.md) (tillgängligt i Windows i version 6.2 och senare för Windows-kluster). EventStore ger dig åtkomst till klustrets händelser per entitet (entiteter inklusive kluster, noder, program, tjänster, partitioner, repliker och behållare) och exponerar dem via REST-API:er och service fabric-klientbiblioteket. Använd EventStore för att övervaka dina utvecklings-/testkluster och för att få en point-in-time-förståelse för produktionsklusternas tillstånd.

* **Data & meddelanden**  
Kritiska loggar och händelser som genereras i meddelanden (för närvarande endast ReverseProxy) och datasökväg (tillförlitliga tjänstemodeller).

* **Data & Messaging - detaljerad**  
Utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret (den här kanalen har en mycket hög mängd händelser).

Utöver dessa finns det två strukturerade EventSource-kanaler som tillhandahålls, samt loggar som vi samlar in i supportsyfte.

* [Reliable Services-händelser](service-fabric-reliable-services-diagnostics.md)  
Programmering modell specifika händelser.

* [Reliable Actors-händelser](service-fabric-reliable-actors-diagnostics.md)  
Programmering modell specifika händelser och prestandaräknare.

* Stödloggar  
Systemloggar som genereras av Service Fabric endast för att användas av oss när de tillhandahåller support.

Dessa olika kanaler täcker de flesta av de plattformsnivå loggning som rekommenderas. För att förbättra loggning på plattformsnivå bör du överväga att investera i bättre förståelse av hälsomodellen och lägga till anpassade hälsorapporter och lägga till anpassade **prestandaräknare** för att skapa en realtidsförståelse av effekterna av dina tjänster och program i klustret.

För att kunna dra nytta av dessa loggar rekommenderas det starkt att lämna "Diagnostik" aktiverat under klusterskapande i Azure Portal. Genom att aktivera diagnostik, när klustret distribueras, kan Windows Azure Diagnostics bekräfta kanalerna För drift, tillförlitliga tjänster och tillförlitliga aktörer och lagra data enligt vad som förklaras ytterligare i [Aggregerade händelser med Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Hälso- och belastningsrapportering för Azure Service Fabric

Service Fabric har sin egen hälsomodell, som beskrivs i detalj i dessa artiklar:

- [Introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md)
- [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Lägga till anpassade hälsorapporter för Service Fabric](service-fabric-report-health.md)
- [Visa hälsorapporter för Service Fabric](service-fabric-view-entities-aggregated-health.md)

Hälsoövervakning är avgörande för flera aspekter av driften av en tjänst, särskilt under en programuppgradering. När varje uppgraderingsdomän för tjänsten har uppgraderats måste uppgraderingsdomänen klara hälsokontroller innan distributionen flyttas till nästa uppgraderingsdomän. Om OK-hälsostatus inte kan uppnås återställs distributionen så att programmet förblir i ett känt OK-tillstånd. Även om vissa kunder kan påverkas innan tjänsterna återställs, kommer de flesta kunder inte att uppleva något problem. Dessutom sker en upplösning relativt snabbt utan att behöva vänta på åtgärder från en mänsklig operatör. Ju fler hälsokontroller som ingår i koden, desto mer flexibel är din tjänst för distributionsproblem.

En annan aspekt av tjänstens hälsotillstånd är att rapportera mått från tjänsten. Mått är viktiga i Service Fabric eftersom de används för att balansera resursanvändning. Mått kan också vara en indikator på systemets hälsa. Du kan till exempel ha ett program som har många tjänster och varje instans rapporterar ett RPS-mått (Requests per sekund). Om en tjänst använder fler resurser än en annan tjänst flyttar Service Fabric tjänstinstanser runt klustret för att försöka behålla även resursutnyttjande. En mer detaljerad förklaring av hur resursutnyttjande fungerar finns [i Hantera resursförbrukning och inläsning i Service Fabric med mått](service-fabric-cluster-resource-manager-metrics.md).

Mätvärden kan också ge dig insikt i hur din tjänst fungerar. Med tiden kan du använda mått för att kontrollera att tjänsten fungerar inom förväntade parametrar. Om trender till exempel visar att den genomsnittliga RPS klockan 9 på måndag morgon är 1 000, kan du ställa in en hälsorapport som varnar dig om RPS är under 500 eller över 1 500. Allt kan vara helt bra, men det kan vara värt en titt för att vara säker på att dina kunder har en bra upplevelse. Din tjänst kan definiera en uppsättning mått som kan rapporteras för hälsokontroll, men som inte påverkar resursbalanseringen av klustret. Det gör du genom att ange måttvikten till noll. Vi rekommenderar att du startar alla mått med en vikt på noll och inte ökar vikten förrän du är säker på att du förstår hur viktning av måtten påverkar resursbalansering för klustret.

> [!TIP]
> Använd inte för många viktade mått. Det kan vara svårt att förstå varför tjänstinstanser flyttas runt för balansering. Några mått kan räcka långt!

All information som kan indikera programmets hälsa och prestanda är en kandidat för mått och hälsorapporter. **En CPU-prestandaräknare kan berätta hur noden används, men det talar inte om för dig om en viss tjänst är felfri, eftersom flera tjänster kan köras på en enda nod.** Men mått som RPS, bearbetade artiklar och svarstid för begäran kan alla ange hälsotillståndet för en viss tjänst.

## <a name="service-fabric-support-logs"></a>Supportloggar för Service Fabric

Om du behöver kontakta Microsoft-supporten för att få hjälp med ditt Azure Service Fabric-kluster krävs nästan alltid supportloggar. Om klustret finns i Azure konfigureras och samlas supportloggar in automatiskt som en del av att skapa ett kluster. Loggarna lagras i ett dedikerat lagringskonto i klustrets resursgrupp. Lagringskontot har inget fast namn, men i kontot visas blob-behållare och tabeller med namn som börjar med *tyg*. Information om hur du konfigurerar loggsamlingar för ett fristående kluster finns i [Skapa och hantera ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md) och [konfigurationsinställningar för ett fristående Windows-kluster](service-fabric-cluster-manifest.md). För fristående Service Fabric-instanser ska loggarna skickas till en lokal filresurs. Du **måste** ha dessa loggar för support, men de är inte avsedda att kunna användas av någon utanför Microsofts kundtjänst.

## <a name="measuring-performance"></a>Mätning av prestanda

Måttprestanda för klustret hjälper dig att förstå hur det kan hantera belastnings- och enhetsbeslut kring skalning av klustret (se mer om skalning av ett kluster [på Azure](service-fabric-cluster-scale-up-down.md)eller [lokalt](service-fabric-cluster-windows-server-add-remove-nodes.md)). Prestandadata är också användbara jämfört med åtgärder som du eller dina program och tjänster kan ha vidtagit när du analyserar loggar i framtiden. 

En lista över prestandaräknare som ska samlas in när du använder Service Fabric finns [i Prestandaräknare i serviceinfrastruktur](service-fabric-diagnostics-event-generation-perf.md)

Här är två vanliga sätt att konfigurera insamling av prestandadata för klustret:

* **Använda en agent**  
Detta är det bästa sättet att samla in prestanda från en dator, eftersom agenter vanligtvis har en lista över möjliga prestandamått som kan samlas in, och det är en relativt enkel process att välja de mått som du vill samla in eller ändra. Läs om Azure Monitor som erbjuder Azure Monitor-loggar i Service [Fabric:s Azure Monitor-loggningsintegrering](service-fabric-diagnostics-event-analysis-oms.md) och [konfigurera Log Analytics-agenten](../log-analytics/log-analytics-windows-agent.md) för att lära dig mer om Log Analytics-agenten, som är en sådan övervakningsagent som kan hämta prestandadata för kluster-virtuella datorer och distribuerade behållare.

* **Prestandaräknare till Azure Table Storage**  
Du kan också skicka prestandamått till samma tabelllagring som händelserna. Detta kräver att du ändrar Azure Diagnostics-konfigurationen för att hämta lämpliga prestandaräknare från de virtuella datorerna i klustret och gör det möjligt att hämta dockerstatistik om du ska distribuera några behållare. Läs om hur du konfigurerar [prestandaräknare i WAD](service-fabric-diagnostics-event-aggregation-wad.md) i Service Fabric för att ställa in prestandaräknarsamling.

## <a name="next-steps"></a>Nästa steg

* Läs om Integration av [Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md) i Service Fabric för att samla in klusterdiagnostik och skapa anpassade frågor och aviseringar
* Lär dig mer om Service Fabric's inbyggda diagnostikupplevelse, [EventStore](service-fabric-diagnostics-eventstore.md)
* Gå igenom några [vanliga diagnostiska scenarier](service-fabric-diagnostics-common-scenarios.md) i Service Fabric
