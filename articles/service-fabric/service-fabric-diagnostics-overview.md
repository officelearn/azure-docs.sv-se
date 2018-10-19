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
ms.date: 04/25/2018
ms.author: srrengar
ms.openlocfilehash: a607f560b5e74071f5ee15d03e615138f25a3aef
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406814"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervakning och diagnostik för Azure Service Fabric

Den här artikeln innehåller en översikt över övervakning och diagnostik för Azure Service Fabric. Övervakning och diagnostik är kritiska för utveckling, testning och distribution av arbetsbelastningar i alla miljöer i molnet. Övervakning kan du spåra hur dina program används, din användning av resurser och den övergripande hälsan för klustret. Du kan använda informationen för att diagnostisera och åtgärda eventuella problem och förhindra att problem inträffar i framtiden. 

## <a name="application-monitoring"></a>Programövervakning
Programövervakning spårar hur funktioner och komponenter i ditt program används. Du vill övervaka dina program för att se till att problem som påverkar användare fångas. Övervaka dina program kan vara användbart i följande scenarier:
* Fastställa programinläsning och användaren trafik – behöver du skala dina tjänster för att uppfylla användarkrav på eller åtgärda potentiella flaskhalsar i programmet?
* Identifierar problem med tjänst-kommunikation och fjärrkommunikation med i klustret
* Räkna ut vad användarna gör med ditt program – samla in telemetri i dina program kan hjälpa att utveckla nya guide framtida funktioner och bättre diagnostik för app-fel
* Övervaka vad som händer i din behållare som körs

