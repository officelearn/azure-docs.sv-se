---
title: Starta, övervaka och avbryta inlärnings körningar i python
titleSuffix: Azure Machine Learning
description: Lär dig hur du startar, ställer in status för, tagga och ordnar dina dator inlärnings experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: fc9dee3d4ace5f70c4238cdce5c57696b131bfa9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897307"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Starta, övervaka och avbryta inlärnings körningar i python


[Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true), [Machine Learning CLI](reference-azure-machine-learning-cli.md)och [Azure Machine Learning Studio](https://ml.azure.com) tillhandahåller olika metoder för att övervaka, organisera och hantera dina körningar för utbildning och experimentering.

Den här artikeln innehåller exempel på följande uppgifter:

* Övervaka körnings prestanda.
* Avbryt eller kör inte.
* Skapa underordnade körningar.
* Tagga och hitta körningar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande objekt:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* Azure Machine Learning SDK för python (version 1.0.21 eller senare). Information om hur du installerar eller uppdaterar till den senaste versionen av SDK finns i [Installera eller uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).

    Använd följande kod för att kontrol lera din version av Azure Machine Learning SDK:

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -och [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Starta en körning och dess loggnings process

### <a name="using-the-sdk"></a>Med SDK

Konfigurera experimentet genom att importera [arbets ytan](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true), [experimentet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true), [körnings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true)-och [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) -klasserna från [azureml. Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py&preserve-view=true) -paketet.

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Starta en körning och dess loggnings process med- [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) metoden.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Använda CLI

Gör så här för att starta en körning av experimentet:

1. Använd Azure CLI från ett gränssnitt eller en kommando tolk för att autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

1. Koppla en arbets ytans konfiguration till den mapp som innehåller ditt utbildnings skript. Ersätt `myworkspace` med din Azure Machine Learning-arbetsyta. Ersätt `myresourcegroup` med Azure-resurs gruppen som innehåller din arbets yta:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Det här kommandot skapar en `.azureml` under katalog som innehåller exempel på runconfig-och Conda-miljöfiler. Den innehåller också en `config.json` fil som används för att kommunicera med din Azure Machine Learning-arbetsyta.

    Mer information finns i [AZ ml Folder Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Starta körningen med hjälp av följande kommando. När du använder det här kommandot anger du namnet på runconfig-filen (texten före \* . runconfig om du tittar på ditt fil system) mot parametern-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`Kommandot skapade en `.azureml` under katalog, som innehåller två exempel runconfig-filer.
    >
    > Om du har ett Python-skript som skapar ett kör konfigurations objekt program mässigt kan du använda [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) för att spara det som en RunConfig-fil.
    >
    > Fler exempel på runconfig-filer finns i [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .

    Mer information finns i [AZ ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning Studio

För att starta en skicka en pipeline-körning i designern, använder du följande steg:

1. Ange ett standard beräknings mål för din pipeline.

1. Välj **Kör** överst i pipeline-arbetsytan.

1. Välj ett experiment för att gruppera dina pipelines-körningar.

## <a name="monitor-the-status-of-a-run"></a>Övervaka status för en körning

### <a name="using-the-sdk"></a>Med SDK

Hämta status för en körning med- [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=trueget-status--) metoden.

```python
print(notebook_run.get_status())
```

Använd-metoden för att hämta körnings-ID, körnings tid och ytterligare information om körningen [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#&preserve-view=trueget-details--) .

```python
print(notebook_run.get_details())
```

När körningen har slutförts använder du [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) metoden för att markera den som slutförd.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Om du använder python `with...as` : s design mönster markeras körningen automatiskt som slutförd när körningen är utanför omfånget. Du behöver inte markera kör som slutförd manuellt.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Använda CLI

1. Om du vill visa en lista över körningar för experimentet använder du följande kommando. Ersätt `experiment` med namnet på ditt experiment:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Det här kommandot returnerar ett JSON-dokument som visar information om körningar för det här experimentet.

    Mer information finns i [AZ ml experiment List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Använd följande kommando om du vill visa information om en speciell körning. Ersätt `runid` med ID: t för körningen:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Det här kommandot returnerar ett JSON-dokument som visar information om körningen.

    Mer information finns i [AZ ml Run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning Studio

För att visa antalet aktiva körningar för experimentet i Studio.

1. Gå till avsnittet **experiment** .. 

1. Välj ett experiment.

    På experiment-sidan kan du se antalet aktiva beräknings mål och varaktigheten för varje körning. 

1. Välj ett speciellt körnings nummer.

1. På fliken **loggar** kan du hitta diagnostik-och fel loggar för din pipeline-körning.


## <a name="cancel-or-fail-runs"></a>Avbryt eller kör inte

Om du ser ett fel eller om körningen tar för lång tid att slutföra, kan du avbryta körningen.

### <a name="using-the-sdk"></a>Med SDK

Om du vill avbryta en körning med hjälp av SDK använder du [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truecancel--) metoden:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Om din körning är klar, men den innehåller ett fel (till exempel om ett felaktigt utbildnings skript användes), kan du använda [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) metoden för att markera den som misslyckad.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Använda CLI

Om du vill avbryta en körning med hjälp av CLI använder du följande kommando. Ersätt `runid` med ID: t för körningen

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Mer information finns i [AZ ml kör Avbryt](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning Studio

Om du vill avbryta en körning i Studio använder du följande steg:

1. Gå till pipelinen som körs i avsnittet **experiment** eller **pipelines** . 

1. Välj det körnings nummer för pipelinen som du vill avbryta.

1. I verktygsfältet väljer du **Avbryt**


## <a name="create-child-runs"></a>Skapa underordnade körningar

Skapa underordnade körningar för att gruppera relaterade körningar, t. ex. för olika upprepningar i en parameter.

> [!NOTE]
> Underordnade körningar kan bara skapas med SDK: n.

I det här kod exemplet används `hello_with_children.py` skriptet för att skapa en grupp med fem underordnade körningar från en skickad körning med hjälp av [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-) metoden:

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

Använd metoden för att skapa många underordnade körningar effektivt [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) . Eftersom varje skapande resulterar i ett nätverks anrop är det mer effektivt att skapa en batch med körningar än att skapa en i taget.

### <a name="submit-child-runs"></a>Skicka underordnade körningar

Underordnade körningar kan också skickas från en överordnad körning. På så sätt kan du skapa hierarkier med överordnade och underordnade körningar. 

Du kanske vill att ditt underordnade ska köras för att använda en annan körnings konfiguration än den överordnade körningen. Till exempel kan du använda en mindre kraftfull, PROCESSORbaserade konfiguration för den överordnade, samtidigt som du använder GPU-baserade konfigurationer för dina barn. En annan vanlig önskan är att skicka alla underordnade olika argument och data. Om du vill anpassa en underordnad körning skickar `RunConfiguration` du ett objekt till den underordnade `ScriptRunConfig` konstruktorn. Det här kod exemplet, som skulle vara en del av det överordnade `ScriptRunConfig` objektets skript:

- Skapar en `RunConfiguration` namngiven beräknings resurs `"gpu-compute"`
- Itererar över olika argument värden som ska skickas till underordnade `ScriptRunConfig` objekt
- Skapar och skickar en ny underordnad körning med hjälp av den anpassade beräknings resursen och argumentet
- Block tills alla underordnade har slutförts

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

Använd metoden för att skapa många underordnade körningar med identiska konfigurationer, argument och indata [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) . Eftersom varje skapande resulterar i ett nätverks anrop är det mer effektivt att skapa en batch med körningar än att skapa en i taget.

I en underordnad körning kan du Visa överordnad körnings-ID:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Frågans underordnade körningar

Använd metoden för att fråga de underordnade körningarna av en speciell överordnad [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) . Med ``recursive = True`` argumentet kan du fråga ett kapslat träd med underordnade och Grandchildren.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Tagga och hitta körningar

I Azure Machine Learning kan du använda egenskaper och taggar för att organisera och fråga dina körningar efter viktig information.

### <a name="add-properties-and-tags"></a>Lägg till egenskaper och Taggar

#### <a name="using-the-sdk"></a>Med SDK

Om du vill lägga till sökbara metadata i dina körningar använder du- [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=trueadd-properties-properties-) metoden. Följande kod lägger till exempel till `"author"` egenskapen i kör:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Egenskaperna är oföränderliga så att de skapar en permanent post för gransknings syfte. Följande kod exempel resulterar i ett fel eftersom vi redan har lagts till `"azureml-user"` som `"author"` egenskaps värde i föregående kod:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Till skillnad från egenskaper är taggarna föränderligt. Använd-metoden för att lägga till sökbar och meningsfull information för användare av experimentet [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truetag-key--value-none-) .

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Du kan också lägga till enkla sträng taggar. När taggarna visas i kod ord listan som nycklar har de värdet `None` .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Använda CLI

> [!NOTE]
> Med CLI kan du bara lägga till eller uppdatera taggar.

Använd följande kommando om du vill lägga till eller uppdatera en tagg:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Mer information finns i [AZ ml kör Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Frågeegenskaper och Taggar

Du kan köra frågor i ett experiment för att returnera en lista över körningar som matchar vissa egenskaper och taggar.

#### <a name="using-the-sdk"></a>Med SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Använda CLI

Azure CLI stöder [JMESPath](http://jmespath.org) -frågor som kan användas för att filtrera körningar baserat på egenskaper och taggar. Om du vill använda en JMESPath-fråga med Azure CLI anger du den med `--query` parametern. I följande exempel visas några frågor med hjälp av egenskaper och Taggar:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Mer information om hur du frågar Azure CLI-resultat finns i [läsa utdata från Azure CLI-kommandot](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning Studio

1. Navigera till avsnittet **pipelines** .

1. Använd Sök fältet för att filtrera pipelines med taggar, beskrivningar, experiment namn och namn på sändning.

## <a name="example-notebooks"></a>Exempelnotebook-filer

Följande antecknings böcker demonstrerar begreppen i den här artikeln:

* Mer information om loggnings-API: er finns i [logg-API-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Mer information om hur du hanterar körningar med Azure Machine Learning SDK finns i [antecknings boken hantera körningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Nästa steg

* Information om hur du loggar mått för dina experiment finns i [logg mått under inlärnings körningar](how-to-track-experiments.md).
* Information om hur du övervakar resurser och loggar från Azure Machine Learning finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).
