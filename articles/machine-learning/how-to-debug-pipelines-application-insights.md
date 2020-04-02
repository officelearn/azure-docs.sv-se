---
title: Felsöka och felsöka pipelines för maskininlärning i Application Insights
titleSuffix: Azure Machine Learning
description: Lägg till loggning i dina pipelines för tränings- och batchbedömning och visa de loggade resultaten i Application Insights.
services: machine-learning
author: aburek
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 217a89f6ede4e4b1d2182eed79b088808432044f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529362"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Felsöka och felsöka pipelines för maskininlärning i Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) python-biblioteket kan användas för att dirigera loggar till Application Insights från dina skript. Genom att samla loggar från pipeline-körningar på ett ställe kan du skapa frågor och diagnostisera problem. Med hjälp av Application Insights kan du spåra loggar över tid och jämföra pipeline-loggar över körningar.

Att ha dina loggar på en gång plats kommer att ge en historik över undantag och felmeddelanden. Eftersom Application Insights integreras med Azure-aviseringar kan du också skapa aviseringar baserat på Application Insights-frågor.

## <a name="prerequisites"></a>Krav

* Följ stegen för att skapa en [Azure Machine Learning-arbetsyta](./how-to-manage-workspace.md) och [skapa din första pipeline](./how-to-create-your-first-pipeline.md)
* [Konfigurera din utvecklingsmiljö](./how-to-configure-environment.md) för att installera Azure Machine Learning SDK.
* Installera [OpenCensus Azure Monitor Exporter-paketet](https://pypi.org/project/opencensus-ext-azure/) lokalt:
  ```python
  pip install opencensus-ext-azure
  ```
* Skapa en [Application Insights-instans](../azure-monitor/app/opencensus-python.md) (det här dokumentet innehåller också information om hur du hämtar anslutningssträngen för resursen)

## <a name="getting-started"></a>Komma igång

Det här avsnittet är en introduktion som är specifik för att använda OpenCensus från en Azure Machine Learning-pipeline. En detaljerad självstudiekurs finns i [OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Lägg till ett PythonScriptStep i din Azure ML Pipeline. Konfigurera [runconfigurationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) med beroendet av opencensus-ext-azure. Konfigurera `APPLICATIONINSIGHTS_CONNECTION_STRING` miljövariabeln.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Skapa en fil med namnet `sample_step.py`. Importera klassen AzureLogHandler för att dirigera loggar till Application Insights. Du måste också importera Python-loggningsbiblioteket.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Lägg sedan till AzureLogHandler i python loggern.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Logga med anpassade dimensioner
 
Som standard har loggar som vidarebefordras till Application Insights inte tillräckligt med kontext för att spåra tillbaka till körningen eller experimentet. För att göra loggarna användbara för att diagnostisera problem behövs ytterligare fält. 

Om du vill lägga till dessa fält kan anpassade dimensioner läggas till för att ge kontext i ett loggmeddelande. Ett exempel är när någon vill visa loggar över flera steg i samma pipelinekörning.

Anpassade dimensioner utgör en ordlista med nyckelvärde (lagrad som sträng, sträng) par. Ordlistan skickas sedan till Application Insights och visas som en kolumn i frågeresultatet. Dess enskilda dimensioner kan användas som [frågeparametrar](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Användbart sammanhang som ska inkluderas

| Field                          | Resonemang/exempel                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Kan fråga loggar för dem med samma parent_run_id för att se loggar över tid för alla steg, istället för att behöva dyka in i varje enskilt steg                                        |
| step_id                        | Kan fråga loggar för dem med samma step_id för att se var ett problem uppstod med ett smalt omfång till bara det enskilda steget                                                        |
| step_name                      | Kan fråga loggar för att se stegprestanda över tid. Hjälper också till att hitta en step_id för de senaste körningarna utan att dyka in i portalgränssnittet                                          |
| experiment_name                | Kan fråga över loggar för att se experimentprestanda över tid. Hjälper också till att hitta en parent_run_id eller step_id för de senaste körningarna utan att dyka in i portalgränssnittet                   |
| run_url                 | Kan ge en länk direkt tillbaka till körningen för undersökning. |

**Andra användbara fält**

Dessa fält kan kräva ytterligare kodinstrumentering och tillhandahålls inte av körningskontexten.

| Field                   | Resonemang/exempel                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Om du använder CI/CD för att distribuera kan det här fältet korrelera loggar till den kodversion som gav steg- och pipelinelogiken. Den här länken kan ytterligare hjälpa till att diagnostisera problem eller identifiera modeller med specifika egenskaper (log/metriska värden) |
| run_type                       | Kan skilja mellan olika modelltyper, eller utbildning kontra poängkörningar |

### <a name="creating-a-custom-dimensions-dictionary"></a>Skapa en egen dimensionsordlista

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python loggning överväganden

OpenCensus AzureLogHandler används för att dirigera Python-loggar till Application Insights. Som ett resultat bör Python loggning nyanser beaktas. När en logger skapas har den en standardloggnivå och visar loggar som är större än eller lika med den nivån. En bra referens för att använda Python loggning funktioner är [Loggning Kokbok](https://docs.python.org/3/howto/logging-cookbook.html).

Miljövariabeln `APPLICATIONINSIGHTS_CONNECTION_STRING` behövs för OpenCensus-biblioteket. Vi rekommenderar att du ställer in den här miljövariabeln i stället för att skicka in den som en pipeline-parameter för att undvika att passera runt enklatextanslutningssträngar.

## <a name="querying-logs-in-application-insights"></a>Fråga loggar i Application Insights

Loggarna som dirigeras till Application Insights visas under "spårningar" eller "undantag". Var noga med att justera tidsfönstret så att det inkluderar pipelinekörningen.

![Frågeresultat för Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Resultatet i Application Insights visar loggmeddelandet och loggnivån, filsökvägen och kodradnumret. Det kommer också att visa alla anpassade dimensioner som ingår. I den här bilden visar ordlistan customDimensions nyckel-/värdepar från föregående [kodexempel](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Ytterligare användbara frågor

Vissa av frågorna nedan använder 'customDimensions.Level'. Dessa allvarlighetsgrader motsvarar den nivå som Python-loggen ursprungligen skickades med. Mer information om frågor finns i [Azure Monitor Log Queries](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

| Användningsfall                                                               | Söka i data                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Logga resultat för specifika anpassade dimensioner, till exempel "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Loggresultat för all träning har körts under de senaste 7 dagarna                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Logga resultat med allvarlighetsgradNivåfel från de senaste 7 dagarna              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Antal loggresultat med allvarlighetsgradNivåfel under de senaste 7 dagarna     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Efterföljande moment

När du har loggar i din Application Insights-instans kan de användas för att ange [Azure Monitor-aviseringar](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) baserat på frågeresultat.

Du kan också lägga till resultat från frågor till en [Azure Dashboard](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) för ytterligare insikter.