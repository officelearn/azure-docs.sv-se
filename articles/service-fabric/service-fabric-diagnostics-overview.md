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
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervaknings- och diagnostikfunktionerna för Azure Service Fabric

Övervaknings- och diagnostikfunktionerna är viktigt att utveckla, testa och distribuera program och tjänster i en miljö. Service Fabric-lösningar som fungerar bäst när du planerar och implementerar övervakning och diagnostik för att kontrollera program och tjänster fungerar som förväntat i en miljö för lokal utveckling eller i produktion.

De huvudsakliga målen med övervakning och diagnostik är att:
* Identifiera och diagnostisera problem med maskinvara och infrastruktur
* Identifiera problem med programvara och app, minska avbrottstiden för tjänsten
* Förstå resource förbrukning och hjälper dig att enheten operations beslut
* Optimera prestanda för program, tjänster och infrastruktur
* Generera affärsinsikter och identifiera förbättringsområden


Det totala arbetsflödet för övervakning och diagnostik består av tre steg:

1. **Händelse genereras**: Detta innefattar händelser (loggar, spårningar, anpassade händelser) i infrastrukturen (kluster), plattform och program / service nivå
2. **Händelsen aggregering**: genererade händelser behöver samlas in och sammanställs innan de kan visas
3. **Analysis**: händelser måste vara visualiserade och tillgänglig i vissa format för analys och visa efter behov

Flera produkter är tillgängliga som omfattar följande tre områden och du kan välja olika tekniker för varje. Det är viktigt att se till att de olika delarna arbetar tillsammans för att leverera en slutpunkt till slutpunkt övervakningslösning för klustret.

## <a name="event-generation"></a>Händelsegenerering

Det första steget i arbetsflödet för övervakning och diagnostik är skapas eller generering av händelser och loggar. Dessa händelser, loggar och spårningar genereras på två nivåer: plattform lagret (inklusive klustret, datorer eller Service Fabric-åtgärder) eller programnivå (alla instrumentation lägga till appar och tjänster som distribueras till klustret). Händelser på dessa nivåer kan anpassas, även om Service Fabric innehåller vissa instrumentation som standard.

Läs mer om [plattform händelser](service-fabric-diagnostics-event-generation-infra.md) och [nivå programhändelser](service-fabric-diagnostics-event-generation-app.md) att förstå vad som tillhandahålls och hur du lägger till ytterligare instrumentation.

När du med ett beslut om du vill använda loggning providern, måste du kontrollera loggarna som ska aggregeras och lagras på rätt sätt.

## <a name="event-aggregation"></a>Händelsen aggregering

