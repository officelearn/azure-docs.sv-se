---
title: Azure Service Fabric diagnosticera vanliga scenarier | Microsoft Docs
description: Lär dig hur du felsöker vanliga scenarier med Azure Service Fabric
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
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655742"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticera vanliga scenarier med Service Fabric

Den här artikeln beskrivs vanliga scenarier som användare har uppstått i området för övervakning och diagnostik med Service Fabric. Scenarier presenteras omfattar alla 3 lager av service fabric: program, kluster och infrastruktur. Varje lösning använder Application Insights och logganalys Azure övervakningsverktyg för att slutföra varje scenario. Stegen i varje lösning ge användarna en introduktion om hur du använder Application Insights och logganalys i samband med Service Fabric.

## <a name="prerequisites-and-recommendations"></a>Krav och rekommendationer

Lösningar i den här artikeln använder följande verktyg. Vi rekommenderar att du har dessa igång och konfigurerade:

* [Application Insights med Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Aktivera Azure-diagnostik i klustret](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurera en OMS Log Analytics-arbetsyta](service-fabric-diagnostics-oms-setup.md)
* [OMS-Agent till att följa prestandaräknare](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hur kan jag se ohanterade undantag i Mina program?

1. Gå till Application Insights-resurs som programmet är konfigurerat med.
2. Klicka på *Sök* längst upp till vänster. Klicka på filtret på panelen nästa.

    ![AI-översikt](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Många typer av händelser (spår, begäranden, anpassade händelser) visas. Välj ”undantag” som filter.

    ![AI-filterlista](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Genom att klicka på ett undantag i listan, kan du visa mer information, inklusive kontexten tjänsten om du använder Service Fabric Application Insights SDK.

    ![AI-undantag](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hur visar jag för HTTP-anrop som används i Mina tjänster?

1. I samma Application Insights-resurs kan du filtrera efter ”begäranden” i stället för undantag och visa alla begäranden som görs
2. Om du använder Service Fabric Application Insights SDK, visas en bild av dina tjänster som är anslutna till varandra och antalet lyckades och misslyckade begäranden. Vänster klickar du på ”programavbildningen”

    ![AI App kartan bladet](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI App karta](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Mer information på kartan program finns det [programavbildningen dokumentation](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hur skapar jag en avisering när en nod kraschar

1. Noden händelser spåras av Service Fabric-klustret. Gå till Service Fabric Analytics lösning resursen med namnet **ServiceFabric(NameofResourceGroup)**
2. Klicka på diagrammet längst ned på bladet med rubriken ”sammanfattning”

    ![OMS-lösning](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Här har du många diagram och paneler visas olika mått. Klicka på diagrammet och det tar du att loggen sökningen. Här kan du fråga efter klustret händelser och prestandaräknare.
4. Ange följande fråga. Dessa händelse-ID hittades i den [nod händelser](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Klicka på ”ny Aviseringsregel” längst upp och nu när en händelse anländer baserat på den här frågan, du får en avisering i ditt valda kommunikationssätt.

    ![Ny OMS-avisering](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hur kan jag vara aviseringar om programmet uppgradera återställningar?

1. Ange följande fråga för uppgradering återställningar på samma loggen sökfönstret som innan. Dessa händelse-ID hittades i [händelser för program](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Klicka på ”ny Aviseringsregel” längst upp och nu när en händelse anländer baserat på den här frågan, du får en avisering.

## <a name="how-do-i-see-container-metrics"></a>Hur ser behållaren mått?

I samma vy med alla diagram visas vissa paneler för prestanda för din behållare. Du behöver OMS-agenten och [behållare övervakning](service-fabric-diagnostics-oms-containers.md) för dessa paneler att fylla i.

![OMS behållaren mått](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Att betalningsinstrument telemetri från **i** din behållare som du måste lägga till den [nuget-paket för Application Insights för behållare](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Hur övervakar prestandaräknare?

1. När du har lagt till OMS-agenten i klustret som du behöver lägga till specifika prestandaräknare som du vill spåra. Navigera till OMS-arbetsytan sida i portalen – från lösningens sidan fliken arbetsyta finns på den vänstra menyn.

    ![Fliken för OMS-arbetsyta](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. När du är på sidan på arbetsytan klickar du på ”Avancerade inställningar” i samma vänstra menyn.

    ![OMS avancerade inställningar](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klicka på Data > Windows prestandaräknare (Data > Linux prestandaräknare för Linux-datorer) att börja samla in-specifika räknare från noderna via OMS-agenten. Här följer exempel på Format för räknare att lägga till

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    I Snabbstart, VotingData och VotingWeb är processen namnen används, så att spåra dessa räknare som ser ut som

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![OMS prestandaräknarna](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Detta kan du se hur infrastrukturen hanterar dina arbetsbelastningar och Ställ in relevanta aviseringar baserat på resursutnyttjande. – Till exempel kanske du vill ange en avisering om den totala processoranvändningen går över 90% eller mindre än 5%. Räknarnamnet som du vill använda för detta är ”% processortid”. Du kan göra detta genom att skapa en aviseringsregel för följande fråga:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hur jag för att spåra prestanda för Mina Reliable Services och aktörer?

Du bör lägga till Service Fabric aktören, Aktörsmetod, Service och tjänsten metoden räknare samt för spårning av prestanda i Reliable Services eller aktörer i dina program. Du kan lägga till dessa räknare på ett liknande sätt som scenariot ovan, här är exempel på tillförlitlig tjänst och aktören prestandaräknare att lägga till i OMS

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Kontrollera de här länkarna för en fullständig lista över prestandaräknare på tillförlitlig [Services](service-fabric-reliable-serviceremoting-diagnostics.md) och [aktörer](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera aviseringar i AI](../application-insights/app-insights-alerts.md) ska meddelas om ändringar i prestanda eller användning
* [Identifiering i Application Insights för smartkort](../application-insights/app-insights-proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till AI att varna dig om potentiella problem med prestanda
* Mer information om OMS logganalys [aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik.
* För lokala kluster erbjuder OMS Gateway (HTTP framåt Proxy) som kan användas för att skicka data till OMS. Läs mer om att [ansluta datorer utan Internetanslutning till OMS med OMS-Gateway](../log-analytics/log-analytics-oms-gateway.md)
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* Få en detaljerad översikt över logganalys och den erbjuder, läsa [vad är Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
