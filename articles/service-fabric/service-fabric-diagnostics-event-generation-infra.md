---
title: Azure Service Fabric-plattformen nivå övervakning | Microsoft Docs
description: Läs mer om plattformen händelser och loggfiler som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1ba02afa775343f496a2b5fec98699e593a330ba
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-the-cluster-and-platform"></a>Övervakning av kluster och plattform

Det är viktigt att övervaka på nivån plattform för att avgöra om maskinvaran och klustret fungerar som förväntat. Även om Service Fabric kan behålla program som körs under ett maskinvarufel, men du fortfarande behöver diagnostisera om ett fel uppstår i ett program eller i den underliggande infrastrukturen. Du även övervaka dina kluster bättre planera för kapacitet, att hjälpa till att beslut om att lägga till eller ta bort maskinvara.

Service Fabric visar flera strukturerade plattform händelser som ”[Service Fabric händelser](service-fabric-diagnostics-events.md)”, loggar via EventStore och olika kanaler out-of-the-box. 

EventStore ger dig tillgång till ditt kluster händelser på grundval av per enhet (entiteter inklusive kluster, noder, program, tjänster, partitioner, repliker och behållare) och visar dem via REST API: er och klientbiblioteket Service Fabric. Använd EventStore för att övervaka kluster för utveckling och testning och för att hämta en tidpunkt i förståelse av status för din driftskluster. Läs mer om detta i [EventStore översikt](service-fabric-diagnostics-eventstore.md).

Service Fabric innehåller också följande loggen kanaler out-of-the-box för att skapa en pipeline som övervakar produktions-kluster:

* [**Använd**](service-fabric-diagnostics-event-generation-operational.md)  
Övergripande åtgärder som utförs av Service Fabric och klustret, inklusive händelser för en nod som följer, ett nytt program som distribueras eller en uppgradering återställning osv.

* **Drift - detaljerad**  
Hälsorapporter och belastningsutjämning beslut.

* **Data & meddelanden**  
Kritiska loggar och händelser som genererats i messaging (för närvarande endast ReverseProxy) och datasökväg (reliable services modeller).

* **Data & Messaging - detaljerad**  
Utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret (den här kanalen har en mycket stor volym med händelser).

Förutom dessa finns två strukturerade EventSource-kanaler som loggar som samlas in för support.

* [Reliable Services-händelser](service-fabric-reliable-services-diagnostics.md)  
Programmering modellen specifika händelser.

* [Reliable Actors-händelser](service-fabric-reliable-actors-diagnostics.md)  
Programmering modellen specifika händelser och prestandaräknare.

* Support-loggar  
Systemloggar som genereras av Service Fabric endast ska användas av oss om att ge stöd.

Dessa olika kanaler täcker de flesta av plattform nivån loggning som rekommenderas. För att förbättra plattform nivån loggning investera för att bättre förstå hälsomodell och lägga till anpassade hälsorapporter och lägga till anpassade **prestandaräknare** att skapa en realtid förståelse av hur dina tjänster och program i klustret.

