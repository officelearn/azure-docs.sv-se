---
title: Utbilda ett djup inlärnings neurala nätverk med TensorFlow
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör TensorFlow-tränings skript i skala med hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 3b8f213bd614e4adce74b83c87649a0f248cba7b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710094"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Bygg en TensorFlow djup inlärnings modell i skala med Azure Machine Learning

Den här artikeln visar hur du kör dina [TensorFlow](https://www.tensorflow.org/overview) -utbildnings skript i stor skala med Azure Machine Learning [TensorFlow-uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) klass. Detta exempel tågen och registrerar en TensorFlow modell för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN).

Oavsett om du utvecklar en TensorFlow-modell från grunden eller om du använder en [befintlig modell](how-to-deploy-existing-model.md) i molnet, kan du använda Azure Machine Learning för att skala ut utbildnings jobb med öppen källkod för att bygga, distribuera, skapa och övervaka modeller för produktions klass .

Lär dig mer om [djup inlärning vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:

 - Azure Machine Learning Notebook VM – inga hämtningar eller installationer behövs

     - Slutför [Självstudie: Konfigurera miljö och arbets](tutorial-1st-experiment-sdk-setup.md) yta för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel djup inlärning på Notebook-servern hittar du en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > tensorflow > distribution > träna-en parameter-finjustera-distribuera-with-tensorflow-** mapp. 
 
 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` och`utils.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

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

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) ett objekt.

Skapa ett objekt för arbets ytan `config.json` från filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djup inlärnings experiment

Skapa ett experiment och en mapp för att lagra dina utbildnings skript. I det här exemplet skapar du ett experiment som kallas "TF-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Skapa en fil data uppsättning

Ett `FileDataset` objekt refererar till en eller flera filer i data lagret för din arbets yta eller offentliga URL: er. Filerna kan vara i valfritt format, och klassen ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Genom att skapa `FileDataset`en skapar du en referens till data käll platsen. Om du har tillämpat eventuella omvandlingar till data uppsättningen lagras de även i data uppsättningen. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår. Mer information finns i [instruktions](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) guiden `Dataset` för.

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

`register()` Använd metoden för att registrera data uppsättningen på din arbets yta så att de kan delas med andra, återanvändas över olika experiment och refereras till av namn i ditt utbildnings skript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
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

## <a name="create-a-tensorflow-estimator"></a>Skapa en TensorFlow-uppskattning

[TensorFlow-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) ger ett enkelt sätt att starta ett TensorFlow utbildnings jobb på ett beräknings mål.

TensorFlow-uppskattningen implementeras via den generiska [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen, som kan användas för att stödja eventuella ramverk. Mer information om tränings modeller med hjälp av den generiska uppskattningen finns i [träna modeller med Azure Machine Learning med hjälp av uppskattning](how-to-train-ml-models.md)

Om ditt utbildnings skript behöver ytterligare pip-eller Conda-paket för att kunna köras, kan du ha paketen installerade på den resulterande Docker-avbildningen `conda_packages` genom att `pip_packages` skicka namnen via argumenten och.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
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

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Du kan också hämta en lokal kopia av modellen med hjälp av objektet kör. I övnings skriptet `mnist-tf.py`behåller ett TensorFlow-sparfunktionen modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuerad utbildning

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Uppskattningen stöder också distribuerad utbildning mellan processor-och GPU-kluster. Du kan enkelt köra distribuerade TensorFlow-jobb och Azure Machine Learning hanterar dirigeringen åt dig.

Azure Machine Learning stöder två metoder för distribuerad utbildning i TensorFlow:

- [MPI-baserad](https://www.open-mpi.org/) distribuerad utbildning med [Horovod](https://github.com/uber/horovod) -ramverket
- Inbyggda [distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed) med hjälp av parameter Server metoden

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) är ett ramverk med öppen källkod för distribuerad utbildning som utvecklas av Uber. Den erbjuder en enkel väg till distribuerade GPU TensorFlow-jobb.

Om du vill använda Horovod anger [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) du ett objekt `distributed_training` för parametern i TensorFlow-konstruktorn. Den här parametern säkerställer att Horovod-biblioteket installeras för användning i ditt utbildnings skript.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Parameterserver

Du kan också köra [intern distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed), som använder parametern server-modell. I den här metoden kan träna du i ett kluster med parametern-servrar och arbetare. ”Arbetarna” beräkna toningar vid träning, även om parametern servrar aggregera på toningar.

Om du vill använda parameter Server metoden anger du [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) ett objekt `distributed_training` för parametern i TensorFlow-konstruktorn.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definiera kluster specifikationer i "TF_CONFIG"

Du behöver också nätverks adresser och portar för klustret för [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), så Azure Machine Learning `TF_CONFIG` ställer in miljövariabeln åt dig.

Den `TF_CONFIG` miljövariabeln är en JSON-sträng. Här är ett exempel på variabeln för en parameterserver:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

För TensorFlow på hög nivå [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) , parsar `TF_CONFIG` TensorFlow variabeln och skapar kluster specifikationen åt dig.

Parsa `TF_CONFIG` variabeln och `tf.train.ClusterSpec` utveckla i din utbildnings kod för TensorFlow på lägre nivå för grundläggande API: er för utbildning.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en TensorFlow-modell. Om du vill lära dig hur du distribuerar en modell till ett GPU-aktiverat kluster kan du fortsätta till vår distributions artikel för GPU-modellen.

> [!div class="nextstepaction"]
> [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
