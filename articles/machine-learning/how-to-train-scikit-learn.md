---
title: Utbilda scikit – lär dig Machine Learning-modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör scikit för att lära dig mer om att köra utbildnings skript på Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: dfe3d0e7bf0d291807a5a051f834753c7816801a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320892"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Bygg scikit – lär dig modeller i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kör dina scikit-utbildnings skript med Azure Machine Learning.

Exempel skripten i den här artikeln används för att klassificera Iris blomma-bilder för att bygga en maskin inlärnings modell baserad på scikit- [data uppsättningen](https://archive.ics.uci.edu/ml/datasets/iris).

Oavsett om du tränar en Machine Learning-scikit – lär dig modell från grunden eller om du använder en befintlig modell i molnet, kan du använda Azure Machine Learning för att skala ut utbildnings jobb med öppen källkod med elastiska moln beräknings resurser. Du kan bygga, distribuera, hantera och övervaka modeller av produktions klass med Azure Machine Learning.

## <a name="prerequisites"></a>Krav

Kör den här koden i någon av följande miljöer:
 - Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

    - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel inlärning på Notebook-servern hittar du en slutförd och expanderad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > scikit-lär > utbildning > träna-den parameter-Tune-Deploy-with-sklearn** -mappen.

 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Förbereda skript

I den här självstudien är utbildnings skriptet **train_iris. py** redan tillgängligt för [dig.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) I praktiken bör du kunna ta med ett anpassat tränings skript som är och köra det med Azure ML utan att behöva ändra koden.

Obs!
- Det tillhandahållna utbildnings skriptet visar hur du loggar vissa mått i Azure ML-körningen med hjälp av `Run` objektet i skriptet.
- Det tillhandahållna utbildnings skriptet använder exempel data från `iris = datasets.load_iris()` funktionen.  För dina egna data kan du behöva använda steg som att [Ladda upp data uppsättning och skript](how-to-train-keras.md#data-upload) för att göra data tillgängliga under utbildningen.

### <a name="define-your-environment"></a>Definiera din miljö.

#### <a name="create-a-custom-environment"></a>Skapa en anpassad miljö.

Redigera din Conda-miljö (sklearn-ENV. yml).
Om du vill skriva Conda-miljön från en bärbar dator kan du lägga till linjen ```%%writefile sklearn-env.yml``` överst i cellen.

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Skapa en Azure ML-miljö från den här Conda-miljö specifikationen. Miljön kommer att paketeras i en Docker-behållare vid körning.
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
```

#### <a name="use-a-curated-environment"></a>Använda en granskad miljö
Azure ML tillhandahåller färdiga, granskade behållar miljöer om du inte vill skapa en egen avbildning. Mer information finns [här](resource-curated-environments.md).
Om du vill använda en granskad miljö kan du köra följande kommando i stället:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig

Den här ScriptRunConfig skickar jobbet för körning på det lokala beräknings målet.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
src.run_config.environment = myenv
```

Om du vill skicka ett fjärran slutet kluster kan du ändra run_config. Target till önskat beräknings mål.

### <a name="submit-your-run"></a>Skicka in din körning
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. I stället kan du komma åt dina data med hjälp av ett data [lager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).

Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Vad händer under körningen
När körningen körs går den igenom följande steg:

- **Förbereder**: en Docker-avbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen./outputs i körningen kopieras till körnings historiken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen kan du spara och registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

Lägg till följande kod i utbildnings skriptet train_iris. py för att spara modellen. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrera modellen på din arbets yta med följande kod. Genom att ange parametrarna `model_framework` , `model_framework_version` , och `resource_configuration` , blir modell distribution utan kod tillgängligt. Med modell distribution utan kod kan du distribuera din modell direkt som en webb tjänst från den registrerade modellen, och [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) objektet definierar beräknings resursen för webb tjänsten.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Distribution

Den modell som du precis har registrerat kan distribueras exakt på samma sätt som andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning som du använde för utbildning. Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

### <a name="preview-no-code-model-deployment"></a>Förhandsgranskningsvyn Distribution utan kod modell

I stället för den traditionella distributions vägen kan du också använda funktionen utan kod distribution (för hands version) för scikit-information. Distribution med ingen kod modell stöds för alla inbyggda scikit – lär dig modell typer. Genom att registrera din modell enligt vad som visas ovan med `model_framework` `model_framework_version` parametrarna,, och `resource_configuration` kan du bara använda den [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statiska funktionen för att distribuera modellen.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Obs! de här beroendena ingår i den förskapade scikit för att få en behållare.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Den fullständiga [instruktionen att](how-to-deploy-and-where.md) distribuera i Azure Machine Learning större djup.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en scikit-modell och lärt dig om distributions alternativ. Mer information om Azure Machine Learning finns i de här artiklarna.

* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
