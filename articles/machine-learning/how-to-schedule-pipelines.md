---
title: Schemalägga Azure Machine Learning-pipelines
titleSuffix: Azure Machine Learning
description: Schemalägg Azure Machine Learning-pipelines med Azure Machine Learning SDK för Python. Med schemalagda pipelines kan du automatisera rutinmässiga, tidskrävande uppgifter som databehandling, utbildning och övervakning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: fed411ea171274513308ec3efa68da80e4d25f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116755"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Schemalägga pipelines för maskininlärning med Azure Machine Learning SDK för Python

I den här artikeln får du lära dig hur du schemalägger en pipeline på Ett programmatiskt för att köras på Azure. Du kan välja att skapa ett schema baserat på förfluten tid eller på filsystemändringar. Tidsbaserade scheman kan användas för att ta hand om rutinuppgifter, till exempel övervakning för datadrift. Ändringsbaserade scheman kan användas för att reagera på oregelbundna eller oförutsägbara ändringar, till exempel nya data som överförs eller gamla data som redigeras. När du har lärt dig hur du skapar scheman får du lära dig hur du hämtar och inaktiverar dem.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://aka.ms/AMLFree).

* En Python-miljö där Azure Machine Learning SDK för Python är installerat. Mer information finns i [Skapa och hantera återanvändbara miljöer för utbildning och distribution med Azure Machine Learning.](how-to-use-environments.md)

* En machine learning-arbetsyta med en publicerad pipeline. Du kan använda den som är inbyggd i [Skapa och köra machine learning-pipelines med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Initiera arbetsytan & hämta data

Om du vill schemalägga en pipeline behöver du en referens till arbetsytan, identifieraren för den publicerade pipelinen och namnet på experimentet där du vill skapa schemat. Du kan hämta dessa värden med följande kod:

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

Om du vill köra en pipeline på en återkommande basis skapar du ett schema. En `Schedule` associerar en pipeline, ett experiment och en utlösare. Utlösaren kan antingen`ScheduleRecurrence` vara en som beskriver väntetiden mellan körningar eller en Datastore-sökväg som anger en katalog att titta efter ändringar. I båda fallen behöver du pipeline-identifieraren och namnet på experimentet där schemat ska skapas.

Importera och `Schedule` `ScheduleRecurrence` klasser högst upp i pythonfilen:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Skapa ett tidsbaserat schema

Konstruktorn `ScheduleRecurrence` har `frequency` ett obligatoriskt argument som måste vara en av följande strängar: "Minut", "Timme", "Dag", "Vecka" eller "Månad". Det kräver också `interval` ett heltal argument `frequency` som anger hur många av enheterna ska förflyter mellan schema startar. Med valfria argument kan du vara mer specifik om starttider, som beskrivs i [ScheduleRecurrence SDK-dokumenten](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Skapa `Schedule` en som börjar en körning var 15:e minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Skapa ett ändringsbaserat schema

Pipelines som utlöses av filändringar kan vara effektivare än tidsbaserade scheman. Du kanske till exempel vill utföra ett förbearbetningssteg när en fil ändras eller när en ny fil läggs till i en datakatalog. Du kan övervaka eventuella ändringar i ett datalager eller ändringar i en viss katalog i datalagret. Om du övervakar en viss katalog utlöses _inte_ ändringar i underkataloger i den katalogen.

Om du vill skapa `Schedule`en filreaktiv måste du ange parametern `datastore` i anropet till [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Om du vill övervaka `path_on_datastore` en mapp anger du argumentet.

Argumentet `polling_interval` låter dig i minuter ange hur ofta datalagret kontrolleras för ändringar.

Om pipelinen har konstruerats med en [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)kan du ange `data_path_parameter_name` variabeln till namnet på den ändrade filen genom att ange argumentet.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Valfria argument när du skapar ett schema

Förutom de argument som diskuterats tidigare `status` kan `"Disabled"` du ange argumentet för att skapa ett inaktivt schema. Slutligen `continue_on_step_failure` kan du skicka en boolesk som åsidosätter pipelinens standardfelbeteende.

## <a name="view-your-scheduled-pipelines"></a>Visa schemalagda pipelines

I webbläsaren navigerar du till Azure Machine Learning. Välj **Pipeline-slutpunkter**i avsnittet Slutpunkter på **navigeringspanelen** . Detta tar dig till en lista över de pipelines som publicerats på arbetsytan.

![Sidan Pipelines i AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

På den här sidan kan du se sammanfattande information om alla pipelines på arbetsytan: namn, beskrivningar, status och så vidare. Borra i genom att klicka i pipelinen. På den resulterande sidan finns det mer information om din pipeline och du kan öka detaljnivån i enskilda körningar.

## <a name="deactivate-the-pipeline"></a>Inaktivera pipelinen

Om du `Pipeline` har en som har publicerats, men inte schemalagts, kan du inaktivera den med:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Om pipelinen är schemalagd måste du avbryta schemat först. Hämta schemats identifierare från portalen eller genom att köra:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

När du `schedule_id` har den du vill inaktivera kör du:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Om du `Schedule.list(ws)` sedan kör igen, bör du få en tom lista.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde du Azure Machine Learning SDK för Python för att schemalägga en pipeline på två olika sätt. Ett schema återkommer baserat på förfluten klocktid. Det andra schemat körs om en fil `Datastore` ändras på en angiven eller i en katalog i det arkivet. Du såg hur du använder portalen för att undersöka pipelinen och enskilda körningar. Slutligen har du lärt dig hur du inaktiverar ett schema så att pipelinen slutar köras.

Mer information finns i:

> [!div class="nextstepaction"]
> [Använda Azure Machine Learning Pipelines för batchbedömning](tutorial-pipeline-batch-scoring-classification.md)

* Läs mer om [pipelines](concept-ml-pipelines.md)
* Läs mer om [att utforska Azure Machine Learning med Jupyter](samples-notebooks.md)

