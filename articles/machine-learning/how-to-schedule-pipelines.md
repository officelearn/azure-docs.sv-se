---
title: Schemalägg Azure Machine Learning pipelines
titleSuffix: Azure Machine Learning
description: Schemalägg Azure Machine Learning pipelines med hjälp av Azure Machine Learning SDK för python. Med schemalagda pipelines kan du automatisera rutinmässiga, tids krävande uppgifter, till exempel data bearbetning, utbildning och övervakning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 97d0f822e63bb6eb32b1cd2f211621af8ad1c4b8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313989"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Schemalägg maskin inlärnings pipeliner med Azure Machine Learning SDK för python

I den här artikeln får du lära dig hur du program mässigt schemalägger en pipeline för att köras på Azure. Du kan välja att skapa ett schema baserat på förfluten tid eller ändringar i fil systemet. Tidsbaserade scheman kan användas för att ta hand om rutin uppgifter, till exempel övervakning av data drift. Ändrings scheman kan användas för att reagera på oregelbundna eller oförutsägbara ändringar, till exempel nya data som laddas upp eller gamla data redige ras. När du har lärt dig hur du skapar scheman får du lära dig hur du hämtar och inaktiverar dem.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).

* En python-miljö där Azure Machine Learning SDK för python är installerat. Mer information finns i [skapa och hantera återanvändbara miljöer för utbildning och distribution med Azure Machine Learning.](how-to-use-environments.md)

* En Machine Learning arbets yta med en publicerad pipeline. Du kan använda en inbyggd pipeline för att [skapa och köra Machine Learning med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Initiera arbets ytan & hämta data

Om du vill schemalägga en pipeline behöver du en referens till din arbets yta, identifieraren för den publicerade pipelinen och namnet på det experiment som du vill skapa schemat i. Du kan hämta dessa värden med följande kod:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Skapa ett schema

Om du vill köra en pipeline regelbundet skapar du ett schema. En `Schedule` kopplar en pipeline, ett experiment och en utlösare. Utlösaren kan antingen vara en `ScheduleRecurrence` som beskriver vänte tiden mellan körningar eller en lagrings Sök väg som anger en katalog som ska bevaka ändringar. I båda fallen behöver du pipeline-identifieraren och namnet på experimentet som schemat ska skapas i.

Importera och klasser överst i python-filen `Schedule` `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Skapa ett tidsbaserat schema

`ScheduleRecurrence`Konstruktorn har ett obligatoriskt `frequency` argument som måste vara en av följande strängar: "minut", "Hour", "Day", "Week" eller "Month". Det kräver också ett heltals `interval` argument som anger hur många av `frequency` enheterna som ska förflyta mellan schema startar. Valfria argument ger dig mer information om start tider, enligt beskrivningen i [SCHEDULERECURRENCE SDK-dokument](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py).

Skapa en `Schedule` som börjar köras var 15: e minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Skapa ett ändrings baserat schema

Pipelines som utlöses av fil ändringar kan vara mer effektiva än tidsbaserade scheman. Du kanske till exempel vill utföra ett förbehandlings steg när en fil ändras eller när en ny fil läggs till i en data katalog. Du kan övervaka ändringar i ett data lager eller ändringar i en angiven katalog i data lagret. Om du övervakar en angiven katalog kommer ändringar i under kataloger i den katalogen _inte_ att utlösa någon körning.

Om du vill skapa en fil som `Schedule` är aktive rad måste du ange `datastore` parametern i anropet till [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Om du vill övervaka en mapp anger du `path_on_datastore` argumentet.

Med `polling_interval` argumentet kan du ange, i minuter, den frekvens som data lagret är markerat för ändringar.

Om pipelinen har konstruerats med en [Datapath](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) - [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py)kan du ange variabeln till namnet på den ändrade filen genom att ange `data_path_parameter_name` argumentet.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Valfria argument när du skapar ett schema

Förutom argumenten som beskrivits tidigare kan du ange `status` argumentet till `"Disabled"` för att skapa ett inaktivt schema. Slutligen kan `continue_on_step_failure` du skicka ett booleskt värde som åsidosätter förloppets standard funktions sätt.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Använda Azure Logic Apps för mer komplexa arbets flöden

Azure Logic Apps stöder mer komplexa arbets flöden och är i stort sett integrerade än Azure Machine Learning pipelines. Mer information finns i [utlösa en körning av en Machine Learning pipeline från en Logic app](how-to-trigger-published-pipeline.md) .

## <a name="view-your-scheduled-pipelines"></a>Visa dina schemalagda pipelines

Navigera till Azure Machine Learning i webbläsaren. I avsnittet **slut punkter** i navigerings fönstret väljer du pipeline- **slutpunkter**. Då går du till en lista över de pipeliner som publicerats i arbets ytan.

![Sidan pipeliner i AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

På den här sidan kan du se översikts information om alla pipeliner på arbets ytan: namn, beskrivningar, status och så vidare. Gå in genom att klicka i din pipeline. På den resulterande sidan finns det mer information om din pipeline och du kan öka detalj nivån i enskilda körningar.

## <a name="deactivate-the-pipeline"></a>Inaktivera pipelinen

Om du har en `Pipeline` som har publicerats, men inte schemalagt, kan du inaktivera den med:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Om pipelinen är schemalagd måste du först avbryta schemat. Hämta schemats identifierare från portalen eller genom att köra:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

När du har angett `schedule_id` att du vill inaktivera kör du:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Om du sedan kör `Schedule.list(ws)` igen bör du hämta en tom lista.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde du Azure Machine Learning SDK för python för att schemalägga en pipeline på två olika sätt. Ett schema återkommer baserat på förfluten Klock tid. Det andra schemat körs om en fil ändras på en angiven `Datastore` eller i en katalog i arkivet. Du såg hur du använder portalen för att undersöka pipelinen och de enskilda körningarna. Slutligen har du lärt dig hur du inaktiverar ett schema så att pipelinen slutar köras.

Mer information finns i:

> [!div class="nextstepaction"]
> [Använd Azure Machine Learning pipelines för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md)

* Läs mer om [pipelines](concept-ml-pipelines.md)
* Lär dig mer om att [utforska Azure Machine Learning med Jupyter](samples-notebooks.md)