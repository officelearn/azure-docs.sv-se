---
title: Träna modeller med TensorFlow
titleSuffix: Azure Machine Learning service
description: Lär dig hur du kör en nod och distribuerad modeller TensorFlow-utbildning med TensorFlow-kostnadsuppskattning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d15d3ed115009ad1395a85d36e833d85197d4d19
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094123"
---
# <a name="train-tensorflow-models-with-azure-machine-learning-service"></a>Skapa TensorFlow-modeller med Azure Machine Learning-tjänsten

För djupa neurala (DNN) nätverksutbildning med TensorFlow, Azure Machine Learning ger en anpassad `TensorFlow` klassen för den `Estimator`. Azure SDK `TensorFlow` kostnadsuppskattning (inte för att vara conflated med den [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) klassen) kan du enkelt skicka TensorFlow-utbildningsjobb för både en nod och distribuerade körs på Azure compute.

## <a name="single-node-training"></a>Nod-utbildning
Utbildning med den `TensorFlow` kostnadsuppskattning är ungefär som att använda den [grundläggande `Estimator` ](how-to-train-ml-models.md), så först läsa igenom artikeln och kontrollera att du förstår begreppen som förklaras det.
  
Om du vill köra ett TensorFlow-jobb, skapa en instans av en `TensorFlow` objekt. Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#amlcompute) objektet `compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

Här anger vi följande parametrar till TensorFlow-konstruktorn:

Parameter | Beskrivning
--|--
`source_directory` | Lokal katalog som innehåller hela din kod som behövs för utbildningsjobbet. Den här mappen kopieras från din lokala dator till den fjärranslutna beräkningen
`script_params` | Ordlista att ange kommandoradsargument för att dina utbildningsskript `entry_script`, i form av < kommandoradsargumentet, värde > par
`compute_target` | Remote beräkningsmål som utbildning skriptet ska köras på, i det här fallet en Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) kluster
`entry_script` | FilePath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen
`conda_packages` | Lista över Python-paket installeras via conda som krävs för dina utbildningsskript. I det här fallet inlärningsskript använder `sklearn` för att läsa in data, så ange det här paketet installeras.  Konstruktorn har en annan parameter med namnet `pip_packages` som du kan använda några pip-paket som behövs
`use_gpu` | Anger denna flagga `True` utnyttja GPU för utbildning. Som standard `False`.

Eftersom du använder TensorFlow-kostnadsuppskattning behållaren som används för utbildning som standard innehåller TensorFlow-paketet och relaterade beroenden som krävs för utbildning om processorer och GPU: er.

Skicka TensorFlow-jobb:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Distribuerad utbildning
TensorFlow-Estimator kan du träna dina modeller i hög skala över processor- och GPU-kluster av virtuella Azure-datorer. Du kan enkelt köra distribuerad TensorFlow-utbildning med några API-anrop, medan Azure Machine Learning ska hantera i bakgrunden alla infrastruktur och dirigering som behövs för att utföra de här arbetsbelastningarna.

Azure Machine Learning stöder två metoder för distribuerad utbildning i TensorFlow:
* MPI-baserade distribuerade utbildning med hjälp av den [Horovod](https://github.com/uber/horovod) framework
* interna [distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed) via metoden parametern server

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är ett ramverk för öppen källkod ring-allreduce för distribuerad utbildning har utvecklats av Uber.

Om du vill köra distribuerade TensorFlow med Horovod framework, skapar du TensorFlow-objekt på följande sätt:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

Koden ovan visar följande nya parametrar för TensorFlow-konstruktorn:

Parameter | Beskrivning | Standard
--|--|--
`node_count` | Antalet noder som ska användas för utbildning-jobbet. | `1`
`process_count_per_node` | Antal processer (eller ”anställda”) för att köra på varje nod.|`1`
`distributed_backend` | Serverdelen för att starta distribuerade utbildning som kostnadsuppskattning erbjuder via MPI. Om du vill utföra parallella eller distribuerade utbildning (t.ex. `node_count`> 1 eller `process_count_per_node`> 1 eller båda) med MPI (och Horovod), ange `distributed_backend='mpi'`. MPI-implementering som används av Azure Machine Learning är [öppna MPI](https://www.open-mpi.org/). | `None`

Exemplet ovan körs distribuerad utbildning med två arbetsroller en arbetare per nod.

Horovod och dess beroenden kommer att installeras åt dig, så kan du helt enkelt importera det i dina utbildningsskript `train.py` på följande sätt:

```Python
import tensorflow as tf
import horovod
```

Slutligen skicka TensorFlow-jobb:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Parameterserver
Du kan också köra [intern distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed), som använder parametern server-modell. I den här metoden kan träna du i ett kluster med parametern-servrar och arbetare. ”Arbetarna” beräkna toningar vid träning, även om parametern servrar aggregera på toningar.

Konstruera TensorFlow-objekt:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Ta hänsyn till följande parametrar till TensorFlow-konstruktorn i koden ovan:

Parameter | Beskrivning | Standard
--|--|--
`worker_count` | Antal arbetare. | `1`
`parameter_server_count` | Antal servrar för parametern. | `1`
`distributed_backend` | Serverdel för distribuerad utbildning. Gör distribuerad utbildning via parameterserver, ange `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>Notera på `TF_CONFIG`
Du måste också nätverksadresser och portar i klustret för den [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), så att Azure Machine Learning anger den `TF_CONFIG` miljövariabeln åt dig.

Den `TF_CONFIG` miljövariabeln är en JSON-sträng. Här är ett exempel på variabeln för en parameterserver:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Om du använder TensorFlow är övergripande [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow parsa detta `TF_CONFIG` variabeln och skapa klustret-specifikationen för dig. 

Om du i stället använder Tensorflows på lägre nivå core API: er för träning, måste du parsa den `TF_CONFIG` variabeln och skapa den `tf.train.ClusterSpec` själv i din utbildning-kod. I [det här exemplet](https://aka.ms/aml-notebook-tf-ps), gör du så i **dina utbildningsskript** på följande sätt:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

När du är klar utbildning skriptet och skapa TensorFlow-objektet, du kan skicka din utbildningsjobb:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Exempel

Notebooks på distribuerade djupinlärning, finns här:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
