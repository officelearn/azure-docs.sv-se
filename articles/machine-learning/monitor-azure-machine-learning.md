---
title: Övervakning av Azure Machine Learning | Microsoft-dokument
description: Lär dig hur du använder Azure Monitor för att visa, analysera och skapa aviseringar om mått från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399102"
---
# <a name="monitoring-azure-machine-learning"></a>Övervaka Azure Machine Learning

I den här artikeln beskrivs övervakningsdata som genereras av Azure Machine Learning. Den beskriver också hur du kan använda Azure Monitor för att analysera dina data och definiera aviseringar.

> [!TIP]
> Informationen i det här dokumentet är främst för administratörer, eftersom den beskriver övervakning för Azure Machine Learning. Om du är datavetare eller utvecklare och vill övervaka information som är specifik för dina modellutbildningskörningar läser du följande dokument:
>
> * [Starta, övervaka och avbryta utbildningskörningar](how-to-manage-runs.md)
> * [Loggmått för träningskörningar](how-to-track-experiments.md)
> * [Spåra experiment med MLflow](how-to-use-mlflow.md)
> * [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning loggar övervakningsdata med Azure Monitor, som är en fullständig stackövervakningstjänst i Azure. Azure Monitor innehåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser. Den kan också övervaka resurser i andra moln och lokalt.

Börja med artikeln [Azure Monitor översikt](/azure/azure-monitor/overview), som ger en översikt över övervakningsfunktionerna. Följande avsnitt bygger på den här informationen genom att tillhandahålla detaljer om hur du använder Azure Monitor med Azure Machine Learning.

Information om hur du förstår kostnader som är associerade med Azure Monitor finns [i Användning och uppskattade kostnader](/azure/azure-monitor/platform/usage-estimated-costs). Information om hur mycket tid det tar för dina data att visas i Azure Monitor finns [i Logga inmatningstid](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Övervaka data från Azure Machine Learning

Azure Machine Learning samlar in samma typer av övervakningsdata som andra Azure-resurser, som beskrivs i [Övervakningsdata från Azure-resurser](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Se [Azure Machine Learning övervakningsdatareferens](monitor-resource-reference.md) för en detaljerad referens av loggar och mått som skapats av Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Analysera måttdata

Du kan analysera mått för Azure Machine Learning genom att öppna **mått** från **Azure Monitor-menyn.** Mer information om hur du använder det här verktyget finns [i Komma igång med Azure Metrics Explorer.](/azure/azure-monitor/platform/metrics-getting-started)

Alla mått för Azure Machine Learning finns i namnområdet **Machine Learning Service Workspace**.

![Statistikutforskaren med Machine Learning Service Workspace markerat](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrering och delning

För mått som stöder dimensioner kan du använda filter med hjälp av ett dimensionsvärde. Filtrering av `cpu-cluster` **aktiva kärnor** för ett **klusternamn** på . 

Du kan också dela ett mått efter dimension för att visualisera hur olika segment i måttet jämförs med varandra. Dela till exempel upp **pipelinestegstypen** för att se antalet typer av steg som används i pipelinen.

Mer information om filtrering och delning finns i [Avancerade funktioner i Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Aviseringar

Du kan komma åt aviseringar för Azure Machine Learning genom att öppna **aviseringar** från **Azure Monitor-menyn.** Se [Skapa, visa och hantera måttaviseringar med Hjälp av Azure Monitor](/azure/azure-monitor/platform/alerts-metric) för information om hur du skapar aviseringar.

I följande tabell visas vanliga och rekommenderade måttaviseringsregler för Azure Machine Learning:

| Aviseringstyp | Villkor | Beskrivning |
|:---|:---|:---|
| Det gick inte att distribuera modeller | Aggregeringstyp: Totalt, Operator: Större än, Tröskelvärde: 0 | När en eller flera modelldistributioner har misslyckats |
| Procent av kvotutnyttjande | Aggregeringstyp: Medelvärde, Operator: Större än, Tröskelvärde: 90| När kvotutnyttjandeprocenten är större än 90 % |
| Oersättliga noder | Aggregeringstyp: Totalt, Operator: Större än, Tröskelvärde: 0 | När det finns en eller flera oersättliga noder |

## <a name="configuration"></a>Konfiguration

> [!IMPORTANT]
> __Mått för Azure Machine Learning behöver inte konfigureras__, de samlas in automatiskt och är tillgängliga i Metrics Explorer för övervakning och avisering.

Du kan lägga till en diagnostikinställning för att konfigurera följande funktioner:

* Arkivera logg- och måttinformation till ett Azure-lagringskonto.
* Strömma logg- och måttinformation till en Azure Event Hub.
* Skicka logg- och måttinformation till Azure Monitor Log Analytics.

För att aktivera dessa inställningar krävs ytterligare Azure-tjänster (lagringskonto, händelsehubb eller Logganalys), vilket kan öka din kostnad. Om du vill beräkna en uppskattad kostnad besöker du [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator).

Mer information om hur du skapar en diagnostikinställning finns i [Skapa diagnostikinställning för att samla in plattformsloggar och mått i Azure](/azure/azure-monitor/platform/diagnostic-settings).

Du kan konfigurera följande loggar för Azure Machine Learning:

| Kategori | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning-beräkningskluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning-beräkningskluster. |
| AmlComputeJobEvent | Händelser från jobb som körs på Azure Machine Learning-beräkning. |

> [!NOTE]
> När du aktiverar mått i en diagnostikinställning inkluderas dimensionsinformation för närvarande inte som en del av informationen som skickas till ett lagringskonto, händelsenav eller logganalys.

## <a name="analyzing-log-data"></a>Analysera loggdata

Med Hjälp av Azure Monitor Log Analytics måste du skapa en diagnostikkonfiguration och aktivera __Skicka information till Log Analytics__. Mer information finns i avsnittet [Konfiguration.](#configuration)

Data i Azure Monitor Logs lagras i tabeller, där varje tabell har sin egen uppsättning unika egenskaper. Azure Machine Learning lagrar data i följande tabeller:

| Tabell | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning-beräkningskluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning-beräkningskluster. |
| AmlComputeJobEvent | Händelser från jobb som körs på Azure Machine Learning-beräkning. |

> [!IMPORTANT]
> När du väljer **Loggar** på Azure Machine Learning-menyn öppnas Log Analytics med frågeomfattningen inställd på den aktuella arbetsytan. Det innebär att loggfrågor endast innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra databaser eller data från andra Azure-tjänster väljer du **Loggar** på **Azure Monitor-menyn.** Mer information finns [i Loggfrågeomfattning och tidsintervall i Azure Monitor Log Analytics.](/azure/azure-monitor/log-query/scope/)

En detaljerad referens för loggar och mått finns i [Azure Machine Learning övervakningsdatareferens](monitor-resource-reference.md).

### <a name="sample-queries"></a>Exempelfrågor

Här följer frågor som du kan använda för att hjälpa dig att övervaka dina Azure Machine Learning-resurser: 

+ Få misslyckade jobb under de senaste fem dagarna:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta poster för ett visst jobbnamn:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta klusterhändelser under de senaste fem dagarna för kluster där vm-storleken är Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Få noder som tilldelats under de senaste åtta dagarna:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Nästa steg

- En referens för loggar och mått finns i [Azure Machine Learning övervakningsdatareferens](monitor-resource-reference.md).
- Information om hur du arbetar med kvoter relaterade till Azure Machine Learning finns i [Hantera och begära kvoter för Azure-resurser](how-to-manage-quotas.md).
- Mer information om hur du övervakar Azure-resurser finns i [Övervaka Azure-resurser med Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
