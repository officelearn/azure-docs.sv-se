---
title: Träna djupinlärning keras modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och registrerar en Keras deep neurala nätverksklassificeringsmodell som körs på TensorFlow med Hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269970"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Träna och registrera en Keras-klassificeringsmodell med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du tränar och registrerar en Keras-klassificeringsmodell som bygger på TensorFlow med Azure Machine Learning. Den använder den populära [MNIST-datauppsättningen](http://yann.lecun.com/exdb/mnist/) för att klassificera handskrivna siffror med hjälp av ett djupt neuralt nätverk (DNN) byggt med [Keras Python-biblioteket](https://keras.io) som körs ovanpå [TensorFlow](https://www.tensorflow.org/overview).

Keras är ett neuralt nätverks-API på hög nivå som kan köra överst i andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning kan du snabbt skala ut utbildningsjobb med elastiska molnberäkningsresurser. Du kan också spåra dina utbildningskörningar, versionsmodeller, distribuera modeller och mycket mer.

Oavsett om du utvecklar en Keras-modell från grunden eller om du tar med en befintlig modell i molnet kan Azure Machine Learning hjälpa dig att skapa produktionsklara modeller.

Se den [konceptuella artikeln](concept-deep-learning-vs-machine-learning.md) för information om skillnaderna mellan maskininlärning och djupinlärning.

## <a name="prerequisites"></a>Krav

Kör den här koden på någon av dessa miljöer:

- Azure Machine Learning-beräkningsinstans – inga hämtningar eller installation nödvändiga

     - Slutför [självstudiekursen: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad anteckningsboksserver som är förinstallerad med SDK och exempeldatabasen.
    - I exempelmappen på den bärbara servern hittar du en slutförd och utökad anteckningsbok genom att navigera till den här katalogen: **hur du ska använda azureml > utbildning med-djupinlärning > tåg-hyperparameter-tune-deploy-with-keras-mapp.**

 - Din egen Jupyter Notebook server

    - [Installera SDK för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
    - [Ladda ned exempelskriptfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` och`utils.py`

    Du kan också hitta en ifylld [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) av den här guiden på GitHub exempelsidan. Anteckningsboken innehåller utökade avsnitt som täcker intelligent hyperparameterjustering, modelldistribution och widgetar för bärbara datorer.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet ställer in utbildningsexperimentet genom att läsa in de python-paket som krävs, initiera en arbetsyta, skapa ett experiment och ladda upp träningsdata och utbildningsskript.

### <a name="import-packages"></a>Importera paket

Importera först nödvändiga Python-bibliotek.

```Python
import os
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

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment som kallas "keras-mnist" på arbetsytan.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Skapa en fildatauppsättning

Ett `FileDataset` objekt refererar till en eller flera filer i din arbetsytedatalager eller offentliga webbadresser. Filerna kan vara av vilket format som helst, och klassen ger dig möjlighet att ladda ner eller montera filerna till din beräkning. Genom att `FileDataset`skapa en skapar du en referens till datakällans plats. Om du har använt några omvandlingar på datauppsättningen lagras de även i datauppsättningen. Data finns kvar på den befintliga platsen, så ingen extra lagringskostnad uppstår. Mer information finns i `Dataset` hur du [guidar](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) på paketet.

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

Använd `register()` metoden för att registrera datauppsättningen på arbetsytan så att de kan delas med andra, återanvändas över olika experiment och refereras till med namn i utbildningsskriptet.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål för TensorFlow-jobbet som ska köras på. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning-beräkningskluster.

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

Mer information om beräkningsmål finns i [vad som är en beräkningsmålsartikel.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Skapa en TensorFlow-uppskattning och importera Keras

[TensorFlow-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) är ett enkelt sätt att starta TensorFlow-utbildningsjobb på beräkningsmål. Eftersom Keras körs ovanpå TensorFlow kan du använda TensorFlow-kalkylatorn och `pip_packages` importera Keras-biblioteket med argumentet.

Hämta först data från arbetsytans datalager med hjälp av `Dataset` klassen.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow-uppskattningen implementeras via den [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) allmänna klassen, som kan användas för att stödja alla ramverk. Skapa dessutom en `script_params` ordlista som innehåller DNN-hyperparameterinställningarna. Mer information om träningsmodeller med hjälp av den allmänna uppskattningen finns i [tågmodeller med Azure Machine Learning med uppskattning](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Skicka en körning

[Run-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körningshistoriken medan jobbet körs och efter att det har slutförts.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereda**: En dockeravbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbetsytans behållarregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI-klustret kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Kör:** Alla skript i skriptmappen överförs till beräkningsmålet, datalager monteras eller kopieras och entry_script körs. Utdata från stdout och mappen ./logs strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen ./output i körningen kopieras över till körningshistoriken.

## <a name="register-the-model"></a>Registrera modellen

När du har tränat DNN-modellen kan du registrera den på din arbetsyta. Med modellregistrering kan du lagra och modellera dina modeller på arbetsytan för att förenkla [modellhantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Modellen du just registrerade distribueras på exakt samma sätt som alla andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning du använde för utbildning. Distributionen innehåller ett avsnitt om hur du registrerar modeller, men du kan hoppa direkt till att [skapa ett beräkningsmål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också ladda ner en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modellvalideringsarbete lokalt. I utbildningsskriptet `mnist-keras.py`beständigs modellen för ett TensorFlow-sparobjekt i en lokal mapp (lokal till beräkningsmålet). Du kan använda run-objektet för att hämta en kopia från datalager.

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

I den här artikeln har du tränat och registrerat en Keras-modell på Azure Machine Learning. Om du vill veta hur du distribuerar en modell fortsätter du till vår artikel om modelldistribution.

> [!div class="nextstepaction"]
> [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körningsmått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referensarkitektur för distribuerad djupinlärningsutbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
