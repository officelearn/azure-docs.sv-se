---
title: Träna och distribuera en TensorFlow-modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör TensorFlow-utbildningsskript i stor skala med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228314"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Skapa en TensorFlow-modell för djupinlärning i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du kör dina [TensorFlow-utbildningsskript](https://www.tensorflow.org/overview) i stor skala med Azure Machine Learnings [TensorFlow-uppskattningsklass.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Det här exemplet tränar och registrerar en TensorFlow-modell för att klassificera handskrivna siffror med hjälp av ett djupt neuralt nätverk (DNN).

Oavsett om du utvecklar en TensorFlow-modell från grunden eller om du tar in en [befintlig modell](how-to-deploy-existing-model.md) i molnet kan du använda Azure Machine Learning för att skala ut utbildningsjobb med öppen källkod för att skapa, distribuera, versionsa och övervaka modeller i produktionsklass.

Läs mer om [djupinlärning vs maskininlärning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Krav

Kör den här koden på någon av dessa miljöer:

 - Azure Machine Learning-beräkningsinstans – inga hämtningar eller installation nödvändiga

     - Slutför [självstudiekursen: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad anteckningsboksserver som är förinstallerad med SDK och exempeldatabasen.
    - I mappen med djupinlärning i exemplen på den bärbara servern hittar du en slutförd och utökad anteckningsbok genom att navigera till den här katalogen: **hur du ska använda azureml-> ml-ramverk > tensorflow >-distribution > tåg-hyperparameter-tune-deploy-with-tensorflow-mapp.** 
 
 - Din egen Jupyter Notebook server

    - [Installera SDK för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
    - [Ladda ned exempelskriptfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` och`utils.py`
     
    Du kan också hitta en ifylld [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) av den här guiden på GitHub exempelsidan. Anteckningsboken innehåller utökade avsnitt som täcker intelligent hyperparameterjustering, modelldistribution och widgetar för bärbara datorer.

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
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

[Arbetsytan Azure Machine Learning](concept-workspace.md) är resursen på den högsta nivån för tjänsten. Det ger dig en centraliserad plats att arbeta med alla artefakter du skapar. I Python SDK kan du komma åt arbetsytans [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefakter genom att skapa ett objekt.

Skapa ett arbetsyteobjekt från `config.json` filen som [skapats](#prerequisites)i avsnittet förutsättningar .

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djupinlärningsexperiment

Skapa ett experiment och en mapp för att hålla dina träningsskript. Skapa i det här exemplet ett experiment som kallas "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

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

# list the files referenced by dataset
dataset.to_path()
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

## <a name="create-a-tensorflow-estimator"></a>Skapa en TensorFlow-uppskattning

[TensorFlow-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) är ett enkelt sätt att starta ett TensorFlow-utbildningsjobb på ett beräkningsmål.

TensorFlow-uppskattningen implementeras via den [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) allmänna klassen, som kan användas för att stödja alla ramverk. Mer information om träningsmodeller med hjälp av den allmänna uppskattningen finns i [tågmodeller med Azure Machine Learning med uppskattning](how-to-train-ml-models.md)

Om ditt utbildningsskript behöver ytterligare pip- eller conda-paket för att köras kan du ha `pip_packages` paketen installerade på den resulterande Docker-avbildningen genom att skicka deras namn genom argumenten och. `conda_packages`

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Stöd för **Tensorflow 2.0** har lagts till i klassen Tensorflow estimator. Se [blogginlägget](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) för mer information.

Mer information om hur du anpassar pythonmiljön finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Skicka en körning

[Run-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körningshistoriken medan jobbet körs och efter att det har slutförts.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereda**: En Docker-avbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbetsytans behållarregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI-klustret kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Kör:** Alla skript i skriptmappen överförs till beräkningsmålet, datalager monteras eller kopieras och entry_script körs. Utdata från stdout och mappen ./logs strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen ./output i körningen kopieras över till körningshistoriken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ner en modell

När du har tränat modellen kan du registrera den på din arbetsyta. Med modellregistrering kan du lagra och modellera dina modeller på arbetsytan för att förenkla [modellhantering och distribution](concept-model-management-and-deployment.md). Genom att ange `model_framework` `model_framework_version`parametrarna `resource_configuration`blir och , no-code-modelldistributionen tillgänglig. På så sätt kan du distribuera modellen direkt som en `ResourceConfiguration` webbtjänst från den registrerade modellen och objektet definierar beräkningsresursen för webbtjänsten.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Du kan också hämta en lokal kopia av modellen med hjälp av run-objektet. I utbildningsskriptet `mnist-tf.py`behåller ett TensorFlow-sparare objekt modellen till en lokal mapp (lokal till beräkningsmålet). Du kan använda run-objektet för att hämta en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuerad träning

Kalkylatorn [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) stöder också distribuerad utbildning över CPU- och GPU-kluster. Du kan enkelt köra distribuerade TensorFlow-jobb och Azure Machine Learning hanterar orkestrering för dig.

Azure Machine Learning stöder två metoder för distribuerad utbildning i TensorFlow:

- [MPI-baserad](https://www.open-mpi.org/) distribuerad utbildning med [horovod-ramen](https://github.com/uber/horovod)
- [Inbyggt distribuerat TensorFlow](https://www.tensorflow.org/deploy/distributed) med parameterservermetoden

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) är ett ramverk med öppen källkod för distribuerad utbildning som utvecklats av Uber. Det erbjuder en enkel väg till distribuerade GPU TensorFlow jobb.

Om du vill använda [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) Horovod `distributed_training` anger du ett objekt för parametern i TensorFlow-konstruktorn. Den här parametern säkerställer att Horovod-biblioteket är installerat så att du kan använda i ditt träningsskript.

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
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Parameterserver

Du kan också köra [inbyggt distribuerat TensorFlow](https://www.tensorflow.org/deploy/distributed), som använder parameterservermodellen. I den här metoden tränar du över ett kluster av parameterservrar och arbetare. Arbetarna beräknar övertoningarna under utbildningen, medan parameterservrarna sammanställer övertoningarna.

Om du vill använda parameterservermetoden anger du ett [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objekt för parametern `distributed_training` i TensorFlow-konstruktorn.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definiera klusterspecifikationer i "TF_CONFIG"

Du behöver också nätverksadresser och portar för [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)klustret `TF_CONFIG` för , så Azure Machine Learning anger miljövariabeln för dig.

Miljövariabeln `TF_CONFIG` är en JSON-sträng. Här är ett exempel på variabeln för en parameterserver:

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

För TensorFlows API [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) på hög nivå tolkar `TF_CONFIG` TensorFlow variabeln och skapar klusterspecifikationen åt dig.

För TensorFlows lågnivå-kärn-API:er för `TF_CONFIG` utbildning tolkar `tf.train.ClusterSpec` du variabeln och bygger in din träningskod.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Distribuera en TensorFlow-modell

Modellen du just registrerade kan distribueras på exakt samma sätt som alla andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning du använde för utbildning. Distributionen innehåller ett avsnitt om hur du registrerar modeller, men du kan hoppa direkt till att [skapa ett beräkningsmål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

## <a name="preview-no-code-model-deployment"></a>(Förhandsgranska) Distribution av no-code-modell

I stället för den traditionella distributionsvägen kan du också använda distributionsfunktionen utan kod (förhandsversion) för Tensorflow. Genom att registrera din modell `model_framework`som `model_framework_version`visas `resource_configuration` ovan med , `deploy()` och parametrar, kan du helt enkelt använda den statiska funktionen för att distribuera din modell.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Den fullständiga [how-to täcker](how-to-deploy-and-where.md) distributionen i Azure Machine Learning på djupet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en TensorFlow-modell och lärt dig mer om alternativ för distribution. Se de här andra artiklarna om du vill veta mer om Azure Machine Learning.

* [Spåra körningsmått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Referensarkitektur för distribuerad djupinlärningsutbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
