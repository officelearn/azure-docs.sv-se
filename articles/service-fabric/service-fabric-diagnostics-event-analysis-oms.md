---
title: Azure Service Fabric-händelseanalys med Azure Monitor-loggar
description: Lär dig mer om att visualisera och analysera händelser med hjälp av Azure Monitor-loggar för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464738"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Händelseanalys och visualisering med Azure Monitor-loggar
 Azure Monitor loggar samlar in och analyserar telemetri från program och tjänster som finns i molnet och tillhandahåller analysverktyg som hjälper dig att maximera deras tillgänglighet och prestanda. I den här artikeln beskrivs hur du kör frågor i Azure Monitor-loggar för att få insikter och felsöka vad som händer i klustret. Följande vanliga frågor behandlas:

* Hur felsöker jag hälsohändelser?
* Hur vet jag när en nod går ner?
* Hur vet jag om programmets tjänster har startat eller stoppats?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Översikt över arbetsytan Log Analytics

>[!NOTE] 
>När diagnostiklagring är aktiverat som standard vid skapande av kluster måste du fortfarande konfigurera log analytics-arbetsytan så att den kan läsas från diagnostiklagringen.

Azure Monitor loggar samlar in data från hanterade resurser, inklusive en Azure-lagringstabell eller en agent, och underhåller den i en central databas. Data kan sedan användas för analys, aviseringar och visualisering, eller ytterligare export. Azure Monitor-loggar stöder händelser, prestandadata eller andra anpassade data. Läs [steg för att konfigurera diagnostiktillägget till aggregerade händelser](service-fabric-diagnostics-event-aggregation-wad.md) och [steg för att skapa en Log Analytics-arbetsyta för att läsa från händelserna i lagringen](service-fabric-diagnostics-oms-setup.md) för att se till att data flödar till Azure Monitor-loggar.

När data har tagits emot av Azure Monitor-loggar har Azure flera *övervakningslösningar* som är färdigförpackade lösningar eller operativa instrumentpaneler för att övervaka inkommande data, anpassade till flera scenarier. Dessa inkluderar en *Service Fabric Analytics-lösning* och en *containers-lösning,* som är de två mest relevanta för diagnostik och övervakning när du använder Service Fabric-kluster. I den här artikeln beskrivs hur du använder Service Fabric Analytics-lösningen, som skapas med arbetsytan.

## <a name="access-the-service-fabric-analytics-solution"></a>Få tillgång till Service Fabric Analytics-lösningen

Gå till resursgruppen där du skapade Service Fabric Analytics-lösningen i [Azure Portal.](https://portal.azure.com)

Välj **resurstjänstenFabric-namnOfOMSWorkspace\<\>**.

I `Summary`visas paneler i form av ett diagram för var och en av de lösningar som är aktiverade, inklusive en för Service Fabric. Klicka på diagrammet **Service Fabric** om du vill fortsätta till Service Fabric Analytics-lösningen.

![Service Fabric-lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Följande bild visar startsidan för Service Fabric Analytics-lösningen. Den här startsidan innehåller en ögonblicksbild av vad som händer i klustret.

![Service Fabric-lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Om du har aktiverat diagnostik när du skapar kluster kan du se händelser för 

* [Klusterhändelser för service fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Tillförlitliga aktörer programmering modell händelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programmeringsmodellhändelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Förutom Service Fabric-händelserna direkt kan mer detaljerade systemhändelser samlas in genom [att uppdatera konfigurationen av diagnostiktillägget](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa händelser för serviceinfrastruktur, inklusive åtgärder på noder

På sidan Service Fabric Analytics klickar du på diagrammet för **Service Fabric-händelser**.

![Driftkanal för serviceinfrastrukturlösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klicka på **Lista** om du vill visa händelserna i en lista. Väl här ser du alla systemhändelser som har samlats in. Som referens kommer dessa från **WADServiceFabricSystemEventsTable** i Azure Storage-kontot, och på samma sätt kommer de tillförlitliga tjänster och aktörer som visas härnäst från respektive tabeller.
    
![Operativ kanal för fråga](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternativt kan du klicka på förstoringsglaset till vänster och använda Kusto-frågespråket för att hitta det du letar efter. Om du till exempel vill hitta alla åtgärder som vidtas på noder i klustret kan du använda följande fråga. Händelse-ID:erna som används nedan finns i [referensen för operativa kanalhändelser](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Du kan fråga efter många fler fält, till exempel de specifika noderna (Dator) systemtjänsten (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visa tillförlitliga service- och aktörshändelser för serviceinfrastruktur

Klicka på diagrammet för **tillförlitliga tjänster**på sidan Service Fabric Analytics .

![Tillförlitliga tjänster för serviceinfrastruktur](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klicka på **Lista** om du vill visa händelserna i en lista. Här kan du se händelser från de pålitliga tjänsterna. Du kan se olika händelser för när tjänsten runasync startas och slutförs som vanligtvis sker på distributioner och uppgraderingar. 

![Frågesäkra tjänster](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Tillförlitliga skådespelare händelser kan ses på ett liknande sätt. Om du vill konfigurera mer detaljerade händelser `scheduledTransferKeywordFilter` för tillförlitliga aktörer måste du ändra konfigurationen för diagnostiktillägget (se nedan). Detaljer om värdena för dessa finns i referensen för [tillförlitliga aktörer händelser](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Kusto-frågespråket är kraftfullt. En annan värdefull fråga som du kan köra är att ta reda på vilka noder som genererar flest händelser. Frågan i skärmbilden nedan visar drifthändelser för Service Fabric som aggregerats med den specifika tjänsten och noden.

![Frågehändelser per nod](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill aktivera infrastrukturövervakning, dvs prestandaräknare, gå över till att lägga till [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md). Agenten samlar in prestandaräknare och lägger till dem på din befintliga arbetsyta.
* För lokala kluster erbjuder Azure Monitor-loggar en gateway (HTTP Forward Proxy) som kan användas för att skicka data till Azure Monitor-loggar. Läs mer om det i [Ansluta datorer utan Internet-åtkomst till Azure Monitor-loggar med log analytics-gatewayen](../azure-monitor/platform/gateway.md).
* Konfigurera [automatisk avisering](../log-analytics/log-analytics-alerts.md) för att underlätta identifiering och diagnostik.
* Bekanta dig med [loggsöknings- och frågefunktionerna](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor-loggar.
* Få en mer detaljerad översikt över Azure Monitor-loggar och vad den erbjuder, läs [Vad är Azure Monitor-loggar?](../operations-management-suite/operations-management-suite-overview.md).
