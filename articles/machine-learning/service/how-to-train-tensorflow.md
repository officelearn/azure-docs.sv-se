---
title: Skapa TensorFlow-modeller med Azure Machine Learning
description: Lär dig hur du kör en nod och distribuerad modeller TensorFlow-utbildning med TensorFlow-kostnadsuppskattning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ba43593e90b78aaa0083faf4f8162a7663c0ad47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974229"
---
# <a name="how-to-train-tensorflow-models"></a>Hur du tränar TensorFlow-modeller

För djupa neurala (DNN) nätverksutbildning med TensorFlow, erbjuder Azure Machine Learning en anpassad TensorFlow-klass för kostnadsuppskattning. Azure SDK TensorFlow kostnadsuppskattning (inte för att vara conflated med den [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) klassen) kan du enkelt skicka TensorFlow-utbildningsjobb för både en nod och distribuerade körs på Azure compute.

## <a name="single-node-training"></a>Nod-utbildning
Utbildning med TensorFlow-kostnadsuppskattning är ungefär som att använda den [basera kostnadsuppskattning](how-to-train-ml-models.md), så först läsa igenom artikeln och kontrollera att du förstår begreppen som förklaras det.
  
Om du vill köra ett TensorFlow-jobb, skapa en instans av en `TensorFlow` objekt. Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#batch) objektet `compute_target`.

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
* `source_directory`: Den lokala katalogen som innehåller hela din kod som behövs för utbildningsjobbet. Den här mappen kopieras från din lokala dator till den fjärranslutna beräkningen
* `script_params`: En ordlista som att ange kommandoradsargument för att dina utbildningsskript `entry_script`, i form av < kommandoradsargumentet, värde > par
* `compute_target`: Den fjärranslutna beräkning som utbildning skriptet ska köras på, i det här fallet en [Batch AI](how-to-set-up-training-targets.md#batch) kluster
* `entry_script`: Filepath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen
* `conda_packages`: Lista över Python-paket installeras via conda som krävs för dina utbildningsskript. I det här fallet inlärningsskript använder `sklearn` för att läsa in data, så ange det här paketet installeras.  
Konstruktorn har en annan parameter med namnet `pip_packages` som du kan använda några pip-paket som behövs
* `use_gpu`: Ställ in den här flaggan på `True` utnyttja GPU för utbildning. Som standard `False`.

Eftersom du använder TensorFlow-kostnadsuppskattning behållaren som används för utbildning som standard innehåller TensorFlow-paketet och relaterade beroenden som krävs för utbildning om processorer och GPU: er.

Skicka TensorFlow-jobb:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Distribuerad utbildning
TensorFlow-Estimator kan du träna dina modeller i hög skala över processor- och GPU-kluster av virtuella Azure-datorer. Du kan enkelt köra distribuerad TensorFlow-utbildning med några API-anrop, medan Azure Machine Learning ska hantera i bakgrunden alla infrastruktur och dirigering som behövs för att utföra de här arbetsbelastningarna.

Azure Machine Learning stöder två metoder för distribuerad utbildning i TensorFlow:
1. MPI-baserade distribuerade utbildning med hjälp av den [Horovod](https://github.com/uber/horovod) framework
2. interna [distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed) via metoden parametern server

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
* `node_count`: Antalet noder för utbildning-jobbet. Det här argumentet som standard `1`
* `process_count_per_node`: Antalet processer (eller ”anställda”) för att köra på varje nod. Det här argumentet som standard `1`
* `distributed_backend`: Serverdelen för att starta distribuerade utbildning som kostnadsuppskattning erbjuder via MPI. Det här argumentet som standard `None`. Om du vill utföra parallella eller distribuerade utbildning (t.ex. `node_count`> 1 eller `process_count_per_node`> 1 eller båda) med MPI (och Horovod), ange `distributed_backend='mpi'`. MPI-implementering som används av Azure Machine Learning är [öppna MPI](https://www.open-mpi.org/).

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
* `worker_count`: Antalet arbetare. Det här argumentet som standard `1`
* `parameter_server_count`: Antalet parametern servrar. Det här argumentet som standard `1`
* `distributed_backend`: Serverdelen för distribuerad utbildning. Det här argumentet som standard `None`. För att kunna göra distribuerad utbildning via parameterserver, kommer du behöva ange `distributed_backend='ps'`

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
En självstudiekurs om nod TensorFlow-utbildning, se:
* `training/03.train-tune-deploy-tensorflow/03.train-tune-deploy-tensorflow.ipynb`

En självstudiekurs om distribuerade TensorFlow med Horovod finns:
* `training/04.distributed-tensorflow-with-horovod/04.distributed-tensorflow-with-horovod.ipynb`

En självstudiekurs om interna distribuerade TensorFlow finns:
* `training/05.distributed-tensorflow-with-parameter-server/05.distributed-tensorflow-with-parameter-server.ipynb`

Hämta dessa anteckningsböcker:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
