---
title: Starta, övervaka och avbryta träningskörningar i Python
titleSuffix: Azure Machine Learning service
description: Lär dig hur du kommer igång, status, tagg och organisera exempelexperiment för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 4/5/2019
ms.openlocfilehash: 726273024a2da0cea5207c86140f3c31263a208f
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426743"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Starta, övervaka och avbryta träningskörningar i Python

Den [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) innehåller olika metoder för att övervaka, organisera och hantera dina körningar för utbildning och experimentering.

Den här anvisningen visar exempel på följande uppgifter:

* [Övervaka köra prestanda](#monitor)
* [Avbryta eller misslyckas körs](#cancel)
* [Skapa underordnade körningar](#children)
* [Tagga och hitta körningar](#tag)

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md).

* Azure Machine Learning-SDK för Python installerat (version 1.0.21 eller senare). Om du vill installera eller uppdatera till den senaste versionen av SDK, gå till den [installera/uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) sidan.

    Använd följande kod för att kontrollera vilken version av SDK: N för Azure Machine Learning.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-and-status-a-run"></a>Start- och status för en körning

Konfigurera ditt experiment genom att importera den [arbetsytan](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experimentera](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [kör](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), och [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) klasser från [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) paketet.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Starta en körning och dess loggningsprocessen med den [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metod.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

Hämta status för körning med [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

För mer information om hur du kör använder [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

När din körning har slutförts kan du använda den [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metod för att markera det som slutfört.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Du kan också använda Python's `with...as` mönster. I det här sammanhanget markerar Kör automatiskt själva som slutförd när körningen är utanför omfånget. Det här sättet du behöver inte manuellt Markera körningen som slutförd.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Avbryta eller misslyckas körs

 Om du ser ett misstag eller din körning verkar vara tar lång tid att slutföra, Använd den [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metod för att stoppa körningen innan den är klar och markeras som har avbrutits.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Om din körning är klar, men innehåller ett fel i stil, felaktig inlärningsskript användes, kan du använda den [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metod för att markera den som misslyckades.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Skapa underordnade körningar

Skapa underordnade körningar gruppera relaterade körningar som för olika finjustering justering iterationer.

Det här kodexemplet använder hello_with_children.py-skript för att skapa en batch med fem underordnade körs från inom en skickade kör med den [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metod.

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Underordnade körs fullständig automatiskt när de flyttas utanför omfånget.

Du kan också starta underordnade körs en i taget, men eftersom varje skapa resulterar i ett nätverksanrop, är det mindre effektivt än att skicka in en batch med körningar.

Använd den [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metod för att fråga efter underordnat körs för en viss överordnade.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Tagga och hitta körningar

I Azure Machine Learning-tjänsten kan du använda egenskaper och taggar för att sortera och fråga efter dina körningar för viktig information.

### <a name="add-properties-and-tags"></a>Lägg till egenskaper och taggar

Använd den [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) lägga till sökbara metadata på dina körningar. Till exempel följande kod lägger till egenskapen ”författare” körningen.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Egenskaperna är inte kan ändras, vilket är användbart som en permanent post i granskningssyfte. Följande exempel resulterar i ett fel, eftersom vi redan lagts till ”azureml-användare” som ”författare”-egenskap i föregående kod.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Taggar, men är kan ändras. Använd [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) att lägga till sökbart och beskrivande information för användare av experimentet.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Du kan också lägga till en enkel sträng-tagg. Den visas i taggen intunemamsettings med värdet för `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Frågeegenskaper och taggar

Du kan fråga körs i ett experiment som överensstämmer med specifika egenskaper och taggar.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Exempel-anteckningsböcker

Följande anteckningsböcker demonstrera begreppen i den här artikeln:

* Mer information om loggning av API: er finns i [loggning API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Mer information om hur du hanterar körs med Azure Machine Learning SDK finns i den [hantera körningar notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Nästa steg

* Läs hur du loggar in mått för dina experiment i den [logga mått under träningskörningar](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) artikeln.