För att samla in loggar och händelser som genereras av dina program och klustret, normalt bör du använda [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (mer liknar agent-baserade Logginsamling) eller [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (pågående Logginsamling).

Samla in programloggar med hjälp av Azure-diagnostik tillägget är ett bra alternativ för Service Fabric-tjänster om uppsättning loggen källor och mål inte ändras ofta och det finns en enkel mappning mellan källorna och sina mål. Orsaken sker för detta konfigurerar Azure Diagnostics på Resource Manager-lagret, så gör betydande ändringar i konfigurationen kräver uppdatering/omdistribuera klustret. Dessutom är det bäst används Kontrollera loggarna är som finns på en lite mer permanent från där de kan nås av olika plattformar för analys. Det innebär att den hamnar är mindre effektivt för en pipeline än med ett alternativ som EventFlow.

Med hjälp av [EventFlow](https://github.com/Azure/diagnostics-eventflow) kan du skicka loggar med deras direkt till en analys och visualisering plattform och/eller till storage services. Andra bibliotek (ILogger, Serilog osv.) kan användas för samma ändamål, men EventFlow har fördelen att har utformats speciellt för pågående Logginsamling och för att stödja Service Fabric-tjänster. Detta brukar ha flera potentiella fördelar:

* Enkel konfiguration och distribution
    * Konfigurationen av diagnostikdata samlingen har bara en del av konfigurationen för tjänsten. Det är lätt att alltid synkronisera den ”” med resten av programmet
    * Programspecifika eller per service configuration kan enkelt uppnås
    * Konfigurera datamål via EventFlow är bara gäller att lägga till lämpliga NuGet-paketet och ändra den *eventFlowConfig.json* fil
* Flexibilitet
    * Programmet kan skicka data oavsett var den ska, så länge det finns ett klientbiblioteket som stöder måldata lagringssystemet. Nya mål kan läggas till på önskat sätt
    * Komplexa capture, filtrering och Datasammanställning regler kan genomföras
* Åtkomst till interna programdata och kontext
    * Undersystemet diagnostik körs i processen för program/tjänst kan enkelt utöka spårningar med relevant information

Observera är att de här två alternativen är inte ömsesidigt uteslutande och när det är möjligt att få en liknande arbete med att använda en av passar dig ställa in både också kan göra. I de flesta fall kan att kombinera en agent med pågående samling leda till ett mer tillförlitlig övervakning arbetsflöde. Azure-diagnostik tillägget (agent) kan vara din valda sökväg för plattformen nivån loggar medan du kan använda EventFlow (pågående samling) för din programloggarna för nivån. När du har förstått vad som fungerar bäst för dig är det dags att tänka på hur du vill att data ska visas och analyseras.

## <a name="event-analysis"></a>Händelseanalys

Det finns flera bra plattformar som finns på marknaden när det gäller analys och visualisering av data för övervakning och diagnostik. De två rekommenderar vi är [OMS](service-fabric-diagnostics-event-analysis-oms.md) och [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) på grund av deras bättre integrering med Service Fabric, men du bör också se ut till den [elastisk Stack](https://www.elastic.co/products) (särskilt om du använder ett kluster i en frånkopplad miljö), [Splunk](https://www.splunk.com/), eller valfri plattform för dina inställningar.

Nyckelord för en plattform som du bör inkludera hur nöjd du är med användargränssnittet och frågar alternativ, möjligheten att visualisera data och skapa lättläst instrumentpaneler och ytterligare verktyg de tillhandahåller för att förbättra din övervakning automatiserade som aviseringar.

Förutom den plattform som du väljer när du ställer in ett Service Fabric-kluster som en Azure-resurs kan du också få åtkomst till Azures out box övervakning för datorer som kan vara användbar för specifika prestanda och övervakning av mått.

### <a name="azure-monitor"></a>Azure Monitor

Du kan använda [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview.md) att övervaka många av de Azure-resurser som en Service Fabric-klustret har skapats. En uppsättning mätvärden för den [virtuella datorns skaluppsättning](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) och enskilda [virtuella datorer](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) samlas in automatiskt och visas i Azure-portalen. Om du vill visa den insamlade informationen i Azure portal väljer du den resursgrupp som innehåller Service Fabric-klustret. Välj sedan virtuella datorns skaluppsättning som du vill visa. I den **övervakning** väljer **mått** att visa ett diagram av värden.

![Azure portal-vy av Insamlad information för mått](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Om du vill anpassa diagrammen, följ instruktionerna i [mått i Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Du kan också skapa varningar baserat på dessa mått, enligt beskrivningen i [skapa aviseringar i Azure-Monitor för Azure-tjänster](../monitoring-and-diagnostics/insights-alerts-portal.md). Du kan skicka aviseringar till tjänsten för meddelanden med hjälp av web hook enligt beskrivningen i [konfigurera ett webbprogram hook på en Azure mått avisering](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure-Monitor stöder bara en prenumeration. Om du behöver övervaka flera prenumerationer, eller om du behöver ytterligare funktioner [logganalys](https://azure.microsoft.com/documentation/services/log-analytics/), del av Microsoft Operations Management Suite ger en heltäckande IT-hanteringslösning både för lokala och molnbaserade infrastrukturen. Du kan vidarebefordra data från Azure-Monitor direkt till Log Analytics, så att du kan se mått och loggfiler för hela miljön i en enda plats.

## <a name="next-steps"></a>Nästa steg

### <a name="watchdogs"></a>Watchdogs

En watchdog är en separat tjänst som kan titta på hälsa och läsa in över tjänster och rapporten hälsa för något i hierarkin hälsa modellen. Detta kan hjälpa att förhindra fel som inte skulle kunna identifieras baserat på vyn för en enskild tjänst. Watchdogs är också bra att värd-kod som utför vidtar åtgärder utan interaktion från användaren (t.ex, rensa loggfiler i lagring vid vissa intervall). Du hittar en implementering av exemplet watchdog [här](https://github.com/Azure-Samples/service-fabric-watchdog-service).

Komma igång med att förstå hur händelser och loggar hämta skapas på den [plattformsnivå](service-fabric-diagnostics-event-generation-infra.md) och [programnivå](service-fabric-diagnostics-event-generation-app.md).