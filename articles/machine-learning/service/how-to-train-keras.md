---
title: Träna djup inlärning neurala Network med keras
titleSuffix: Azure Machine Learning service
description: Lär dig att träna och registrera en keras djup neurala nätverks klassificerings modell som körs på TensorFlow med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: b4f2bf5c762b6f93c2d23714d860e8261f6756d1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847692"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning-service"></a>Träna och registrera en keras klassificerings modell med Azure Machine Learning tjänsten

Den här artikeln visar hur du tränar och registrerar en keras-klassificerings modell som bygger på TensorFlow med hjälp av Azure Machine Learning-tjänsten. Den använder den populära [MNIST](http://yann.lecun.com/exdb/mnist/) -datauppsättningen för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN) som skapats med det [keras python-biblioteket](https://keras.io) som körs ovanpå [TensorFlow](https://www.tensorflow.org/overview).

Keras är en neurala nätverks-API på hög nivå som kan köra andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning tjänsten kan du snabbt skala ut utbildnings jobb med elastiska moln beräknings resurser. Du kan också spåra dina utbildnings körningar, versions modeller, distribuera modeller och mycket mer.

Oavsett om du utvecklar en keras-modell från grunden eller om du använder en befintlig modell i molnet kan Azure Machine Learning tjänsten hjälpa dig att skapa produktions klara modeller.

I den [konceptuella artikeln](concept-deep-learning-vs-machine-learning.md) finns information om skillnaderna mellan maskin inlärning och djup inlärning.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:

 - Azure Machine Learning Notebook VM – inga hämtningar eller installationer behövs

     - Slutför [Självstudie: Konfigurera miljö och arbets](tutorial-1st-experiment-sdk-setup.md) yta för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen Samples på Notebook-servern letar du reda på en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > utbildning-with-djupgående-learning > träna-keras parameter-Tune-Deploy-with-** Folder.

 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` och`utils.py`

    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

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

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment och en mapp för att lagra dina utbildnings skript. I det här exemplet skapar du ett experiment med namnet "keras-mnist".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Ladda upp data uppsättning och skript

Data [lagret](how-to-access-data.md) är en plats där data kan lagras och nås genom att montera eller kopiera data till beräknings målet. Varje arbets yta tillhandahåller ett standard data lager. Överför data och utbildnings skript till data lagret så att de enkelt kan nås under utbildningen.

1. Ladda ned MNIST-datauppsättningen lokalt.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Överför MNIST-datauppsättningen till standard data lagret.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Ladda upp utbildnings skriptet `keras_mnist.py`keras, och hjälp `utils.py`filen.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräknings mål för ditt TensorFlow-jobb som ska köras. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräknings kluster.

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

Mer information om beräknings mål finns i artikeln [Vad är en Compute Target](concept-compute-target.md) -artikel.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Skapa en TensorFlow-uppskattning och importera keras

[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) -uppskattningen ger ett enkelt sätt att starta TensorFlow utbildnings jobb på beräknings mål. Eftersom keras körs ovanpå TensorFlow kan du använda TensorFlow-uppskattningen och importera keras-biblioteket med `pip_packages` argumentet.

TensorFlow-uppskattningen implementeras via den generiska [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen, som kan användas för att stödja eventuella ramverk. Skapa dessutom en ord lista `script_params` som innehåller inställningarna för DNN. Mer information om tränings modeller med hjälp av den generiska uppskattningen finns i [träna modeller med Azure Machine Learning med hjälp av uppskattning](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereder**: En Docker-avbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI klustret kräver fler noder för att köra körningen än vad som är tillgängligt.

- **Körning**: Alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen./outputs för körningen kopieras till körnings historiken.

## <a name="register-the-model"></a>Registrera modellen

När du har tränat DNN-modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Du kan också hämta en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modell validerings arbete lokalt. I övnings skriptet `mnist-keras.py`sparar ett TensorFlow-sparfunktionen modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia från data lagret.

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

I den här artikeln har du tränat och registrerat en keras-modell på Azure Machine Learning-tjänsten. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår modell distributions artikel.

> [!div class="nextstepaction"]
> [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
