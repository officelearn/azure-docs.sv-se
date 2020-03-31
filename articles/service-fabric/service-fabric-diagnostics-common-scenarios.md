---
title: Azure Service Fabric diagnostisera vanliga scenarier
description: Lär dig mer om felsökning av vanliga övervaknings- och diagnostikscenarier i Azure Service Fabric-program.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906943"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostisera vanliga scenarier med Service Fabric

Den här artikeln illustrerar vanliga scenarier som användare har stött på när det gäller övervakning och diagnostik med Service Fabric. De scenarier som presenteras täcker alla tre lager av tjänstinfrastruktur: Program, Kluster och Infrastruktur. Varje lösning använder Application Insights och Azure Monitor-loggar, Azure-övervakningsverktyg, för att slutföra varje scenario. Stegen i varje lösning ger användarna en introduktion till hur du använder Application Insights och Azure Monitor-loggar i samband med Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Förutsättningar och rekommendationer

Lösningarna i den här artikeln kommer att använda följande verktyg. Vi rekommenderar att du har dessa konfigurerade och konfigurerade:

* [Programinsikter med serviceinfrastruktur](service-fabric-tutorial-monitoring-aspnet.md)
* [Aktivera Azure Diagnostics i klustret](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurera en log analytics-arbetsyta](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics-agent för att spåra prestandaräknare](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hur kan jag se ohanterade undantag i mitt program?

1. Navigera till den Application Insights-resurs som programmet är konfigurerat med.
2. Klicka på *Sök* längst upp till vänster. Klicka sedan på filtret på nästa panel.

    ![AI Översikt](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Du kommer att se många typer av händelser (spårningar, begäranden, anpassade händelser). Välj "Undantag" som filter.

    ![Lista över AI-filter](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Genom att klicka på ett undantag i listan kan du titta på mer information, inklusive tjänstens kontext om du använder SDK för tjänst fabric-program.

    ![AI-undantag](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hur visar jag vilka HTTP-anrop som används i mina tjänster?

1. I samma Application Insights-resurs kan du filtrera efter "begäranden" i stället för undantag och visa alla begäranden som gjorts
2. Om du använder SDK för Service Fabric Application Insights kan du se en visuell representation av dina tjänster som är anslutna till varandra och antalet misslyckade och misslyckade begäranden. Till vänster klickar du på "Programkarta"

    ![AI App](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Karta Blade AI App Karta](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Mer information om programkartan finns i [dokumentationen till Programkartan](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hur skapar jag en avisering när en nod går ner

1. Nodhändelser spåras av ditt Service Fabric-kluster. Navigera till lösningsresursen Service Fabric Analytics med namnet **ServiceFabric(NameofResourceGroup)**
2. Klicka på diagrammet längst ner på bladet med titeln "Sammanfattning"

    ![Azure Monitor-logglösning](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Här har du många grafer och paneler som visar olika mått. Klicka på en av diagrammen och det tar dig till loggsökning. Här kan du fråga efter klusterhändelser eller prestandaräknare.
4. Ange följande fråga. Dessa händelse-ID:er finns i [nodhändelsereferensen](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klicka på "Ny varningsregel" högst upp och nu när som helst en händelse anländer baserat på den här frågan får du en avisering i din valda kommunikationsmetod.

    ![Azure Monitor loggar ny avisering](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hur kan jag bli aviserad om återställningar för programuppgradering?

1. I samma loggsökfönster som tidigare anger du följande fråga för återställning av uppgradering. Dessa händelse-ID:er hittas under [Referens för programhändelser](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klicka på "Ny varningsregel" högst upp och nu när som helst en händelse anländer baserat på den här frågan får du en avisering.

## <a name="how-do-i-see-container-metrics"></a>Hur ser jag behållarmått?

I samma vy med alla grafer, kommer du att se några brickor för prestanda för dina behållare. Du behöver lösningen Log Analytics Agent och [Container Monitoring](service-fabric-diagnostics-oms-containers.md) för att dessa paneler ska kunna fyllas i.

![Logganalysbehållaremått](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>För att instrumentera telemetri **inifrån** din behållare måste du lägga till [Application Insights nuget-paketet för behållare](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Hur kan jag övervaka prestandaräknare?

1. När du har lagt till Log Analytics-agenten i klustret måste du lägga till de specifika prestandaräknare som du vill spåra. Navigera till log analytics-arbetsytans sida i portalen – från lösningens sida finns fliken arbetsyta på den vänstra menyn.

    ![Fliken Logganalysarbetsyta](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. När du är på arbetsytans sida klickar du på "Avancerade inställningar" i samma vänstra meny.

    ![Avancerade inställningar för Logganalys](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klicka på Data > Windows Performance Counters (Data > Linux Performance Counters for Linux-datorer) för att börja samla in specifika räknare från dina noder via Log Analytics-agenten. Här är exempel på formatet för räknare att lägga till

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     I snabbstarten är VotingData och VotingWeb de processnamn som används, så att spåra dessa räknare skulle se ut

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Logganalys perf-räknare](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. På så sätt kan du se hur infrastrukturen hanterar dina arbetsbelastningar och ställa in relevanta aviseringar baserat på resursutnyttjande. Till exempel – du kanske vill ställa in en avisering om den totala processoranvändningen går över 90% eller under 5%. Räknarnamnet som du skulle använda för detta är %processortid. Du kan göra detta genom att skapa en aviseringsregel för följande fråga:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hur spårar jag resultatet av mina tillförlitliga tjänster och aktörer?

Om du vill spåra prestanda för tillförlitliga tjänster eller aktörer i dina program bör du också samla in räknarna Service Fabric Actor, Actor Method, Service och Service Method. Här är exempel på tillförlitliga service- och aktörsprestandaräknare för att samla in

>[!NOTE]
>Prestandaräknare för Service Fabric kan inte samlas in av Log Analytics-agenten för närvarande, men kan samlas in av [andra diagnostiska lösningar](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Kontrollera dessa länkar för en fullständig lista över prestandaräknare på reliable [services](service-fabric-reliable-serviceremoting-diagnostics.md) och [aktörer](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Nästa steg

* [Slå upp aktiveringsfel för common code package](./service-fabric-diagnostics-code-package-errors.md)
* [Ställ in aviseringar i AI](../azure-monitor/app/alerts.md) som ska meddelas om ändringar i prestanda eller användning
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) utför en proaktiv analys av den telemetri som skickas till AI för att varna dig för potentiella prestandaproblem
* Läs mer om Azure Monitor-loggar [som varnar](../log-analytics/log-analytics-alerts.md) för att underlätta identifiering och diagnostik.
* För lokala kluster erbjuder Azure Monitor-loggar en gateway (HTTP Forward Proxy) som kan användas för att skicka data till Azure Monitor-loggar. Läs mer om det i [Ansluta datorer utan Internet-åtkomst till Azure Monitor-loggar med log analytics-gatewayen](../azure-monitor/platform/gateway.md)
* Bekanta dig med [loggsöknings- och frågefunktionerna](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor-loggar
* Få en mer detaljerad översikt över Azure Monitor-loggar och vad den erbjuder, läs [Vad är Azure Monitor-loggar?](../operations-management-suite/operations-management-suite-overview.md)
