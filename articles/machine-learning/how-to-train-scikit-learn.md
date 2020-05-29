---
title: Utbilda scikit – lär dig Machine Learning-modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina scikit-utbildnings skript i företags skala med hjälp av Azure Machine Learning SKlearns uppskattnings klass. Exempel skripten klassificerar Iris blomma-bilder för att bygga en maskin inlärnings modell baserad på scikit-data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: c842e0ea126ffe4c3328c4fd237e2891d6cff229
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168819"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Bygg scikit – lär dig modeller i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kör dina scikit i företags skala med hjälp av Azure Machine Learning [SKlearns uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klass. 

Exempel skripten i den här artikeln används för att klassificera Iris blomma-bilder för att bygga en maskin inlärnings modell baserad på scikit- [data uppsättningen](https://archive.ics.uci.edu/ml/datasets/iris).

Oavsett om du tränar en Machine Learning-scikit – lär dig modell från grunden eller om du använder en befintlig modell i molnet, kan du använda Azure Machine Learning för att skala ut utbildnings jobb med öppen källkod med elastiska moln beräknings resurser. Du kan bygga, distribuera, hantera och övervaka modeller av produktions klass med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:
 - Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

    - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel inlärning på Notebook-servern hittar du en slutförd och expanderad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > scikit-lär > utbildning > träna-den parameter-Tune-Deploy-with-sklearn** -mappen.

 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - Hämta data uppsättningen och exempel skript filen 
        - [Iris-datauppsättning](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris. py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller ett expanderat avsnitt som täcker intelligenta parametrar för att justera och hämta den bästa modellen med primära mått.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

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

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Skapa ett maskininlärningsexperiment

Skapa ett experiment och en mapp för att lagra dina utbildnings skript. I det här exemplet skapar du ett experiment med namnet "sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

experiment = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Förbered utbildnings skript

I den här självstudien har utbildnings skriptet **train_iris. py** redan angetts för dig. I praktiken bör du kunna ta med ett anpassat tränings skript som är och köra det med Azure ML utan att behöva ändra koden.

Om du vill använda funktionerna för Azure ML-spårning och-mått lägger du till en liten del av Azure ML-koden i utbildnings skriptet.  Övnings skriptet **train_iris. py** visar hur du loggar vissa mått i Azure ml-körningen med hjälp av `Run` objektet i skriptet.

Det tillhandahållna utbildnings skriptet använder exempel data från `iris = datasets.load_iris()` funktionen.  För dina egna data kan du behöva använda steg som att [Ladda upp data uppsättning och skript](how-to-train-keras.md#data-upload) för att göra data tillgängliga under utbildningen.

Kopiera övnings skriptet **train_iris. py** i projekt katalogen.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Skapa eller hämta ett beräknings mål

Skapa ett beräknings mål för ditt scikit – Läs jobb att köra på. Scikit – Läs bara stöd för en nod, CPU-bearbetning.

Följande kod skapar en Azure Machine Learning Managed Compute (AmlCompute) för beräknings resursen för Fjärrutbildning. Skapandet av AmlCompute tar cirka 5 minuter. Om AmlCompute med det namnet redan finns i din arbets yta hoppar den här koden över skapande processen.

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

Mer information om beräknings mål finns i artikeln [Vad är en Compute Target](concept-compute-target.md) -artikel.

## <a name="create-a-scikit-learn-estimator"></a>Skapa en scikit – lär dig uppskattning

[Scikit-lär dig uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) tjänsten ger ett enkelt sätt att starta ett scikit utbildnings jobb på ett beräknings mål. Den implementeras via [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klassen, som kan användas för att stödja CPU-utbildning med en nod.

Om ditt utbildnings skript behöver ytterligare pip-eller Conda-paket för att kunna köras, kan du ha paketen installerade på den resulterande Docker-avbildningen genom att skicka namnen via `pip_packages` `conda_packages` argumenten och.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py',
                    pip_packages=['joblib']
                   )
```


Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

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

Registrera modellen på din arbets yta med följande kod. Genom att ange parametrarna `model_framework` , `model_framework_version` , och `resource_configuration` , blir modell distribution utan kod tillgängligt. På så sätt kan du distribuera din modell direkt som en webb tjänst från den registrerade modellen, och [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) objektet definierar beräknings resursen för webb tjänsten.

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
