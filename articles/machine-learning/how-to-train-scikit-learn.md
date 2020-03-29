---
title: Träna scikit-learn maskininlärningsmodeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina scikit-learn-utbildningsskript i företagsskala med hjälp av azure Machine Learning SKlearn-uppskattningsklassen. Exempelskripten klassificerar irisblommbilder för att skapa en maskininlärningsmodell baserad på scikit-learns irisdatauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942279"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Skapa scikit-learn-modeller i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kör dina scikit-learn-utbildningsskript i företagsskala med hjälp av azure Machine Learning [SKlearn-uppskattningsklassen.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 

Exempelskripten i den här artikeln används för att klassificera irisblommbilder för att skapa en maskininlärningsmodell baserat på scikit-learns [irisdatauppsättning](https://archive.ics.uci.edu/ml/datasets/iris).

Oavsett om du tränar en machine learning scikit-learn-modell från grunden eller om du tar in en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut utbildningsjobb med öppen källkod med elastiska molnberäkningsresurser. Du kan skapa, distribuera, version och övervaka modeller i produktionsklass med Azure Machine Learning.

## <a name="prerequisites"></a>Krav

Kör den här koden på någon av dessa miljöer:
 - Azure Machine Learning-beräkningsinstans – inga hämtningar eller installation nödvändiga

    - Slutför [självstudiekursen: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad anteckningsboksserver som är förinstallerad med SDK och exempeldatabasen.
    - I exempelträningsmappen på den bärbara servern hittar du en slutförd och utökad anteckningsbok genom att navigera till den här katalogen: **hur du ska använda azureml-> ml-ramverk > scikit-learn >-utbildning > tåg-hyperparameter-tune-deploy-with-sklearn-mapp.**

 - Din egen Jupyter Notebook server

    - [Installera SDK för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
    - Hämta datauppsättningen och exempelskriptfilen 
        - [iris-datauppsättning](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Du kan också hitta en ifylld [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) av den här guiden på GitHub exempelsidan. Anteckningsboken innehåller ett utökat avsnitt som täcker intelligent hyperparameterjustering och hämtar den bästa modellen efter primära mått.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet ställer in utbildningsexperimentet genom att läsa in de python-paket som krävs, initiera en arbetsyta, skapa ett experiment och ladda upp träningsdata och utbildningsskript.

### <a name="import-packages"></a>Importera paket

Importera först nödvändiga Python-bibliotek.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

[Arbetsytan Azure Machine Learning](concept-workspace.md) är resursen på den högsta nivån för tjänsten. Det ger dig en centraliserad plats att arbeta med alla artefakter du skapar. I Python SDK kan du komma åt arbetsytans [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefakter genom att skapa ett objekt.

Skapa ett arbetsyteobjekt från `config.json` filen som [skapats](#prerequisites)i avsnittet förutsättningar .

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Skapa ett maskininlärningsexperiment

Skapa ett experiment och en mapp för att hålla dina träningsskript. Skapa i det här exemplet ett experiment som kallas "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Förbered utbildningsskript

I den här självstudien finns redan utbildningsskriptet **train_iris.py** för dig. I praktiken bör du kunna ta alla anpassade utbildningsskript som de är och köra det med Azure ML utan att behöva ändra koden.

Om du vill använda Azure ML-spårnings- och måttfunktionerna lägger du till en liten mängd Azure ML-kod i ditt utbildningsskript.  Utbildningsskriptet **train_iris.py** visar hur du loggar vissa mått till din `Run` Azure ML-körning med objektet i skriptet.

Det medföljande utbildningsskriptet `iris = datasets.load_iris()` använder exempeldata från funktionen.  För dina egna data kan du behöva använda steg som [Ladda upp datauppsättning och skript](how-to-train-keras.md#data-upload) för att göra data tillgängliga under träningen.

Kopiera utbildningsskriptet **train_iris.py** till projektkatalogen.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Skapa eller hämta ett beräkningsmål

Skapa ett beräkningsmål för ditt scikit-learn-jobb att köra på. Scikit-learn stöder bara enkel nod, CPU-datoranvändning.

Följande kod skapar en Azure Machine Learning-hanterad beräkning (AmlCompute) för din beräkningsresurs för fjärrträning. Skapandet av AmlCompute tar cirka 5 minuter. Om AmlCompute med det namnet redan finns på arbetsytan hoppar den här koden över skapandeprocessen.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Mer information om beräkningsmål finns i [vad som är en beräkningsmålsartikel.](concept-compute-target.md)

## <a name="create-a-scikit-learn-estimator"></a>Skapa en scikit-learn-estimator

[Scikit-learn-kalkylatorn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) är ett enkelt sätt att starta ett scikit-learn-utbildningsjobb på ett beräkningsmål. Det implementeras genom [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klassen, som kan användas för att stödja enkelnod CPU-utbildning.

Om ditt träningsskript behöver ytterligare pip- eller conda-paket för att köras kan du ha `pip_packages` paketen installerade på den resulterande docker-avbildningen genom att skicka deras namn genom argumenten och. `conda_packages`

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Mer information om hur du anpassar pythonmiljön finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Skicka en körning

[Run-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körningshistoriken medan jobbet körs och efter att det har slutförts.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereda**: En dockeravbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbetsytans behållarregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI-klustret kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Kör:** Alla skript i skriptmappen överförs till beräkningsmålet, datalager monteras eller kopieras och entry_script körs. Utdata från stdout och mappen ./logs strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen ./output i körningen kopieras över till körningshistoriken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen kan du spara och registrera den på arbetsytan. Med modellregistrering kan du lagra och modellera dina modeller på arbetsytan för att förenkla [modellhantering och distribution](concept-model-management-and-deployment.md).

Lägg till följande kod i utbildningsskriptet, train_iris.py, för att spara modellen. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrera modellen på din arbetsyta med följande kod. Genom att ange `model_framework` `model_framework_version`parametrarna `resource_configuration`blir och , no-code-modelldistributionen tillgänglig. På så sätt kan du distribuera modellen direkt som en [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) webbtjänst från den registrerade modellen och objektet definierar beräkningsresursen för webbtjänsten.

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

Modellen du just registrerade kan distribueras på exakt samma sätt som alla andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning du använde för utbildning. Distributionen innehåller ett avsnitt om hur du registrerar modeller, men du kan hoppa direkt till att [skapa ett beräkningsmål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

### <a name="preview-no-code-model-deployment"></a>(Förhandsgranska) Distribution av no-code-modell

I stället för den traditionella distributionsvägen kan du också använda funktionen för distribution utan kod (förhandsversion)för scikit-learn. Distribution av no-code-modell stöds för alla inbyggda scikit-learn-modelltyper. Genom att registrera din modell `model_framework`som `model_framework_version`visas `resource_configuration` ovan med , [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) och parametrar, kan du helt enkelt använda den statiska funktionen för att distribuera din modell.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Dessa beroenden ingår i den färdiga behållaren för scikit-learn-inferens.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Den fullständiga [how-to täcker](how-to-deploy-and-where.md) distributionen i Azure Machine Learning på djupet.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en scikit-learn-modell och lärt dig mer om distributionsalternativ. Se de här andra artiklarna om du vill veta mer om Azure Machine Learning.

* [Spåra körningsmått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Referensarkitektur för distribuerad djupinlärningsutbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
