---
title: Träna maskin inlärnings modeller med scikit – lära
titleSuffix: Azure Machine Learning service
description: Lär dig hur du kör dina scikit-utbildnings skript i företags skala med hjälp av Azure Machine Learning SKlearn-uppskattnings klass. Exempel skripten klassificerar Iris blomma-bilder för att bygga en maskin inlärnings modell baserad på scikit-data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 98c04c50bc4a52e9b2e4e267895fdd94888885f5
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775154"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Bygg scikit – lär dig modeller i stor skala med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du kör dina scikit i företags skala med hjälp av Azure Machine Learning [SKlearn uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klass. 

Exempel skripten i den här artikeln används för att klassificera Iris blomma-bilder för att bygga en maskin inlärnings modell baserad på scikit- [data uppsättningen](https://archive.ics.uci.edu/ml/datasets/iris).

Oavsett om du tränar en Machine Learning-scikit – lär dig modell från grunden eller om du använder en befintlig modell i molnet, kan du använda Azure Machine Learning för att skala ut utbildnings jobb med öppen källkod med elastiska moln beräknings resurser. Du kan bygga, distribuera, hantera och övervaka modeller av produktions klass med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:
 - Azure Machine Learning Notebook VM – inga hämtningar eller installationer behövs

    - Slutför [Självstudie: Konfigurera miljö och arbets](tutorial-1st-experiment-sdk-setup.md) yta för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel utbildning på Notebook-servern hittar du en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > training > Train-y-Deploy-with-sklearn** Folder.

 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK för python](setup-create-workspace.md#sdk)
    - [Skapa en konfigurations fil för arbets ytor](setup-create-workspace.md#write-a-configuration-file)
    - Hämta data uppsättningen och exempel skript filen 
        - [Iris-datauppsättning](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller ett expanderat avsnitt som täcker intelligenta parametrar för att justera och hämta den bästa modellen med primära mått.

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

Den [Azure Machine Learning service-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) ett objekt.

Skapa ett objekt för arbets ytan `config.json` från filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Skapa ett Machine Learning-experiment

Skapa ett experiment och en mapp för att lagra dina utbildnings skript. I det här exemplet skapar du ett experiment med namnet "sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Ladda upp data uppsättning och skript

Data [lagret](how-to-access-data.md) är en plats där data kan lagras och nås genom att montera eller kopiera data till beräknings målet. Varje arbets yta tillhandahåller ett standard data lager. Överför data och utbildnings skript till data lagret så att de enkelt kan nås under utbildningen.

1. Skapa katalogen för dina data.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Överför Iris-datauppsättningen till standard data lagret.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Ladda upp utbildnings skriptet scikit-lär, `train_iris.py`.

    ```Python
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

[Scikit-lär dig uppskattnings](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) tjänsten ger ett enkelt sätt att starta ett scikit utbildnings jobb på ett beräknings mål. Den implementeras via [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klassen, som kan användas för att stödja CPU-utbildning med en nod.

Om ditt utbildnings skript behöver ytterligare pip-eller Conda-paket för att kunna köras, kan du ha paketen installerade på den resulterande Docker-avbildningen `conda_packages` genom att `pip_packages` skicka namnen via argumenten och.

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

## <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereder**: En Docker-avbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI klustret kräver fler noder för att köra körningen än vad som är tillgängligt.

- **Körning**: Alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen./outputs för körningen kopieras till körnings historiken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen kan du spara och registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

Lägg till följande kod i utbildnings skriptet, train_iris. py, för att spara modellen. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrera modellen på din arbets yta med följande kod.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en maskin inlärning, klassificerings modell med scikit-information om Azure Machine Learning-tjänsten.

* Om du vill lära dig hur du distribuerar en modell fortsätter du till vår [modell distributions](how-to-deploy-and-where.md) artikel.

* [Justera disponeringsparametrarna](how-to-tune-hyperparameters.md).

* [Spåra körnings mått under träning](how-to-track-experiments.md).

* Lär dig mer om [djup inlärning vs Machine Learning](concept-deep-learning-vs-machine-learning.md).