---
title: Övervaknings Azure Machine Learning | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för att visa, analysera och skapa aviseringar för mått från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399102"
---
# <a name="monitoring-azure-machine-learning"></a>Övervaknings Azure Machine Learning

I den här artikeln beskrivs övervaknings data som genereras av Azure Machine Learning. Det beskriver också hur du kan använda Azure Monitor för att analysera dina data och definiera aviseringar.

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för administratörer, som beskriver övervakningen av Azure Machine Learning. Om du är data expert eller utvecklare och vill övervaka information som är unik för din modell utbildning, se följande dokument:
>
> * [Starta, övervaka och avbryta inlärnings körningar](how-to-manage-runs.md)
> * [Logg mått för utbildnings körningar](how-to-track-experiments.md)
> * [Spåra experiment med MLflow](how-to-use-mlflow.md)
> * [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning loggar övervakningsdata med hjälp av Azure Monitor, som är en fullständig stackövervakningstjänst i Azure. Azure Monitor innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser. Den kan också övervaka resurser i andra moln och lokalt.

Börja med artikeln [Azure Monitor översikt](/azure/azure-monitor/overview), som ger en översikt över övervakningsfunktionerna. Följande avsnitt bygger på den här informationen genom att tillhandahålla information om hur du använder Azure Monitor med Azure Machine Learning.

För att förstå kostnader som är kopplade till Azure Monitor, se [användning och beräknade kostnader](/azure/azure-monitor/platform/usage-estimated-costs). För att förstå hur lång tid det tar för dina data att visas i Azure Monitor, se [logg data](/azure/azure-monitor/platform/data-ingestion-time)Inhämtnings tid.

## <a name="monitoring-data-from-azure-machine-learning"></a>Övervaknings data från Azure Machine Learning

Azure Machine Learning samlar in samma typer av övervakningsdata som andra Azure-resurser, som beskrivs i [Övervaka data från Azure-resurser](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Se [Azure Machine Learning övervaknings data referens](monitor-resource-reference.md) för en detaljerad referens för de loggar och mått som skapats av Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Analysera mått data

Du kan analysera måtten för Azure Machine Learning genom att öppna **mått** från **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) .

Alla mått för Azure Machine Learning finns i namn området **Machine Learning service Workspace**.

![Metrics Explorer med Machine Learning tjänst arbets ytan har valts](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrering och delning

För mått som stöder dimensioner kan du använda filter med hjälp av ett dimensions värde. Du kan till exempel filtrera **aktiva kärnor** för ett **kluster namn** för `cpu-cluster`. 

Du kan också dela upp ett mått per dimension för att visualisera hur olika segment i måttet jämförs med varandra. Du kan till exempel dela upp **steg typen pipeline** för att se ett antal typer av steg som används i pipelinen.

Mer information om filtrering och delning finns i [avancerade funktioner i Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Aviseringar

Du kan få åtkomst till aviseringar för Azure Machine Learning genom att öppna **aviseringar** från **Azure Monitor** -menyn. Se [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](/azure/azure-monitor/platform/alerts-metric) för information om hur du skapar aviseringar.

I följande tabell visas vanliga och rekommenderade mått för varnings regler för Azure Machine Learning:

| Aviseringstyp | Villkor | Beskrivning |
|:---|:---|:---|
| Modelldistribution misslyckades | Sammansättnings typ: Total, operator: större än, tröskelvärde: 0 | När en eller flera modell distributioner har misslyckats |
| Kvot användning i procent | Sammansättnings typ: genomsnitt, operator: större än, tröskelvärde: 90| När kvot användnings procenten är större än 90% |
| Oanvändbara noder | Sammansättnings typ: Total, operator: större än, tröskelvärde: 0 | När det finns en eller flera oanvändbara noder |

## <a name="configuration"></a>Konfiguration

> [!IMPORTANT]
> __Mått för Azure Machine Learning behöver inte konfigureras__, de samlas in automatiskt och är tillgängliga i Metrics Explorer för övervakning och avisering.

Du kan lägga till en diagnostisk inställning för att konfigurera följande funktioner:

* Arkivera logg-och mått information till ett Azure Storage-konto.
* Strömma logg-och mått information till en Azure Event Hub.
* Skicka logg-och mått information till Azure Monitor Log Analytics.

Att aktivera de här inställningarna kräver ytterligare Azure-tjänster (lagrings konto, händelsehubben eller Log Analytics), vilket kan öka din kostnad. Om du vill beräkna en uppskattad kostnad går du till [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator).

Mer information om hur du skapar en diagnostisk inställning finns i [skapa diagnostisk inställning för att samla in plattforms loggar och mått i Azure](/azure/azure-monitor/platform/diagnostic-settings).

Du kan konfigurera följande loggar för Azure Machine Learning:

| Kategori | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning beräknings kluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning beräknings kluster. |
| AmlComputeJobEvent | Händelser från jobb som körs på Azure Machine Learning Compute. |

> [!NOTE]
> När du aktiverar mått i en diagnostisk inställning ingår dimensions information för närvarande inte som en del av informationen som skickas till ett lagrings konto, en Event Hub-eller Log Analytics.

## <a name="analyzing-log-data"></a>Analysera loggdata

Om du använder Azure Monitor Log Analytics måste du skapa en diagnostisk konfiguration och aktivera __skicka information till Log Analytics__. Mer information finns i avsnittet [konfiguration](#configuration) .

Data i Azure Monitor loggar lagras i tabeller, där varje tabell har en egen uppsättning unika egenskaper. Azure Machine Learning lagrar data i följande tabeller:

| Tabell | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning beräknings kluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning beräknings kluster. |
| AmlComputeJobEvent | Händelser från jobb som körs på Azure Machine Learning Compute. |

> [!IMPORTANT]
> När du väljer **loggar** från Azure Machine Learning-menyn öppnas Log Analytics med fråge omfånget som är inställt på den aktuella arbets ytan. Det innebär att logg frågor bara innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra databaser eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

En detaljerad referens för loggar och mått finns i [Azure Machine Learning övervaknings data referens](monitor-resource-reference.md).

### <a name="sample-queries"></a>Exempelfrågor

Följande är frågor som du kan använda för att övervaka dina Azure Machine Learning-resurser: 

+ Hämta misslyckade jobb de senaste fem dagarna:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta poster för ett angivet jobb namn:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta kluster händelser under de senaste fem dagarna för kluster där storleken på den virtuella datorn är Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Hämta noder som tilldelats de senaste åtta dagarna:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Nästa steg

- En referens för loggar och mått finns i [Azure Machine Learning övervaknings data referens](monitor-resource-reference.md).
- Information om hur du arbetar med kvoter som är relaterade till Azure Machine Learning finns i [Hantera och begära kvoter för Azure-resurser](how-to-manage-quotas.md).
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
