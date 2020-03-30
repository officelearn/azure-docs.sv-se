---
title: Samla in & analysera resursloggar
description: Registrera och analysera resurslogghändelser för Azure Container Registry, till exempel autentisering, push-push och avbildningsdragning.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409651"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry loggar för diagnostikutvärdering och granskning

I den här artikeln beskrivs hur du samlar in loggdata för ett Azure-behållarregister med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [resursloggar](../azure-monitor/platform/platform-logs-overview.md) (tidigare kallade *diagnostikloggar)* för användardrivna händelser i registret. Samla in och använda dessa data för att uppfylla behov som:

* Granska registerautentiseringshändelser för att säkerställa säkerhet och efterlevnad 

* Ange ett fullständigt aktivitetsspår på registerartefakter som pull- och pull-händelser så att du kan diagnostisera driftproblem med registret 

Insamling av resursloggdata med Azure Monitor kan medföra ytterligare kostnader. Se [Priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Databashändelser

Följande händelser på databasnivå för bilder och andra artefakter loggas för närvarande:

* **Push-händelser**
* **Dra händelser**
* **Avtaghändelser**
* **Ta bort händelser** (inklusive databasborttagningshändelser)

Händelser på databasnivå som för närvarande inte är inloggade: Rensa händelser.

## <a name="registry-resource-logs"></a>Registerresursloggar

Resursloggar innehåller information som skickas av Azure-resurser som beskriver deras interna åtgärd. För ett Azure-behållarregister innehåller loggarna autentiserings- och databasnivåhändelser som lagras i följande tabeller. 

* **ContainerRegistryLoginEvents** - Registry autentiseringshändelser och status, inklusive inkommande identitet och IP-adress
* **ContainerRegistryRepositoryEvents** - Åtgärder som push and pull för avbildningar och andra artefakter i registerdatabaser
* **AzureMetrics** - [Container-registermått](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) som aggregerade push- och pull-antal.

För åtgärder innehåller loggdata:
  * Status för lyckad eller misslyckad
  * Start- och sluttidsstämplar

Förutom resursloggar tillhandahåller Azure en [aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md), en enda prenumerationsnivå för Azure-hanteringshändelser som att skapa eller ta bort ett behållarregister.

## <a name="enable-collection-of-resource-logs"></a>Aktivera insamling av resursloggar

Insamling av resursloggar för ett behållarregister är inte aktiverat som standard. Aktivera uttryckligen diagnostikinställningar för varje register som du vill övervaka. Alternativ för att aktivera diagnostikinställningar finns i [Skapa diagnostikinställning för att samla in plattformsloggar och mått i Azure](../azure-monitor/platform/diagnostic-settings.md).

Om du till exempel vill visa loggar och mått för ett behållarregister i nära realtid i Azure Monitor samlar du in resursloggarna på en Log Analytics-arbetsyta. Så här aktiverar du den här diagnostikinställningen med Azure-portalen:

1. Om du inte redan har en arbetsyta skapar du en arbetsyta med [Azure-portalen](../azure-monitor/learn/quick-create-workspace.md). Om du vill minimera svarstiden i datainsamlingen kontrollerar du att arbetsytan finns i **samma region** som behållarregistret.
1. Välj registret i portalen och välj **Övervakning > diagnostikinställningar > Lägg till diagnostikinställning**.
1. Ange ett namn för inställningen och välj **Skicka till Logganalys**.
1. Välj arbetsyta för registerdiagnostiskt loggar.
1. Markera de loggdata som du vill samla in och klicka på **Spara**.

Följande avbildning visar skapandet av en diagnostikinställning för ett register med hjälp av portalen.

![Aktivera diagnostikinställningar](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Samla bara in de data som du behöver, balansera kostnader och dina övervakningsbehov. Om du till exempel bara behöver granska autentiseringshändelser väljer du bara loggen **ContainerRegistryLoginEvents.** 

## <a name="view-data-in-azure-monitor"></a>Visa data i Azure Monitor

När du har aktiverat insamling av diagnostikloggar i Log Analytics kan det ta några minuter innan data visas i Azure Monitor. Om du vill visa data i portalen markerar du registret och väljer **Övervakning > loggar**. Välj en av tabellerna som innehåller data för registret. 

Kör frågor för att visa data. Flera exempelfrågor tillhandahålls eller kör egna. Följande fråga hämtar till exempel de senaste 24 timmarna data från tabellen **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Följande bild visar exempelutdata:

![Frågeloggdata](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

En självstudiekurs om hur du använder Log Analytics i Azure-portalen finns i [Komma igång med Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)eller prova miljön För Logganalys [demo](https://portal.loganalytics.io/demo). 

Mer information om loggfrågor finns [i Översikt över loggfrågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Ytterligare frågeexempel

#### <a name="100-most-recent-registry-events"></a>100 senaste registerhändelser

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Ytterligare loggdestinationer

Förutom att skicka loggarna till Log Analytics, eller som ett alternativ, är ett vanligt scenario att välja ett Azure Storage-konto som ett loggmål. Om du vill arkivera loggar i Azure Storage skapar du ett lagringskonto innan du aktiverar arkivering via diagnostikinställningarna.

Du kan också strömma diagnostiklogghändelser till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan inta miljontals händelser per sekund, som du sedan kan omvandla och lagra med hjälp av alla analysleverantörer i realtid. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder [Logganalys](../azure-monitor/log-query/get-started-portal.md) och skapar [loggfrågor](../azure-monitor/log-query/get-started-queries.md).
* Se [Översikt över Azure-plattformsloggar](../azure-monitor/platform/platform-logs-overview.md) för att lära dig mer om plattformsloggar som är tillgängliga på olika lager av Azure.

