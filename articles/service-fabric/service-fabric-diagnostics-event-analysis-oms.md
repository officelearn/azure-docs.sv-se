---
title: Azure Service Fabric-Händelseanalys med Log Analytics | Microsoft Docs
description: Läs mer om visualisera och analysera händelser med hjälp av Log Analytics för övervakning och diagnostik av Azure Service Fabric-kluster.
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
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 1552fbe66f7bad219d8aea8a3f208b0fe69cdf2b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716065"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Händelseanalys och visualisering med logganalys

Logganalys, även kallat OMS (Operations Management Suite) är en uppsättning tjänster som underlättar övervakning och diagnostik för program och tjänster i molnet. Den här artikeln beskrivs hur du kör frågor i logganalys få insikter och felsökning av vad som händer i klustret. Följande vanliga frågor tas upp:

* Hur felsöker hälsa händelser?
* Hur vet jag när en nod kraschar?
* Hur vet jag om min programtjänster har startas eller stoppas?

## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta

Logganalys samlar in data från hanterade resurser, inklusive en Azure-lagring eller en agent och underhåller i en central databas. Data kan sedan användas för analys, varningar och visualisering eller ytterligare exporteras. Logganalys stöder händelser, prestandadata eller andra anpassade data. Checka ut [steg för att konfigurera diagnostik-tillägget för att aggregera händelser](service-fabric-diagnostics-event-aggregation-wad.md) och [steg för att skapa en logganalys-arbetsytan att läsa från händelser i lagring](service-fabric-diagnostics-oms-setup.md) kontrollera data flödar till logganalys .

När data tas emot av logganalys Azure har flera *hanteringslösningar* som är färdigförpackade lösningar för att övervaka inkommande data, anpassas efter flera scenarier. Dessa inkluderar en *Service Fabric Analytics* lösning och en *behållare* lösning som är de två viktigaste som att diagnostik- och övervaka när du använder Service Fabric-kluster. Den här artikeln beskriver hur du använder Service Fabric Analytics-lösning som har skapats med arbetsytan.

## <a name="access-the-service-fabric-analytics-solution"></a>Service Fabric Analytics-lösning

1. Gå till resursgruppen som du skapade Service Fabric Analytics-lösning i Azure-portalen.

2. Markera resursen **ServiceFabric\<nameOfOMSWorkspace\>**.

2. Sammanfattningsvis visas paneler i form av ett diagram för var och en av de lösningar som aktiverad, inklusive en för Service Fabric. Klicka på den **Service Fabric** kurva (första bilden nedan) för att fortsätta till Service Fabric Analytics-lösning (andra bilden nedan).

    ![OMS SA lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![OMS SA lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

Bilden ovan är startsidan för Service Fabric Analytics-lösning. Det här är en ögonblicksbild vy över vad som händer i klustret. Om du har aktiverat diagnostik när klustret har skapats kan du visa händelser för 

* [Operativa kanalen](service-fabric-diagnostics-event-generation-operational.md): att åtgärder som utför Service Fabric-plattformen (insamling av systemtjänster).
* [Reliable Actors programmering modellen händelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programming modellen händelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Förutom den operativa kanalen, mer detaljerad systemhändelser kan samlas in av [uppdaterar konfigurationen av diagnostik-tillägg](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa Service Fabric händelser inklusive åtgärder på noder

1. På sidan Service Fabric Analytics klickar du på diagrammet för **Service Fabric händelser**.

    ![OMS SA lösning operativa kanalen](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new-operational.png)

2. Klicka på tabellen för att visa händelser i en lista. När du ser här alla händelserna som har samlats in. Dessa är från WADServiceFabricSystemEventsTable i Azure Storage-konto för referens, och på samma sätt tjänsterna tillförlitliga och aktörer händelser visas bredvid är från dessa respektive tabeller.
    
    ![OMS fråga operativa kanalen](media/service-fabric-diagnostics-event-analysis-oms/oms-query-operational-channel.png)

Du kan också klicka på förstoringsglaset till vänster och använda frågespråket Kusto för att hitta det du söker. Du kan exempelvis använda följande fråga för att hitta alla åtgärder som vidtas på noder i klustret. Händelse-ID används nedan finns i den [operativa kanalen händelser](service-fabric-diagnostics-event-generation-operational.md)

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Du kan fråga på flera fält, till exempel de specifika noderna (dator) systemtjänst (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visa Fabric tillförlitlig Service och aktören händelser

1. Diagram för på sidan Service Fabric Analytics **Reliable Services**.

    ![OMS SA lösning Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-reliable-services.png)

2. Klicka på tabellen för att visa händelser i en lista. Här kan du visa händelser från tillförlitliga tjänsterna. Du kan se olika händelser för när tjänsten runasync startades och slutfördes som inträffar oftast på distribution och uppgraderingar. 

    ![OMS fråga Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms-query-reliable-services.png)

Tillförlitliga aktören händelser kan visas på ett liknande sätt. Om du vill konfigurera mer detaljerade händelser för reliable actors, måste du ändra den `scheduledTransferKeywordFilter` i konfigurationen av filnamnstillägget diagnostiska (se nedan). Information om värdena för dessa finns i den [tillförlitliga aktörer händelser](service-fabric-reliable-actors-diagnostics.md#keywords)

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Frågespråket Kusto är kraftfull. En annan värdefulla fråga som du kan köra är att ta reda på vilka noder som genererar flest händelser. Frågan i skärmbilden nedan visar reliable services händelse aggregeras med viss tjänst och nod

![OMS frågan händelser per nod](media/service-fabric-diagnostics-event-analysis-oms/oms-query-events-per-node.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill aktivera infrastrukturövervakning d.v.s. prestandaräknare, gå till [att lägga till OMS-agenten](service-fabric-diagnostics-oms-agent.md). Agenten samlar in prestandaräknare och lägger till dem i en befintlig arbetsyta.
* För lokala kluster erbjuder OMS Gateway (HTTP framåt Proxy) som kan användas för att skicka data till OMS. Läs mer om att [ansluta datorer utan Internetanslutning till OMS med OMS-Gateway](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* Få en detaljerad översikt över logganalys och den erbjuder, läsa [vad är Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
