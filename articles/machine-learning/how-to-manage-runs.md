---
title: Starta, övervaka och avbryta utbildningskörningar i Python
titleSuffix: Azure Machine Learning
description: Lär dig hur du startar, anger status för, taggar och organiserar dina maskininlärningsexperiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: cdc739c7464b3deb87faaaabfd8d657ae8c28678
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617767"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Starta, övervaka och avbryta utbildningskörningar i Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[Azure Machine Learning SDK för Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)Machine Learning [CLI](reference-azure-machine-learning-cli.md)och Azure Machine [Learning studio](https://ml.azure.com) innehåller olika metoder för att övervaka, organisera och hantera dina körningar för utbildning och experiment.

I den här artikeln visas exempel på följande uppgifter:

* Övervaka kör prestanda.
* Avbryt eller misslyckas körs.
* Skapa underordnade körningar.
* Tagga och hitta körningar.

## <a name="prerequisites"></a>Krav

Du behöver följande:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* Azure Machine Learning SDK för Python (version 1.0.21 eller senare). Om du vill installera eller uppdatera till den senaste versionen av SDK finns i [Installera eller uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Så här kontrollerar du din version av Azure Machine Learning SDK:

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI-](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) och [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Starta en körning och dess loggningsprocess

### <a name="using-the-sdk"></a>Med SDK

Konfigurera experimentet genom att importera klasserna [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)och [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) från [azureml.core-paketet.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Starta en körning och dess [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) loggningsprocess med metoden.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Använda CLI

Så här startar du en körning av experimentet:

1. Från ett skal eller en kommandotolk använder du Azure CLI för att autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

1. Bifoga en arbetsytas konfiguration till mappen som innehåller utbildningsskriptet. Ersätt `myworkspace` med din Azure Machine Learning-arbetsyta. Ersätt `myresourcegroup` med den Azure-resursgrupp som innehåller arbetsytan:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Det här kommandot `.azureml` skapar en underkatalog som innehåller exempelkörnings- och conda-miljöfiler. Den innehåller också `config.json` en fil som används för att kommunicera med din Azure Machine Learning-arbetsyta.

    Mer information finns i [az ml-mapprena](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Starta körningen genom att använda följande kommando. När du använder det här kommandot anger du namnet på \*runconfig-filen (texten före .runconfig om du tittar på filsystemet) mot parametern -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Kommandot `az ml folder attach` skapade `.azureml` en underkatalog, som innehåller två exempel runconfig filer.
    >
    > Om du har ett Python-skript som skapar ett körningskonfigurationsobjekt programmässigt kan du använda [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) för att spara det som en runconfig-fil.
    >
    > Mer exempel på runconfig-filer finns i [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Mer information finns i [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning studio

Så här startar du en skicka en pipeline-körning i designern (förhandsversion):

1. Ange ett standardberäkningsmål för pipelinen.

1. Välj **Kör** högst upp på pipeline-arbetsytan.

1. Välj ett experiment för att gruppera pipeline-körningar.

## <a name="monitor-the-status-of-a-run"></a>Övervaka status för en körning

### <a name="using-the-sdk"></a>Med SDK

Hämta status för en [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) körning med metoden.

```python
print(notebook_run.get_status())
```

Använd metoden för att hämta körnings-ID, körningstid [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) och ytterligare information om körningen.

```python
print(notebook_run.get_details())
```

När körningen är klar använder [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) du metoden för att markera den som slutförd.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Om du använder `with...as` Pythons designmönster markeras körningen automatiskt som slutförd när körningen är utanför omfånget. Du behöver inte markera körningen som slutförd manuellt.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Använda CLI

1. Om du vill visa en lista över körningar för experimentet använder du följande kommando. Ersätt `experiment` med namnet på experimentet:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Det här kommandot returnerar ett JSON-dokument som listar information om körningar för det här experimentet.

    Mer information finns i [experimentlistan az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Om du vill visa information om en viss körning använder du följande kommando. Ersätt `runid` med ID:et för körningen:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Det här kommandot returnerar ett JSON-dokument som listar information om körningen.

    Mer information finns i [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning studio

Om du vill visa antalet aktiva körningar för experimentet i studion.

1. Navigera till avsnittet **Experiment..** 

1. Välj ett experiment.

    På experimentsidan kan du se antalet aktiva beräkningsmål och varaktigheten för varje körning. 

1. Välj ett visst körningsnummer.

1. På fliken **Loggar** hittar du diagnostik- och felloggar för pipeline-körningen.


## <a name="cancel-or-fail-runs"></a>Avbryt eller misslyckas körs

Om du upptäcker ett misstag eller om körningen tar för lång tid att slutföra kan du avbryta körningen.

### <a name="using-the-sdk"></a>Med SDK

Om du vill avbryta en körning [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) med SDK använder du metoden:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Om körningen är klar, men den innehåller ett fel (till exempel det [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) felaktiga utbildningsskriptet användes), kan du använda metoden för att markera det som misslyckat.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Använda CLI

Om du vill avbryta en körning med CLI använder du följande kommando. Ersätt `runid` med ID:et för körningen

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Mer information finns i [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning studio

Så här avbryter du en körning i studion med hjälp av följande steg:

1. Gå till pipelinen som körs i avsnittet **Experiment** eller **Pipelines.** 

1. Välj det pipeline-körningsnummer som du vill avbryta.

1. Välj **Avbryt i** verktygsfältet


## <a name="create-child-runs"></a>Skapa underordnade körningar

Skapa underordnade körningar för att gruppera relaterade körningar, till exempel för olika hyperparameter-tuning iterationer.

> [!NOTE]
> Underordnade körningar kan bara skapas med SDK.

I det här `hello_with_children.py` kodexemplet används skriptet för att skapa [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) en batch med fem underordnade körningar inifrån en inskickade körning med hjälp av metoden:

```python
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
> När de flyttas utanför omfånget markeras underordnade körningar automatiskt som slutförda.

Om du vill skapa många [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) underordnade körningar effektivt använder du metoden. Eftersom varje skapande resulterar i ett nätverksanrop är det effektivare att skapa en batch med körningar än att skapa dem en efter en.

### <a name="submit-child-runs"></a>Skicka in underordnade körningar

Underordnade körningar kan också skickas från en överordnad körning. På så sätt kan du skapa hierarkier för överordnade och underordnade körningar. 

Du kanske vill att ditt barn kör ska använda en annan körningskonfiguration än den överordnade körningen. Du kan till exempel använda en mindre kraftfull, CPU-baserad konfiguration för den överordnade, när du använder GPU-baserade konfigurationer för dina barn. En annan vanlig önskan är att skicka varje barn olika argument och data. Om du vill anpassa `RunConfiguration` en underordnad körning `ScriptRunConfig` skickar du ett objekt till barnets konstruktor. Det här kodexemplet, `ScriptRunConfig` som skulle vara en del av det överordnade objektets skript:

- Skapar en `RunConfiguration` hämtning av en namngiven beräkningsresurs`"gpu-compute"`
- Itererar över olika argumentvärden som `ScriptRunConfig` ska skickas till underordnade objekt
- Skapar och skickar en ny underordnad körning med hjälp av den anpassade beräkningsresursen och argumentet
- Blockerar tills hela barnet körs klart

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Om du vill skapa många underordnade körningar med identiska [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) konfigurationer, argument och indata effektivt använder du metoden. Eftersom varje skapande resulterar i ett nätverksanrop är det effektivare att skapa en batch med körningar än att skapa dem en efter en.

I en underordnad körning kan du visa det överordnade körnings-ID:t:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Frågeorden körs

Om du vill fråga efter de [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) underordnade körningarna för en viss överordnad använder du metoden. Argumentet ``recursive = True`` låter dig fråga ett kapslat träd med barn och barnbarn.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Tagga och hitta körningar

I Azure Machine Learning kan du använda egenskaper och taggar för att organisera och fråga dina körningar efter viktig information.

### <a name="add-properties-and-tags"></a>Lägga till egenskaper och taggar

#### <a name="using-the-sdk"></a>Med SDK

Om du vill lägga till sökbara metadata i dina körningar använder du [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metoden. Följande kod lägger till `"author"` egenskapen i körningen:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Egenskaper är oföränderliga, så de skapar en permanent post för granskningsändamål. Följande kodexempel resulterar i ett `"azureml-user"` fel, eftersom vi redan har lagt till egenskapsvärdet `"author"` i föregående kod:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Till skillnad från egenskaper är taggarna mutable. Om du vill lägga till sökbar och [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) meningsfull information för konsumenter av experimentet använder du metoden.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Du kan också lägga till enkla strängtaggar. När dessa taggar visas i taggordlistan som `None`nycklar har de värdet .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Använda CLI

> [!NOTE]
> Med CLI kan du bara lägga till eller uppdatera taggar.

Om du vill lägga till eller uppdatera en tagg använder du följande kommando:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Mer information finns i [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Egenskaper och taggar för frågor

Du kan fråga körs i ett experiment för att returnera en lista över körningar som matchar specifika egenskaper och taggar.

#### <a name="using-the-sdk"></a>Med SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Använda CLI

Azure CLI stöder [JMESPath-frågor,](http://jmespath.org) som kan användas för att filtrera körningar baserat på egenskaper och taggar. Om du vill använda en JMESPath-fråga `--query` med Azure CLI anger du den med parametern. I följande exempel visas grundläggande frågor med egenskaper och taggar:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Mer information om hur du frågar Azure CLI-resultat finns i [Fråga Azure CLI-kommandoutdata](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning studio

1. Navigera till avsnittet **Pipelines.**

1. Använd sökfältet för att filtrera pipelines med hjälp av taggar, beskrivningar, experimentnamn och inlämnarenamn.

## <a name="example-notebooks"></a>Exempel på anteckningsböcker

Följande anteckningsböcker visar begreppen i den här artikeln:

* Mer information om loggnings-API:erna finns i [loggnings-API-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Mer information om hur du hanterar körningar med Azure Machine Learning SDK finns i [anteckningsboken hantera körningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du loggar mått för dina experiment finns [i Logga mått under utbildningskörningar](how-to-track-experiments.md).
* Mer information om hur du övervakar resurser och loggar från Azure Machine Learning finns i [Övervaka Azure Machine Learning](monitor-azure-machine-learning.md).