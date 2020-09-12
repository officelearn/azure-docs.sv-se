---
title: Skicka en utbildnings körning till ett beräknings mål
titleSuffix: Azure Machine Learning
description: Träna din Machine Learning-modell på olika utbildnings miljöer (beräknings mål). Du kan enkelt växla mellan utbildnings miljöer. Börja träna lokalt. Om du behöver skala ut växlar du till ett moln baserat beräknings mål.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: da48b593b8f645566b2f9775fabc5d8e62e625b6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661567"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Skicka en utbildnings körning till ett beräknings mål

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder olika utbildnings miljöer ([beräknings mål](concept-compute-target.md)) för att träna din Machine Learning-modell.

När det gäller utbildning är det vanligt att starta på den lokala datorn och senare köra det utbildnings skriptet på ett annat Compute-mål. Med Azure Machine Learning kan du köra skriptet på olika beräknings mål utan att behöva ändra ditt utbildnings skript.

Allt du behöver göra är att definiera miljön för varje beräknings mål i en **skript körnings konfiguration**.  När du sedan vill köra ditt utbildnings experiment på ett annat beräknings mål anger du körnings konfigurationen för den beräkningen.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag
* [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md), `ws`
* Ett beräknings mål, `my_compute_target` .  Skapa ett beräknings mål med:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning-studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Vad är en skript körnings konfiguration?

Du skickar in ditt utbildnings experiment med ett [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) -objekt.  Det här objektet innehåller:

* **source_directory**: käll katalogen som innehåller ditt utbildnings skript
* **skript**: identifiera utbildnings skriptet
* **run_config**: [Kör konfigurationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), som i sin tur definierar var träningen ska ske. I `run_config` anger du beräknings mål och miljö som ska användas när du kör övnings skriptet.  

## <a name="whats-an-environment"></a>Vad är en miljö?

Azure Machine Learning [miljöer](concept-environments.md) är en inkapsling av miljön där din Machine Learning-utbildning sker. De anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och bedömnings skript. De anger också kör tider (python, Spark eller Docker).  

Miljöer anges i objektet i  `run_config` en `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Träna modellen

Kod mönstret för att skicka en tränings körning är detsamma för alla typer av beräknings mål:

1. Skapa ett experiment att köra
1. Skapa en miljö där skriptet ska köras
1. Skapa en skript körnings konfiguration som refererar till Compute-målet och miljön
1. Skicka körningen
1. Vänta tills körningen har slutförts

Eller så kan du:

* Skicka experimentet med ett- `Estimator` objekt som det visas i [träna ml-modeller med uppskattningar](how-to-train-ml-models.md).
* Skicka en HyperDrive-körning för inställning av min [parameter](how-to-tune-hyperparameters.md).
* Skicka ett experiment via [vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment i din arbets yta.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Skapa en miljö

Granskade miljöer innehåller samlingar med python-paket och är tillgängliga i arbets ytan som standard. De här miljöerna backas upp av cachelagrade Docker-avbildningar som minskar kostnaden för att köra förberedelser. För ett fjärrberäknings mål kan du använda någon av dessa populära granskade miljöer för att börja med:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Mer information och information om miljöer finns [i skapa & använda program varu miljöer i Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Lokalt beräknings mål

Om ditt beräknings mål är din **lokala dator**ansvarar du för att se till att alla nödvändiga paket är tillgängliga i python-miljön där skriptet körs.  Använd `python.user_managed_dependencies` för att använda din aktuella python-miljö (eller python på den sökväg som du anger).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Skapa skript körnings konfiguration

Nu när du har ett beräknings mål ( `compute_target` ) och miljö ( `my_environment` ) skapar du en skript körnings konfiguration som kör ditt utbildnings skript ( `train.py` ) som finns i din `project_folder` katalog:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Du kanske också vill ställa in ramverket för din körning.

* För ett HDI-kluster:
    ```python
    src.run_config.framework = "pyspark"
    ```

* För en virtuell dator i fjärrdatorn:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Skicka experimentet

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> När du skickar utbildningen skapas en ögonblicks bild av den katalog som innehåller dina utbildnings skript och skickas till beräknings målet. Den lagras också som en del av experimentet i din arbets yta. Om du ändrar filer och skickar körningen igen kommer bara de ändrade filerna att överföras.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Mer information om ögonblicks bilder finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exempel på bärbara datorer

Se dessa antecknings böcker för exempel på utbildning med olika beräknings mål:
* [instruktion för att använda – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Lär dig hur du [effektivt justerar](how-to-tune-hyperparameters.md) de båda parametrarna för att bygga bättre modeller. Visa = Azure-ml-py&bevara – Visa = sant)
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa SDK-referens för [RunConfiguration-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true) .
* [Använda Azure Machine Learning med virtuella Azure-nätverk](how-to-enable-virtual-network.md)