För att kunna dra nytta av dessa loggar, rekommenderas att ”-diagnostik är aktiverad när klustret skapas. Genom att aktivera diagnostik, när klustret distribueras Windows Azure-diagnostik är kunna bekräfta drift, tillförlitlig tjänster och Reliable actors kanaler och lagra data som förklaras mer i [aggregera händelser med Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric-hälsa och Läs in rapportering

Service Fabric har sin egen hälsomodell som beskrivs i detalj i följande artiklar:

- [Introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md)
- [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Lägg till anpassade hälsorapporter för Service Fabric](service-fabric-report-health.md)
- [Visa Service Fabric-hälsorapporter](service-fabric-view-entities-aggregated-health.md)

Det är viktigt att flera aspekter av att driva en tjänst för övervakning av hälsotillstånd. Övervakning av hälsotillstånd är särskilt viktigt när Service Fabric utför en uppgradering av namngivna programmet. När varje uppgraderingsdomän för tjänsten har uppgraderats och är tillgänglig för dina kunder, måste uppgradera domänen klara hälsokontroller innan distributionen går vidare till nästa uppgraderingsdomän. Om bra hälsostatus inte kan uppnås, återställs distributionen, så att programmet är i ett fungerande tillstånd. Även om vissa kunder kan påverka innan tjänsterna återställs drabbas de flesta kunder inte av problemet. En lösning inträffar också relativt snabbt och utan att behöva vänta tills åtgärden från en mänsklig operatör. De mer hälsokontroller som ingår i din kod mer robust du din tjänst är att distributionsproblem.

En annan aspekt av tjänstens hälsa rapporterar mått från tjänsten. Mått är viktiga i Service Fabric eftersom de används för att balansera Resursanvändning. Mått kan också vara en indikator för systemhälsa. Till exempel du kanske har ett program som har många tjänster och varje instans i rapporterna en begäranden per andra (RPS) mått. Om en tjänst använder mer resurser än en annan tjänst, flyttar Service Fabric-tjänstinstanser runt klustret, att underhålla även resursutnyttjande. En mer detaljerad förklaring av hur resursutnyttjande fungerar, se [hantera resursförbrukning och hämta i Service Fabric med](service-fabric-cluster-resource-manager-metrics.md).

Mått också hjälper ger dig insyn i hur tjänsten utförs. Du kan använda mått med tiden för att kontrollera att tjänsten fungerar inom förväntade parametrar. Till exempel om trender visar att genomsnittliga RPS på 9: 00 på måndag morgon är 1 000, kan sedan du konfigurera en hälsorapport som varnar dig om RPS är under 500 eller större än 1 500. Allt kanske perfekt bra, men det kan vara värt att titta på att säkerställa att dina kunder har en bra upplevelse. Tjänsten kan definiera ett mått som rapporteras för health check ändamål, men som inte påverkar resurs fördelningen av klustret. Det gör du genom att ange tjänstmåttets vikt till noll. Vi rekommenderar att du börjar alla med en vikt på noll och inte att öka vikten tills du är säker på att du förstår hur viktning mätvärdena påverkar resurser för klustret.

> [!TIP]
> Använd inte för många viktat mått. Det kan vara svårt att förstå varför tjänstinstanser flyttas för belastningsutjämning. Några mått kan vara en stor!

All information som kan ange hälsotillstånd och prestanda för ditt program är en kandidat för mått och hälsorapporter. En CPU-prestandaräknare ser du hur noden används, men den tala inte du en viss tjänst är felfri, eftersom flera tjänsterna kan köras på en enda nod. Men, mätvärden som RPS bearbetade, objekt och alla svarstid för begäran kan det tyda på hälsotillståndet för en specifik tjänst.

## <a name="service-fabric-support-logs"></a>Service Fabric support-loggar

Om du behöver kontakta Microsofts support för hjälp med Azure Service Fabric-kluster krävs nästan alltid support-loggar. Om klustret finns i Azure, support loggar konfigureras automatiskt och samlas in som en del av att skapa ett kluster. Loggfilerna lagras i ett dedikerat storage-konto i din klusterresursgrupp. Lagringskontot har inte ett fast namn men kontot visas i blob-behållare och tabeller med namn som börjar med *fabric*. Information om hur du konfigurerar loggsamlingar för ett fristående kluster finns i [skapa och hantera ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md) och [konfigurationsinställningarna för ett fristående Windows-kluster](service-fabric-cluster-manifest.md). Loggfilerna ska skickas till en lokal resurs för fristående Service Fabric-instanser. Du är **krävs** har dessa loggar för support, men de är inte avsedda att användas av personer utanför Microsoft customer support-teamet.

## <a name="measuring-performance"></a>Mäta prestanda

Måttet prestanda på klustret hjälper dig att förstå hur den kan hantera belastningen och enheten beslut runt skalning klustret (Läs mer om att skala ett kluster [på Azure](service-fabric-cluster-scale-up-down.md), eller [lokalt](service-fabric-cluster-windows-server-add-remove-nodes.md)). Prestandadata är också användbart jämfört med åtgärder som du eller ditt program och tjänster har tagit, när du analyserar loggar i framtiden. 

En lista över prestandaräknare för att samla in när du använder Service Fabric finns [prestandaräknare i Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Här följer två vanliga sätt som du kan konfigurera att samla in prestandadata för klustret:

* **Med hjälp av en agent**  
Detta är det bästa sättet för att samla in prestanda från en dator eftersom agenter har vanligtvis en lista över möjliga prestandamått som kan samlas in och det är relativt enkelt att välja mått som du vill samla in eller ändra dem. Läs mer om [hur du konfigurerar OMS för Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) och [ställa in Windows-agenten OMS](../log-analytics/log-analytics-windows-agent.md) artiklar om du vill veta mer om OMS-agenten som är en sådan övervakningsagenten som går att hämta prestandadata för kluster för virtuella datorer och distribueras behållare.

* **Konfigurera diagnostik för att skriva prestandaräknare till en tabell**  
För kluster i Azure innebär detta ändringar av Azure-diagnostik-konfigurationen för att välja lämplig prestandaräknare från de virtuella datorerna i klustret och att hämta docker statistik om du planerar att distribuera en behållare. Läs om hur du konfigurerar [prestandaräknare i BOMULLSTUSS](service-fabric-diagnostics-event-aggregation-wad.md) i Service Fabric att ställa in prestandaräknarsamlingen.

## <a name="next-steps"></a>Nästa steg

Din loggar och händelser måste aggregeras innan de kan skickas till valfri analysplattform. Läs mer om [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [BOMULLSTUSS](service-fabric-diagnostics-event-aggregation-wad.md) att bättre förstå några av de rekommenderade alternativ.
