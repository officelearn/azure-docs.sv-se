---
title: Starta, övervaka och avbryta träningskörningar i Python
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att starta, ange status för taggen och organisera dina machine learning-experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819209"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Starta, övervaka och avbryta träningskörningar i Python

Den [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) innehåller olika metoder för att övervaka, organisera och hantera dina körningar för utbildning och experimentering.

Den här artikeln visar exempel på följande uppgifter:

* Övervakare som kör prestanda.
* Avbryt, annars misslyckas körs.
* Skapa underordnade körs.
* Tagga och hitta körs.

## <a name="prerequisites"></a>Nödvändiga komponenter

Behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En [Azure Machine Learning-tjänstens arbetsyta](setup-create-workspace.md).

* Azure Machine Learning-SDK för Python (version 1.0.21 eller senare). Om du vill installera eller uppdatera till den senaste versionen av SDK, se [installera eller uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Använd följande kod för att kontrollera vilken version av Azure Machine Learning-SDK:

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Starta en körning och dess loggningsprocessen

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

## <a name="monitor-the-status-of-a-run"></a>Övervaka status för en körning

Hämta status för en körning med den [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metod.

```Python
print(notebook_run.get_status())
```

Om du vill ha mer information om körningen, använda den [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metod.

```Python
notebook_run.get_details()
```

När din körning har slutförts kan du använda den [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metod för att markera det som slutfört.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Om du använder Python's `with...as` mönster, kör automatiskt markeras själva som slutfört när körningen är utanför omfånget. Du behöver inte manuellt Markera körningen som slutförd.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Avbryta eller misslyckas körs

 Om du ser ett fel eller om din körning tar för lång tid att slutföra, den [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metod för att stoppa körningen innan den är klar och markeras som har avbrutits.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Om din körning är klar, men den innehåller ett fel (till exempel felaktig inlärningsskript användes), du kan använda den [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metod för att markera den som misslyckades.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Skapa underordnade körningar

Skapa underordnade körningar gruppera relaterade körs, till exempel för olika finjustering justering iterationer.

Det här kodexemplet använder den `hello_with_children.py` skript för att skapa en batch med fem underordnade körs från inom en skickade körning med hjälp av den [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metod:

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
> När de flyttas utanför markeras automatiskt underordnade körningar som slutförd.

Du kan också starta underordnade körs en i taget, men eftersom varje skapa resulterar i ett nätverksanrop, är det mindre effektivt än att skicka in en batch med körningar.

Om du vill fråga underordnade körningar av en viss överordnade, använda den [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metod.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Tagga och hitta körningar

I Azure Machine Learning-tjänsten kan du använda egenskaper och taggar för att sortera och fråga efter dina körningar för viktig information.

### <a name="add-properties-and-tags"></a>Lägg till egenskaper och taggar

Om du vill lägga till sökbara metadata i dina körningar, använda den [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metod. Till exempel följande kod lägger till den `"author"` egenskapen kör:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Egenskaperna är inte kan ändras, så att de skapar en permanent post i granskningssyfte. I följande kod exempel resulterar i ett fel, eftersom vi redan lagts till `"azureml-user"` som den `"author"` egenskapsvärdet i föregående kod:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Till skillnad från egenskaperna för är taggar kan ändras. Lägg till information om sökbart och meningsfulla för konsumenter av experimentet genom att använda den [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metod.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Du kan också lägga till sträng taggar. När dessa taggar visas i ordlistan tagg kan de ha värdet `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Frågeegenskaper och taggar

Du kan fråga körs i ett experiment att returnera en lista över körningar som överensstämmer med specifika egenskaper och taggar.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Exempel-anteckningsböcker

Följande anteckningsböcker demonstrera koncept som i den här artikeln:

* Mer information om loggning API: er finns i [loggning API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Mer information om hur du hanterar körs med Azure Machine Learning SDK finns i den [hantera körningar notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Nästa steg

* Läs hur du loggar in mått för dina experiment i [logga mått under träningskörningar](how-to-track-experiments.md).