Service Fabric stöder många alternativ för att instrumentera din programkod med rätt spår och telemetri. Vi rekommenderar att du använder Application Insights (AI). AIS integrering med Service Fabric innehåller verktyg för Visual Studio och Azure portal, samt Service Fabric-specifika mått, ger en omfattande loggning för out-of the box-upplevelse. Även om många loggar som skapas automatiskt och som samlas in för dig med AI, rekommenderar vi att du lägger till ytterligare anpassad loggning dina program att skapa en rikare upplevelse för diagnostik. Mer information om att komma igång med Application Insights med Service Fabric på [händelseanalys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Övervakning av plattform (kluster)
Övervakning av Service Fabric-klustret är viktigt att säkerställa att plattformen och alla arbetsbelastningar körs som avsett. Ett av målen för Service Fabric är att underhålla program motståndskraftig mot maskinvarufel. Det här målet uppnås via plattformens system services möjligheten att identifiera problem med infrastruktur och snabbt redundans arbetsbelastningar till andra noder i klustret. Men i detta fall, vad händer om systemtjänster själva har problem med? Eller om du i försök att flytta en arbetsbelastning kan reglerna för placering av tjänster har brutits? Övervakning av klustret kan du hålla dig informerad om aktiviteter som äger rum i ditt kluster, vilket hjälper dig att diagnostisera problem och åtgärda dem effektivt. Det finns några viktiga saker som du vill letar reda på efter:
* Service Fabric fungerar som förväntat när det gäller att placera dina program och nätverksbelastning lösning klustret? 
* Användarens åtgärder vidtas på ditt kluster bekräftas och köras på som förväntat? Detta är särskilt relevanta när skala ett kluster.
* Service Fabric hanterar dina data och din tjänst-tjänst-kommunikation inom klustret korrekt?

Service Fabric tillhandahåller en omfattande uppsättning händelser direkt ur lådan. Dessa [Service Fabric händelser](service-fabric-diagnostics-events.md) kan nås via EventStore APIs eller användningskanal (händelse kanal som exponeras av plattformen). 
* EventStore - EventStore (finns i Windows i version 6.2 och senare, Linux fortfarande håller på att från och med den här artikeln datum för senaste uppdatering), visar dessa händelser via en uppsättning API: er (komma åt via REST-slutpunkter eller via klientbiblioteket). Läs mer om EventStore på den [över EventStore](service-fabric-diagnostics-eventstore.md).
* Service Fabric händelser kanaler - på Windows Service Fabric är tillgängliga från en enda ETW-provider med en uppsättning relevanta `logLevelKeywordFilters` används för att välja mellan drift och Data & Messaging kanaler – detta är det sättet som vi skilja ut utgående Service Fabric-händelser som ska filtreras på efter behov. På Linux, Service Fabric händelser levereras via LTTng och placeras i en lagringstabell från där de kan filtreras efter behov. Dessa kanaler innehålla granskad, strukturerade händelser som kan användas för att bättre förstå datorernas tillstånd på klustret. Diagnostik är aktiverat som standard när kluster skapas, som skapar en Azure Storage-tabell där händelser från dessa kanaler skickas att fråga i framtiden. 

Vi rekommenderar att du använder EventStore för snabb analys och för att få en ögonblicksbild uppfattning av hur klustret fungerar och om händer saker som förväntat. För att samla in loggar och händelser som genereras av ditt kluster, vanligtvis bör du använda den [Azure-diagnostiktillägget](service-fabric-diagnostics-event-aggregation-wad.md). Detta kan integreras med Service Fabric-analys, Log Analytics Service Fabric specifik lösning, vilket ger en anpassad instrumentpanel för övervakning av Service Fabric-kluster och kan du fråga din klusterhändelser och ställa in aviseringar. Läs mer om detta på [händelseanalys med Log Analytics](service-fabric-diagnostics-event-analysis-oms.md). 

 Du kan läsa mer om övervakning av ditt kluster på [genereringen av plattform på händelse- och log](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Prestandaövervakning
Övervaka din underliggande infrastruktur är en viktig del av förstå tillståndet för klustret och din användning av resurser. Mäta systemets prestanda beror på flera faktorer, som mäts vanligtvis via en OEM-tillverkare (Key Performance Indicator). Service Fabric relevanta KPI: er kan mappas till mätvärden som kan samlas in från noder i klustret som prestandaräknare.
Dessa KPI: er kan hjälpa dig med:
* Förstå Resursanvändning och Läs in – för att skala ditt kluster eller optimera dina serviceprocesser.
* Förutse problem med infrastruktur – många problem föregås av ändringar (släpper) i prestanda, så du kan använda KPI: er, till exempel I/O- och nätverksanvändning för att förutsäga och diagnostisera problem med infrastrukturen.

En lista över prestandaräknare som ska samlas in på infrastrukturnivå finns på [prestandamått](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric tillhandahåller en uppsättning prestandaräknare för Reliable Services och aktörer programmeringsmodeller. Om du använder något av dessa modeller, ange de här prestandaräknarna KPI: er som bidrar till att säkerställa att din aktörer skapa uppåt och nedåt korrekt eller att tillförlitlig tjänst-begäranden hanteras tillräckligt snabbt. Mer information finns i [övervakning för fjärrstyrd tillförlitlig tjänst](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) och [prestandaövervakning för Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Utöver detta är Application Insights även har en uppsättning prestandamått som det samlar in, om konfigurerad med ditt program.

Använd den [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) att samla in lämpliga prestandaräknarna och visa dessa KPI: er i Azure Log Analytics.

![Diagram för diagnostik](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric-plattformen innehåller hälsomodellen, vilket ger extensible hälsotillstånd rapportering för status för entiteter i ett kluster. Varje nod, program, tjänst, partition, repliken eller instansen hälsostatusen uppdateras kontinuerligt. Hälsostatus kan antingen vara ”OK”, ”varning” eller ”Error”. Hälsotillståndet ändras via hälsorapporter som genereras för varje entitet baserat på problem i klustret. Hälsotillståndet för dina entiteter kan kontrolleras när som helst i Service Fabric Explorer (SFX) som visas nedan, eller kan efterfrågas via plattformarna hälso-API. Du kan också anpassa rapporter om hälsotillstånd och ändra hälsostatus för en entitet genom att lägga till egna rapporter om hälsotillstånd eller använda hälso-API. Mer information om hälsomodellen finns på [introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md).

![Instrumentpanelen för hälsotillstånd för SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Förutom att se senaste hälsorapporter i SFX finns också varje rapport som en händelse. Health-händelser som samlas in via den operativa kanalen (se [händelse aggregering med Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)), och lagras i Log Analytics för avisering och frågor i framtiden. Detta hjälper dig att identifiera problem som kan påverka dina program tillgängliga, så vi rekommenderar att du ställer in aviseringar för lämpliga felscenarion (anpassade aviseringar via Log Analytics).

## <a name="other-logging-solutions"></a>Andra lösningar för loggning

Även om de två lösningarna rekommenderar vi, [Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) och [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) har inbyggd integrering med Service Fabric många händelser skrivs via etw-leverantörer och börjar utökningsbar med andra lösningar för loggning. Du bör också titta på den [Elastic Stack](https://www.elastic.co/products) (särskilt om du funderar kör ett kluster i en miljö som är offline), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), eller någon annan plattform för dina inställningar. 

De viktigaste syftena för vilken plattform du väljer bör inkludera hur nöjd du är med användargränssnittet och frågekörning alternativ, möjlighet att visualisera data och skapa enkelt kan läsas instrumentpaneler och ytterligare verktyg som de tillhandahåller för att förbättra din övervakning till exempel automatiserade aviseringar.

## <a name="next-steps"></a>Nästa steg

* Komma igång med instrumentering av dina program, finns i [genereringen av program på händelse- och log](service-fabric-diagnostics-event-generation-app.md).
* Läs mer om hur du övervakar plattformen och de händelser som Service Fabric tillhandahåller för [genereringen av plattform på händelse- och log](service-fabric-diagnostics-event-generation-infra.md).
* Gå igenom stegen för att konfigurera AI för ditt program med [övervaka och diagnostisera ett ASP.NET Core-program i Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Lär dig hur du ställer in Azure Log Analytics för övervakning av behållare – [övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Lär dig mer om allmänna övervakning rekommendationer för Azure-resurser – [metodtips – övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
