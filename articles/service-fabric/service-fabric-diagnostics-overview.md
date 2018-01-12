---
title: "Azure Service Fabric övervakning och diagnostik: översikt | Microsoft Docs"
description: "Läs mer om övervakning och diagnostik för Azure Service Fabric-kluster, program och tjänster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 43a45a31efffcd623e6381049876c3607663ec4f
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervaknings- och diagnostikfunktionerna för Azure Service Fabric

Den här artikeln innehåller en översikt över hur du konfigurerar övervakning och diagnostik för ditt program som körs i Service Fabric-kluster. Övervaknings- och diagnostikfunktionerna är kritiska för utveckling, testning och distribution av arbetsbelastningar i en molnmiljö. Övervakning kan du spåra hur dina program används din resursutnyttjande och den övergripande hälsan för klustret. Du kan använda den här informationen för att diagnostisera och åtgärda eventuella problem i klustret och för att förhindra att problem uppstår i framtiden. 

De huvudsakliga målen med övervakning och diagnostik är att:
* Identifiera och diagnostisera problem med infrastruktur
* Identifiera problem med ditt program
* Förstå förbrukning av nätverksresurser
* Spåra prestanda för program, tjänster och infrastruktur

I ett Service Fabric-kluster, övervakning och diagnostik data som kommer från tre nivåer: program, plattform (kluster) och infrastruktur. 
* Den [programnivå](service-fabric-diagnostics-event-generation-app.md) innehåller data om prestanda för dina program och eventuella ytterligare anpassad loggning som du har lagt till. Programmet övervakningsdata ska hamna i Application Insights (AI) från själva programmet. Det kan finnas via AI-SDK, EventFlow eller en annan pipeline önskat.
* Den [plattformsnivå](service-fabric-diagnostics-event-generation-infra.md) innehåller händelser från åtgärder på ditt kluster som rör hantering av klustret och de program som körs på den. Plattform övervakningsdata ska skickas till OMS logganalys med Service Fabric Analytics-lösningen för att visualisera inkommande händelser. Dessa data vanligtvis skickas till ett lagringskonto via tillägget Windows eller Linux Azure-diagnostik från där den används av OMS logganalys. 
* Nivån infrastruktur fokuserar på [prestandaövervakning](service-fabric-diagnostics-event-generation-perf.md), som söker på nyckelvärden och prestandaräknare för att fastställa resursanvändningen och belastning. Övervakning av programprestanda kan uppnås med hjälp av en agent - vi rekommenderar att du använder OMS (Microsoft Monitoring)-agenten så att din prestandadata avslutas med på samma plats som din plattform händelser, vilket tillåter en bättre diagnostik-upplevelse som du korrelera ändringar i ett kluster. 

