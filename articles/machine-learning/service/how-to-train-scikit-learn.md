---
title: Träna och registrera scikit-Läs modeller
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du tränar och registrera en scikit-Läs modell med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: d2c9b104d1fe9333221bc20e7e23b436358c9ece
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514017"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Träna och registrera Scikit-Läs modeller i hög skala med Azure Machine Learning-tjänsten

Den här artikeln visar hur du tränar och registrerar en Scikit-Läs modell med hjälp av Azure Machine Learning-tjänsten. Den använder de populära [Iris-datauppsättningen](https://archive.ics.uci.edu/ml/datasets/iris) att klassificera iris blommor bilder med anpassat [scikit-Läs](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klass.

Scikit-Läs är en öppen källkod databaserad ramverk som ofta används för machine learning. Med Azure Machine Learning-tjänsten kan du snabbt skala ut öppen källkod-utbildningsjobb med hjälp av elastisk molnberäkningsresurser. Du kan också spåra ditt träningskörningar, version modeller, distribuera modeller och mycket mer.

Om du utvecklar en Scikit-Läs modell från grunden eller du behöver en befintlig modell i molnet, Azure Machine Learning-tjänsten kan hjälpa dig att skapa modeller för produktionsklart.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden på något av dessa miljöer:
 - Azure Machine Learning Notebook VM - inga nedladdningar eller installeras

    - Slutför den [molnbaserade notebook Snabbstart](quickstart-run-cloud-notebook.md) att skapa en dedikerad anteckningsboksserver förinstallerade med SDK: N och exempellagringsplatsen.
    - I mappen samples på notebook-server kan du hitta en slutförd och utökade anteckningsbok genom att navigera till den här katalogen: **How-to-till-användning – azureml > utbildning > train-hyperparameter-tune-deploy-with-sklearn** mapp.

 - En egen Jupyter Notebook-server

    - [Installera Azure Machine Learning SDK för Python](setup-create-workspace.md#sdk)
    - [Skapa en konfigurationsfil för arbetsyta](setup-create-workspace.md#write-a-configuration-file)
    - [Hämta filen med skript](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) i den här guiden på sidan med kodexempel på GitHub. Den bärbara datorn innehåller ett expanderade avsnitt som täcker intelligent finjustering justera och hämta den bästa modellen med primära mått.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet konfigurerar träningsexperimentet av inläsning av nödvändiga python-paket, initierar en arbetsyta, skapa ett experiment och laddar upp den utbildningsinformationen och skripten för utbildning.

### <a name="import-packages"></a>Importera paket

Först importera de nödvändiga Python-biblioteken.

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

Den [Azure Machine Learning-tjänstens arbetsyta](concept-workspace.md) är den översta resursen för tjänsten. Den ger dig en centraliserad plats för att arbeta med alla artefakter som du skapar. I Python-SDK kan du komma åt arbetsytan artefakter genom att skapa en [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objekt.

Skapa en arbetsyta objekt från den `config.json` fil som skapats i den [krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment och en mapp för att lagra dina utbildningsskript. I det här exemplet skapar du ett experiment som kallas ”sklearn iris”.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Ladda upp datauppsättningen och skript

Den [datalager](how-to-access-data.md) är en plats där data kan lagras och nås genom att montera eller kopiera data till beräkningsmål-. Varje arbetsyta innehåller ett standard-datalager. Ladda upp data och utbildningsskript till databasen så att de enkelt kan nås vid träning.

1. Skapa katalog för dina data.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Ladda upp iris-datauppsättningen till standard-databasen.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Ladda upp Scikit-Läs utbildningsskript, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Skapa eller hämta ett beräkningsmål

Skapa ett beräkningsmål för Scikit-Läs jobbet ska köras på. Scikit Läs bara stöder nod, CPU databehandling.

Följande kod, skapar en Azure Machine Learning hanteras beräkning (AmlCompute) för remote utbildning beräkning. Skapandet av AmlCompute tar cirka 5 minuter. Om AmlCompute med det namnet finns redan i din arbetsyta, kommer den här koden hoppa över skapandeprocessen.

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

Mer information om beräkningsmål finns i den [vad är ett beräkningsmål](concept-compute-target.md) artikeln.

## <a name="create-a-scikit-learn-estimator"></a>Skapa en Scikit-Läs kostnadsuppskattning

Den [Scikit-Läs kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) ger ett enkelt sätt för att starta en Scikit-Läs-utbildningsjobb i ett beräkningsmål. Den implementeras via den [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) klass, som kan användas för att stödja nod CPU-utbildning.

Om dina utbildningsskript behöver ytterligare pip eller conda-paket för att köra, kan du använda de paket som är installerad på den resulterande docker-avbildningen genom att skicka deras namn via den `pip_packages` och `conda_packages` argument.

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

## <a name="submit-a-run"></a>Skicka en körning

Den [köra objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnitt för körningshistoriken medan jobbet körs och när den är slutförd.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Eftersom körningen körs passerar i följande steg:

- **Förbereda**: En docker-avbildning skapas enligt TensorFlow-kostnadsuppskattning. Avbildningen överförs till den arbetsytan container registry och cachelagras för senare körningar. Loggarna strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker att skala upp om Batch AI-kluster kräver fler noder att köra körningen än vad som är tillgängliga.

- **Körning**: Alla skript i mappen skript överförs till beräkningsmål datalager monterad eller kopieras och entry_script körs. Utdata från stdout och. / loggkatalogen strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Det. / matar ut mappen för körningen kopieras till körningshistoriken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen, kan du spara och registrera den till din arbetsyta. Modellen kan du lagra och version dina modeller i din arbetsyta för att förenkla [modellera hantering och distribution](concept-model-management-and-deployment.md).

Lägg till följande kod i skriptet för utbildning, train_iris.py modellen ska sparas. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrera modellen till din arbetsyta med följande kod.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du tränas och registrerat en Scikit-Läs modellen på Azure Machine Learning-tjänsten.

* Om du vill veta hur du distribuerar en modell kan du fortsätta till vår [modellera distribution](how-to-deploy-and-where.md) artikeln.

* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)

* [Spåra kör mått vid träning](how-to-track-experiments.md)