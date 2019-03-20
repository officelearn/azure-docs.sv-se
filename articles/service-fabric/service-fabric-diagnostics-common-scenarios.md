---
title: Azure Service Fabric diagnostisera vanliga scenarier | Microsoft Docs
description: Lär dig att felsöka vanliga scenarier med Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2eb395b4f3d922aa116e01c5de080a54d81e10ff
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118654"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostisera vanliga scenarier med Service Fabric

Den här artikeln beskrivs vanliga scenarier som användare har uppstått i området för övervakning och diagnostik med Service Fabric. De scenarier som visas omfattar alla 3 nivåer av service fabric: Program, kluster och infrastruktur. Varje lösning använder Application Insights och Azure Monitor-loggar, Azure övervakningsverktyg, för att slutföra varje scenario. Stegen i varje lösning som ger användarna en introduktion om hur du använder Application Insights och Azure Monitor-loggar i samband med Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Krav och rekommendationer

Lösningar i den här artikeln använder följande verktyg. Vi rekommenderar att du har dessa igång och konfigurerade:

* [Application Insights med Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Aktivera Azure Diagnostics på ditt kluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurera en Log Analytics-arbetsyta](service-fabric-diagnostics-oms-setup.md)
* [Logga Analytics-agenten för att spåra prestandaräknare](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hur ser jag ett ohanterat undantag i mitt program?

1. Gå till Application Insights-resursen som ditt program är konfigurerad med.
2. Klicka på *Search* längst upp till vänster. Klicka på filtret på nästa panel.

    ![AI-översikt](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Många olika typer av händelser (spårningar, begäranden, anpassade händelser) visas. Välj ”undantag” som filter.

    ![AI-filterlista](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Genom att klicka på ett undantag i listan, kan du titta på mer information, inklusive tjänstkontext om du använder Service Fabric Application Insights SDK.

    ![AI-undantag](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hur gör jag för att visa för HTTP-anrop som används i Mina tjänster?

1. I samma Application Insights-resurs, kan du filtrera efter ”requests” i stället för undantag och visa alla begäranden som görs
2. Om du använder Service Fabric Application Insights SDK kan du kan se en visuell representation av dina tjänster som är anslutna till varandra och antalet lyckades och misslyckade begäranden. Till vänster klickar du på ”Programkartan”

    ![Nu visas Programkartan med AI-bladet](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI Programkarta](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Mer information om programkartan finns i [Programkartan dokumentation](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hur skapar jag en avisering när en nod stängs av

1. Noden händelser spåras med Service Fabric-klustret. Gå till Service Fabric-analys lösning resursen med namnet **ServiceFabric(NameofResourceGroup)**
2. Klicka på diagrammet längst ned på bladet med rubriken ”översikt”

    ![Azure Monitor loggar lösning](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Här har du många diagram och paneler som visar olika mått. Klicka på diagrammet och det tar dig till Loggsökningen. Här kan du fråga efter alla klusterhändelser eller prestandaräknare.
4. Ange följande fråga. Dessa händelse-ID: N finns i den [nod-händelser](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klicka på ”ny Aviseringsregel” högst upp och nu när en händelse anländer baserat på den här frågan, du får en avisering i utvecklingsmetod för kommunikation.

    ![Azure Monitor loggar ny avisering](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hur kan jag få aviseringar programmet uppgradera återställningar?

1. På samma Log Search-fönster som innan du anger du följande fråga för uppgradering återställningar. Dessa händelse-ID: N hittas [programreferens-händelser](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klicka på ”ny Aviseringsregel” högst upp och nu när en händelse anländer baserat på den här frågan, du får en avisering.

## <a name="how-do-i-see-container-metrics"></a>Hur ser jag behållarmätvärden?

I samma vy med alla diagram ser du vissa paneler för prestanda för dina behållare. Du behöver Log Analytics-agenten och [lösning för övervakning av behållare](service-fabric-diagnostics-oms-containers.md) för dessa paneler för att fylla i.

![Behållarmätvärden för log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Att instrumentera telemetri från **inuti** din behållare som du måste lägga till den [nuget-paketet för Application Insights för behållare](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Hur övervakar jag prestandaräknare

1. När du har lagt till Log Analytics-agenten till ditt kluster, måste du lägga till specifika prestandaräknare som du vill spåra. Gå till arbetsytan Log Analytics-sidan i portalen – från lösningens sidan fliken arbetsyta finns på den vänstra menyn.

    ![Fliken för log Analytics-arbetsyta](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. När du är på den arbetsytan sidan klickar du på ”Avancerade inställningar” i samma vänstra meny.

    ![Log Analytics avancerade inställningar](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klicka på Data > Windows-prestandaräknare (Data > Linux-prestandaräknare för Linux-datorer) börjar samla in-specifika räknare från noderna via Log Analytics-agenten. Här följer exempel på formatet för räknare att lägga till

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     I snabbstarten, VotingData och VotingWeb är processnamn används, så att spåra dessa räknare skulle se ut

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Prestandaräknare för log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Detta kommer att du kan se hur din infrastruktur hanterar dina arbetsbelastningar och ställa in relevanta aviseringar baserat på Resursanvändning. Till exempel – kan du ställa in en avisering om den totala processoranvändningen går över 90% eller mindre än 5%. Räknarnamnet som du använder för detta är ”% processortid”. Du kan göra detta genom att skapa en aviseringsregel för följande fråga:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hur jag för att spåra prestanda Mina Reliable Services och aktörer?

Du bör samla in Service Fabric-aktör, Aktörsmetod, Service och tjänsten metoden räknare samt för att spåra prestanda för Reliable Services eller aktörer i dina program. Här följer exempel på tillförlitlig tjänst och aktören prestandaräknare som samlar in

>[!NOTE]
>Prestandaräknare för Service Fabric kan inte samlas in av Log Analytics-agenten för närvarande, men kan samlas in av [andra diagnostiska lösningar](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Kontrollera dessa länkar för en fullständig lista över prestandaräknare på pålitliga [Services](service-fabric-reliable-serviceremoting-diagnostics.md) och [aktörer](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera aviseringar i AI](../azure-monitor/app/alerts.md) meddelas om ändringar i prestanda och användning
* [Smart identifiering i Application Insights](../azure-monitor/app/proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till AI att varna dig om potentiella prestandaproblem
* Läs mer om Azure Monitor-loggar [avisering](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik.
* För lokala kluster erbjuder Azure Monitor-loggar en gateway (HTTP framåt Proxy) som kan användas för att skicka data till Azure Monitor-loggar. Läs mer om att [ansluter datorer utan Internetåtkomst till Azure Monitor-loggar med Log Analytics-gateway](../azure-monitor/platform/gateway.md)
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Azure Monitor-loggar
* Få en mer detaljerad översikt över Azure Monitor-loggar och den erbjuder, läsa [vad är Azure Monitor-loggar?](../operations-management-suite/operations-management-suite-overview.md)