![Diagram för diagnostik](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Övervakningsscenarier

Det här avsnittet beskrivs viktiga scenarier för övervakning av ett Service Fabric-kluster - program, kluster, prestanda och övervakning av hälsotillstånd. För varje scenario beskrivs avsiktshantering och övergripande tillvägagångssätt för övervakning. Mer information om dessa och andra allmänna övervakning rekommendationer för Azure-resurser finns på [metodtips - övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Dessa scenarier också löst är mappade till de tre nivåerna för övervakning och diagnostik data som beskrivs ovan, dvs. för varje scenario ska hanteras på rätt sätt i klustret, bör du ha övervakning och diagnostik data kommer in på motsvarande nivå . Hälsoövervakning scenario är ett undantag, eftersom den sträcker sig över klustret och allt körs i den.

## <a name="application-monitoring"></a>Programövervakning
Programövervakning spårar hur funktioner och komponenter i ett program som du har skapat används. Du vill övervaka dina program och se till att problem som påverkar användare fångas. Övervaka dina program kan vara användbar vid:
* fastställa programinläsning och användaren trafik - behöver du skala dina tjänster för att uppfylla kraven för användaren eller adressen potentiella flaskhalsar i ditt program?
* identifiera problem med tjänstkommunikation och fjärrkommunikation över ditt kluster
* Räkna ut vad användarna gör med ditt program – instrumentering dina program hjälper guiden funktionen för framtida utveckling och bättre diagnostik för app-fel

För övervakning på programnivå i Service Fabric, rekommenderar vi att du använder Application Insights (AI). AIS integrering med Service Fabric innehåller tooling upplevelser för Visual Studio och Azure-portalen och förstå kontext för Service Fabric-tjänster och fjärrkommunikation i AI instrumentpanelen och programavbildningen, vilket leder till en omfattande out box-loggning upplevelse. Även om många loggar skapas automatiskt och samlas in för dig när du använder AI, rekommenderar vi att du lägger till ytterligare anpassad loggning dina program och har som visas i AI tillsammans med vilken finns för att skapa en rikare upplevelse för diagnostik för att hantera problem i framtiden. Läs mer om hur du använder AI med Service Fabric på [händelseanalys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Om du vill komma igång med instrumentering koden för att övervaka dina program, gå till [programmet nivån händelse och logga generation](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Övervaka tillgänglighet
Det är möjligt att allt innehåll i klustret verkar vara körs som förväntat och rapportering som felfri, men dina program verkar vara tillgängliga eller inte kan nås. Om det finns för många fall detta händer, är det viktigt att veta när detta sker för att kunna lösa det så snart som möjligt. Tillgänglighetsövervakning är brett bekymrad spåra tillgängligheten för systemkomponenter att förstå totala ”drifttid” för systemet. Vi fokusera på tillgänglighet för ditt program i ett kluster - plattformen fungerar så att programmet livscykel hanteringsscenarier orsakar inte driftstopp. Dock olika problem i klustret kan resultera i att påverka programmets drifttid och i dessa fall som en program-ägare, vanligtvis vill du veta direkt. Vi rekommenderar att tillsammans med alla andra program, du distribuerar en watchdog i klustret. Syftet med sådana watchdog att kontrollera slutpunkter för programmet under angivet tidsintervall och rapportera att de är tillgängliga. Du kan också göra detta med hjälp av en extern tjänst som pingar slutpunkten och returnerar en rapport med angivet tidsintervall.

## <a name="cluster-monitoring"></a>Klusterövervakning av
Övervaka Service Fabric-kluster är det viktigt att säkerställa att plattformen och alla arbetsbelastningar som körs på den körs som avsett. Ett av målen för Service Fabric är att kunna köra via maskinvarufel program. Detta uppnås genom plattformens system services möjlighet att identifiera problem för infrastruktur och snabbt redundans arbetsbelastningar till andra noder i klustret. Men i det här fallet vad händer om systemtjänster själva har problem? Eller om du vid försöket att flytta en arbetsbelastning, reglerna för placering av tjänster har brutits? Övervaka klustret kan du hålla dig informerad om aktiviteter som äger rum i din kluster, vilket hjälper till att diagnostisera problem och åtgärda dem effektivt. Det finns några viktiga saker som du vill efter:
* Service Fabric fungerar som förväntat när det gäller att placera dina program och nätverksbelastning runt klustret? 
* Åtgärder som vidtas för att ändra konfigurationen för klustret som ska bekräftas och behandlas som förväntat? Detta är särskilt relevanta när skalning ett kluster.
* Service Fabric hanterar dina data och din tjänst-tjänst kommunikation inom klustret korrekt?

Service Fabric ger en omfattande uppsättning händelser direkt via drift och Data & Messaging kanaler. I Windows, dessa är i form av en enskild ETW-provider med en uppsättning relevanta `logLevelKeywordFilters` används för att välja mellan olika kanaler. På Linux för, alla plattformar händelser kommer via LTTng och placeras i en tabell från där de kan filtreras efter behov. 

Dessa kanaler innehålla granskat, strukturerade händelser som kan användas för att bättre förstå tillståndet för klustret. ”-Diagnostik är aktiverat som standard när klustret skapas, som skapar du med en tabell i Azure Storage där händelser från dessa kanaler skickas att fråga i framtiden. Du kan läsa mer om hur du övervakar klustret på [plattform nivån händelse och logga generation](service-fabric-diagnostics-event-generation-infra.md). Vi rekommenderar att du använder OMS Log Analytics för att övervaka ditt kluster. OMS logganalys erbjuder en specifik lösning Service Fabric Analytics, som ger en anpassad instrumentpanel för övervakning av Service Fabric-kluster och kan du fråga din klusterhändelser och ställa in aviseringar för Service Fabric. Läs mer om detta i [händelseanalys med OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Watchdogs
En watchdog är oftast en separat tjänst som kan titta på hälsa och läsa in över tjänster, ping slutpunkter och rapporten hälsa för någonting i klustret. Detta kan hjälpa att förhindra fel som inte skulle kunna identifieras baserat på vyn för en enskild tjänst. Watchdogs är också bra att värd-kod som utför vidtar åtgärder utan interaktion från användaren (t.ex, rensa loggfiler i lagring vid vissa intervall). Du hittar en implementering av exemplet watchdog [här](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Prestandaövervakning
Övervaka infrastrukturen underliggande är en viktig del av förstå statusen för klustret och utnyttjande av nätverksresurser. Mäter systemets prestanda beror på flera faktorer, som mäts vanligtvis via en OEM-tillverkare (Key Performance Indicator). Service Fabric relevanta KPI: er kan mappas till mått som kan samlas in från noder i klustret som prestandaräknare.
Dessa KPI: er kan hjälpa dig med:
* Förstå resursutnyttjande och Läs in – för att skala ditt kluster eller optimera tjänsten bearbetar
* förutsäga infrastruktur problem – många problem föregås av plötsliga förändringar (således) prestanda, så du kan använda KPI: er, till exempel nätverk i/o- och CPU-användning för att förutsäga och diagnostisera problem med infrastrukturella

En lista över prestandaräknare som ska samlas in på infrastrukturnivå finns på [prestandamått](service-fabric-diagnostics-event-generation-perf.md). 

Nivån prestandaövervakning av program ger Service Fabric en uppsättning prestandaräknare för programmeringsmodeller Reliable Services och aktörer. Om du använder något av dessa modeller, kan dessa prestandaräknare ge KPI: er för att säkerställa att din aktörer spinning uppåt och nedåt korrekt eller att tillförlitlig tjänst-begäranden hanteras tillräckligt snabbt. Se [övervakning för tillförlitlig tjänst fjärrkommunikation](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) och [prestandaövervakning för Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters) mer information om dessa. Utöver det har Application Insights också en uppsättning prestandamått den samlar in, om konfigurerad med ditt program.

Använda OMS-agenten för att samla in rätt prestandaräknarna och visa dessa KPI: er i OMS logganalys. Du kan också använda tillägget för Azure-diagnostik-agent (eller andra liknande agent) för att samla in och lagra mått för analys. 

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric-plattformen innehåller en hälsomodell som ger extensible hälsotillståndsrapportering för status för entiteter i ett kluster. Varje nod, program, tjänst, partition, replik eller instansen hälsostatusen kontinuerligt uppdateras. Hälsostatus kan antingen vara ”OK”, ”varning” eller ”Error”. Hälsotillståndet ändras via hälsorapporter som orsakat för varje entitet baserat på problem i klustret. Hälsostatus för entiteter kan kontrolleras när som helst i Service Fabric Explorer (SFX) som visas nedan, eller kan efterfrågas via plattformarna hälsa API. Du kan också anpassa hälsorapporter och ändra hälsostatus för en entitet genom att lägga till egna hälsorapporter eller med hjälp av hälsotillstånd API. Mer information om hälsomodell finns på [introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md).

![Instrumentpanelen för hälsotillstånd för SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Förutom att visa senaste hälsorapporter i SFX finns också varje rapport som en händelse. Hälsotillstånd händelser kan samlas in via operativa kanalen (se [händelse aggregeringen med Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)), och lagras i OMS Log Analytics för varningar och frågor i framtiden. Detta hjälper dig att identifiera problem som kan påverka din tillgänglighet, så vi rekommenderar att du konfigurerar aviseringar för lämpliga scenarier (anpassade aviseringar via OMS).

## <a name="monitoring-workflow"></a>Övervakning av arbetsflöde 

Det totala arbetsflödet för övervakning och diagnostik består av tre steg:

1. **Händelse genereras**: Detta innefattar händelser (loggar, spårningar, anpassade händelser) på nivån infrastruktur, plattform (kluster) och program
2. **Händelsen aggregering**: det här steget är effektivt pipeline för din händelser ska hamna i ett verktyg där du kan analysera dem, vilket inkluderar tillägg för Azure-diagnostik eller EventFlow
3. **Analysis**: händelser måste vara tillgänglig i ett verktyg för analys - visualisering, frågor, aviseringar och så vidare.

Flera produkter är tillgängliga som omfattar följande tre områden och du kan välja olika tekniker för varje. Det är viktigt att se till att de olika delarna arbetar tillsammans för att leverera en slutpunkt till slutpunkt övervakningslösning för klustret.

## <a name="event-generation"></a>Händelsegenerering

Det första steget i arbetsflödet för övervakning och diagnostik är att ställa in skapas och generering av händelse och loggar data. Dessa händelser, loggar och prestandaräknare släpps på alla tre nivåer: programnivå (alla instrumentation lägga till appar och tjänster som distribueras till klustret), plattform (händelser som orsakat från baserat på operationen för Service Fabric-kluster), och infrastrukturnivå (prestandamått från varje nod). Diagnostikdata som samlas in på dessa nivåer kan anpassas, även om Service Fabric aktiverar vissa standard instrumentation. 

Läs mer om [plattform händelser](service-fabric-diagnostics-event-generation-infra.md) och [nivå programhändelser](service-fabric-diagnostics-event-generation-app.md) att förstå vad som tillhandahålls och hur du lägger till ytterligare instrumentation. Huvudsakliga beslut du måste göra det här är hur du vill instrumentera ditt program. Vi rekommenderar att du använder ILogger för .NET-program, men du kan även utforska EventSource, Serilog och andra liknande bibliotek. För Java rekommenderar vi att du använder Log4j. Utöver dessa finns det många alternativ tillgängliga som kan användas baserat på typ av programmet. Passa på att utforska vad skulle vara bäst för din specifika användningsfall eller välj en som du vill använda. 

När du med ett beslut om du vill använda loggning providern, måste du kontrollera loggarna som ska aggregeras och lagras på rätt sätt.

## <a name="event-aggregation"></a>Händelsen aggregering

För att samla in loggar och händelser som genereras av dina program och klustret, normalt bör du använda den [Azure Diagnostics tillägget](service-fabric-diagnostics-event-aggregation-wad.md) (mer liknar agent-baserade Logginsamling) eller [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (pågående Logginsamling.) Om du använder Application Insights och utvecklar i .NET eller Java, rekommenderas med Application Insights SDK i stället för EventFlow.

När det är möjligt att få en liknande arbete med bara en av dessa i de flesta fall kan leder kombinera Azure Diagnostics Tilläggsagenten med en rörledning för pågående samling (AI SDK eller EventFlow) till ett mer tillförlitligt, omfattande övervakning arbetsflöde . Azure-diagnostik Tilläggsagenten kommer att din sökväg för plattformen händelser, samtidigt som du använder AI SDK eller EventFlow (pågående samling) för din programloggarna för nivån. 

Här följer några viktiga saker att tänka på om du vill använda endast en av dessa.
* Samla in programloggar med Azure-diagnostik är ett bra alternativ för Service Fabric-tjänster om uppsättning loggen källor och mål inte ändras ofta och det finns en enkel mappning mellan källorna och sina mål. Orsaken sker för detta konfigurerar Azure Diagnostics på Resource Manager-lagret, så gör betydande ändringar i konfigurationen kräver uppdateras hela klustret. Det innebär att det ofta slutar att mindre effektivt än med AI SDK eller EventFlow.
* Med hjälp av EventFlow kan du har tjänster skicka loggar med deras direkt till en analys och visualisering plattform och/eller till lagring. Andra bibliotek (ILogger, Serilog osv.) kan också användas för samma ändamål, men EventFlow har fördelen att har utformats speciellt för pågående Logginsamling och för att stödja Service Fabric-tjänster. Detta tenderar att har flera fördelar av konfiguration och distribution och ger mer flexibilitet för att stödja olika loggning bibliotek och verktyg för analys. 

## <a name="event-analysis"></a>Händelseanalys

Det finns flera bra plattformar som finns på marknaden när det gäller analys och visualisering av data för övervakning och diagnostik. De två rekommenderar vi är [OMS](service-fabric-diagnostics-event-analysis-oms.md) och [Programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md) på grund av deras integrering med Service Fabric. Du bör också titta i den [elastisk Stack](https://www.elastic.co/products) (särskilt om du använder ett kluster i en frånkopplad miljö), [Splunk](https://www.splunk.com/), eller valfri plattform för dina inställningar. 

Nyckelord för en plattform som du bör inkludera hur nöjd du är med användargränssnittet och frågar alternativ, möjligheten att visualisera data och skapa lättläst instrumentpaneler och ytterligare verktyg de tillhandahåller för att förbättra din övervakning automatiserade som aviseringar.

Förutom den plattform som du väljer när du ställer in ett Service Fabric-kluster som en Azure-resurs kan du också få åtkomst till Azures out box prestandaövervakning för dina datorer.

### <a name="azure-monitor"></a>Azure Monitor

Du kan använda [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview.md) att övervaka många av de Azure-resurser som en Service Fabric-klustret har skapats. En uppsättning mätvärden för den [virtuella datorns skaluppsättning](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) och enskilda [virtuella datorer](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) samlas in automatiskt och visas i Azure-portalen. Om du vill visa den insamlade informationen i Azure portal väljer du den resursgrupp som innehåller Service Fabric-klustret. Välj sedan virtuella datorns skaluppsättning som du vill visa. I den **övervakning** (i avsnittet vänstra navigeringsfältet), Välj **mått** att visa ett diagram av värden.

![Azure portal-vy av Insamlad information för mått](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Om du vill anpassa diagrammen, följ instruktionerna i [mått i Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Du kan också skapa varningar baserat på dessa mått, enligt beskrivningen i [skapa aviseringar i Azure-Monitor för Azure-tjänster](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du övervakar plattformen och händelserna Service Fabric ger du vid [plattform nivån händelse och logga generation](service-fabric-diagnostics-event-generation-infra.md)
* Komma igång med instrumentering dina program, finns [programmet nivån händelse och logga generation](service-fabric-diagnostics-event-generation-app.md)
* Gå igenom kursen [övervaka och diagnostisera programmet ASP.NET Core på Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)

