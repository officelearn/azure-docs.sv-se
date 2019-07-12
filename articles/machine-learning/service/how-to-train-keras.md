---
title: Träna och registrera Keras modeller som körs på TensorFlow
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du tränar och registrera en Keras-modell som körs på TensorFlow med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840053"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Träna och registrera Keras modeller i hög skala med Azure Machine Learning-tjänsten

Den här artikeln visar hur du tränar och registrera en Keras-modell som bygger på TensorFlow med hjälp av Azure Machine Learning-tjänsten. Den använder de populära [MNIST datauppsättning](http://yann.lecun.com/exdb/mnist/) att klassificera handskriven siffror med hjälp av ett djupa neurala nätverk (DNN) som skapats med den [Keras Python-bibliotek](https://keras.io) som körs ovanpå [TensorFlow](https://www.tensorflow.org/overview) .

Keras är ett övergripande neurala nätverk API kan köra upp i andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning-tjänsten kan du snabbt skala ut utbildningsjobb med hjälp av elastisk molnberäkningsresurser. Du kan också spåra ditt träningskörningar, version modeller, distribuera modeller och mycket mer.

Om du utvecklar en modell för Keras från grunden eller du behöver en befintlig modell i molnet, Azure Machine Learning-tjänsten kan hjälpa dig att skapa modeller för produktionsklart.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden på något av dessa miljöer:

 - Azure Machine Learning Notebook VM - inga nedladdningar eller installeras

     - Slutför den [molnbaserade notebook Snabbstart](quickstart-run-cloud-notebook.md) att skapa en dedikerad anteckningsboksserver förinstallerade med SDK: N och exempellagringsplatsen.
    - I mappen samples på notebook-server kan du hitta en slutförd och utökade anteckningsbok genom att navigera till den här katalogen: **How-to-till-användning – azureml > utbildning med deep learning > train-hyperparameter-tune-deploy-with-keras** mappen. 
 
 - En egen Jupyter Notebook-server

     - [Installera Azure Machine Learning SDK för Python](setup-create-workspace.md#sdk)
    - [Skapa en konfigurationsfil för arbetsyta](setup-create-workspace.md#write-a-configuration-file)
    - [Ladda ned skriptet exempelfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` och `utils.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) i den här guiden på sidan med kodexempel på GitHub. Den bärbara datorn innehåller utökade avsnitt som täcker intelligent finjustering av hyperparametrar, distribution av modeller och notebook widgetar.

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

Skapa ett experiment och en mapp för att lagra dina utbildningsskript. I det här exemplet skapar du ett experiment som kallas ”keras-mnist”.

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Ladda upp datauppsättningen och skript

Den [datalager](how-to-access-data.md) är en plats där data kan lagras och nås genom att montera eller kopiera data till beräkningsmål-. Varje arbetsyta innehåller ett standard-datalager. Ladda upp data och utbildningsskript till databasen så att de enkelt kan nås vid träning.

1. Hämta MNIST datauppsättningen lokalt.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Ladda upp MNIST datauppsättningen till standard-databasen.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Ladda upp inlärningsskript Keras `keras_mnist.py`, och filen helper `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål för TensorFlow jobbet ska köras på. I det här exemplet skapar du ett GPU-aktiverade Azure Machine Learning-beräkningskluster.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Mer information om beräkningsmål finns i den [vad är ett beräkningsmål](concept-compute-target.md) artikeln.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Skapa en kostnadsuppskattning TensorFlow och importera Keras

Den [TensorFlow kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) ger ett enkelt sätt att starta jobb för TensorFlow-utbildning på beräkningsmål. Eftersom Keras körs ovanpå TensorFlow, du kan använda TensorFlow-kostnadsuppskattning och importera den Keras bibliotek med hjälp av den `pip_packages` argumentet.

TensorFlow-kostnadsuppskattning implementeras via ett allmänt [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klass, som kan användas för att stödja valfritt ramverk. Läs mer om träna modeller med hjälp av generiska kostnadsuppskattning [träna modeller med Azure Machine Learning med hjälp av kostnadsuppskattning](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Skicka en körning

Den [köra objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnitt för körningshistoriken medan jobbet körs och när den är slutförd.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Eftersom körningen körs passerar i följande steg:

- **Förbereda**: En docker-avbildning skapas enligt TensorFlow-kostnadsuppskattning. Avbildningen överförs till den arbetsytan container registry och cachelagras för senare körningar. Loggarna strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker att skala upp om Batch AI-kluster kräver fler noder att köra körningen än vad som är tillgängliga.

- **Körning**: Alla skript i mappen skript överförs till beräkningsmål datalager monterad eller kopieras och entry_script körs. Utdata från stdout och. / loggkatalogen strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Det. / matar ut mappen för körningen kopieras till körningshistoriken.

## <a name="register-the-model"></a>Registrera modellen

När du har tränat modellen, kan du registrera den till din arbetsyta. Modellen kan du lagra och version dina modeller i din arbetsyta för att förenkla [modellera hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Du kan också hämta en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare verifiering lokalt arbete. I skriptet utbildning `mnist-keras.py`, ett TensorFlow skärmsläckare objekt kvarstår modellen till en lokal mapp (lokal till beräkningsmål-). Du kan använda objektet kör för att ladda ned en kopia från datalagret.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du tränas och registrerat en Keras-modell i Azure Machine Learning-tjänsten. Lär dig hur du distribuerar en modell, Fortsätt till vår artikel för distribution av modellen.

> [!div class="nextstepaction"]
> [Hur och var du vill distribuera modeller](how-to-deploy-and-where.md)
