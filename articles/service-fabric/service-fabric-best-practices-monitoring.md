---
title: Metodtips för övervakning av Azure Service Fabric
description: Metodtips och designöverväganden för övervakning av kluster och program med Hjälp av Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551824"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

[Övervakning och diagnostik](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) är avgörande för att utveckla, testa och distribuera arbetsbelastningar i alla molnmiljöer. Du kan till exempel spåra hur dina program används, de åtgärder som vidtas av Service Fabric-plattformen, resursutnyttjandet med prestandaräknare och klustrets allmänna hälsotillstånd. Du kan använda den här informationen för att diagnostisera och korrigera problem och förhindra att de inträffar i framtiden.

## <a name="application-monitoring"></a>Programövervakning

Programövervakning spårar hur funktioner och komponenter i ditt program används. Övervaka dina program för att se till att problem som påverkar användarna fångas. Programövervakning är ansvaret för dem som utvecklar programmet och dess tjänster eftersom det är unikt för affärslogiken i ditt program. Vi rekommenderar att du ställer in programövervakning med [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), Azures programövervakningsverktyg.

## <a name="cluster-monitoring"></a>Klusterövervakning

Ett av Service Fabrics mål är att göra program motståndskraftiga mot maskinvarufel. Det här målet uppnås genom plattformens systemtjänsters förmåga att identifiera infrastrukturproblem och snabbt redundansarbetsbelastningar till andra noder i klustret. Men vad händer om systemtjänsterna själva har problem? Eller om regler för placering av tjänster överträds när du försöker distribuera eller flytta en arbetsbelastning? Service Fabric tillhandahåller diagnostik för dessa och andra problem för att se till att du informeras om hur Service Fabric-plattformen interagerar med dina program, tjänster, behållare och noder.

För Windows-kluster rekommenderar vi att du konfigurerar klusterövervakning med [diagnostikagent-](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) och [Azure Monitor-loggar](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

För Linux-kluster är Azure Monitor-loggar också det rekommenderade verktyget för Azure-plattform och infrastrukturövervakning. Linux-plattform diagnostik kräver olika konfiguration som anges i [Service Fabric Linux kluster händelser i Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Infrastrukturövervakning

[Azure Monitor-loggar](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) rekommenderas för övervakning av klusternivåhändelser. När du har konfigurerat Log Analytics-agenten med arbetsytan enligt beskrivningen i föregående länk kan du samla in prestandamått som CPU-användning, .NET-prestandaräknare som cpu-användning på processnivå, prestandaräknare för service fabric, till exempel # av undantag från en tillförlitlig tjänst och behållarmått som CPU-användning.  Du måste skriva behållarloggar till stdout eller stderr så att de blir tillgängliga i Azure Monitor-loggar.

## <a name="watchdogs"></a>Vakthundar

I allmänhet är en watchdog en separat tjänst som bevakar hälsa och belastning över tjänster, pingar slutpunkter och rapporterar oväntade hälsohändelser i klustret. Detta kan bidra till att förhindra fel som kanske inte identifieras baserat endast på prestanda för en enda tjänst. Watchdogs är också ett bra ställe att vara värd för kod som utför avhjälpande åtgärder som inte kräver användarinteraktion, till exempel rensa loggfiler i lagring med vissa tidsintervall. Se ett exempel på implementering av watchdog-tjänsten i [Tjänst Fabric Linux-klusterhändelser i Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Nästa steg

* Kom igång med instrumentering av dina program: [Händelse- och logggenerering på programnivå](service-fabric-diagnostics-event-generation-app.md).
* Gå igenom stegen för att konfigurera Application Insights för ditt program med [Övervaka och diagnostisera ett ASP.NET Core-program på Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Läs mer om övervakning av plattformen och de händelser som Service Fabric tillhandahåller för dig: [Händelse- och logggenerering på plattformsnivå](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurera Integrering av Azure Monitor-loggar med Service Fabric: [Konfigurera Azure Monitor-loggar för ett kluster](service-fabric-diagnostics-oms-setup.md)
* Lär dig hur du konfigurerar Azure Monitor-loggar för övervakningsbehållare: [Övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Se exempel på diagnostikproblem och lösningar med Service Fabric: [diagnostisera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Lär dig mer om allmänna övervakningsrekommendationer för Azure-resurser: [Metodtips – Övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).