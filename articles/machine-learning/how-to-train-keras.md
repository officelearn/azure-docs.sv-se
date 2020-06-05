---
title: Träna djup inlärning keras-modeller
titleSuffix: Azure Machine Learning
description: Lär dig att träna och registrera en keras djup neurala nätverks klassificerings modell som körs på TensorFlow med hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 14649d3e7bc12205283863f725a902a3cef20290
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433854"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Träna och registrera en keras klassificerings modell med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du tränar och registrerar en keras-klassificerings modell som bygger på TensorFlow med hjälp av Azure Machine Learning. Den använder den populära [MNIST-datauppsättningen](http://yann.lecun.com/exdb/mnist/) för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN) som skapats med det [keras python-biblioteket](https://keras.io) som körs ovanpå [TensorFlow](https://www.tensorflow.org/overview).

Keras är en neurala nätverks-API på hög nivå som kan köra andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning kan du snabbt skala ut utbildnings jobb med elastiska moln beräknings resurser. Du kan också spåra dina utbildnings körningar, versions modeller, distribuera modeller och mycket mer.

Oavsett om du utvecklar en keras-modell från grunden eller om du har en befintlig modell i molnet kan Azure Machine Learning hjälpa dig att skapa produktions klara modeller.

I den [konceptuella artikeln](concept-deep-learning-vs-machine-learning.md) finns information om skillnaderna mellan maskin inlärning och djup inlärning.

## <a name="prerequisites"></a>Krav

Kör den här koden i någon av följande miljöer:

- Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

     - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen Samples på Notebook-servern letar du reda på en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > utbildning-with-djupgående-learning > träna-keras parameter-Tune-Deploy-with-** Folder.

 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` särskilt`utils.py`

    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

```Python
import os
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

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment med namnet "keras-mnist" i din arbets yta.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Skapa en fil data uppsättning

Ett `FileDataset` objekt refererar till en eller flera filer i data lagret för din arbets yta eller offentliga URL: er. Filerna kan vara i valfritt format, och klassen ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Genom att skapa en `FileDataset` skapar du en referens till data käll platsen. Om du har tillämpat eventuella omvandlingar till data uppsättningen lagras de även i data uppsättningen. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår. Mer information finns i [instruktions](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) guiden `Dataset` för.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Använd `register()` metoden för att registrera data uppsättningen på din arbets yta så att de kan delas med andra, återanvändas över olika experiment och refereras till av namn i ditt utbildnings skript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Skapa ett beräknings mål

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

[TensorFlow-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) ger ett enkelt sätt att starta TensorFlow utbildnings jobb på beräknings mål. Eftersom keras körs ovanpå TensorFlow kan du använda TensorFlow-uppskattningen och importera keras-biblioteket med `pip_packages` argumentet.

Hämta först data från data lagrets data lager med hjälp av `Dataset` klassen.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow-uppskattningen implementeras via den generiska [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen, som kan användas för att stödja eventuella ramverk. Skapa dessutom en ord lista `script_params` som innehåller inställningarna för DNN. Mer information om tränings modeller med hjälp av den generiska uppskattningen finns i [träna modeller med Azure Machine Learning med hjälp av uppskattning](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

- **Förbereder**: en Docker-avbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen./outputs i körningen kopieras till körnings historiken.

## <a name="register-the-model"></a>Registrera modellen

När du har tränat DNN-modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Den modell som du precis har registrerat har distribuerats exakt på samma sätt som andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning som du använde för utbildning. Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också hämta en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modell validerings arbete lokalt. I övnings skriptet `mnist-keras.py` sparar ett TensorFlow-sparfunktionen modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia från data lagret.

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

I den här artikeln har du tränat och registrerat en keras-modell på Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår modell distributions artikel.

> [!div class="nextstepaction"]
> [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
