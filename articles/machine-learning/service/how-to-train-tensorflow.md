---
title: Träna modeller med TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Lär dig hur du kör en nod och distribuerad TensorFlow och Keras-utbildning med TensorFlow och Keras estimators
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d5751ab96dc6b44229e2b18b832a570930058ca
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442346"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Skapa TensorFlow och Keras modeller med Azure Machine Learning-tjänsten

Du kan enkelt köra TensorFlow-utbildningsjobb på Azure compute med hjälp av den [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) kostnadsuppskattning klass i SDK: N för Azure Machine Learning. Den `TensorFlow` kostnadsuppskattning dirigerar Azure Machine Learning-tjänsten att köra jobbet på en TensorFlow-aktiverade behållare för djupa Neurala nätverk (DNN) utbildning.

Den `TensorFlow` kostnadsuppskattning innehåller också ett lager av abstraktion under körning, vilket innebär att du enkelt kan konfigurera parametriserade körs på olika beräkningsmål utan att ändra skripten utbildning.

## <a name="get-started"></a>Kom igång

Eftersom den `TensorFlow` kostnadsuppskattning klass liknar grundläggande [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), rekommenderar vi att du första Läs den [grundläggande kostnadsuppskattning artikel](how-to-train-ml-models.md) att förstå de övergripande begrepp.

Du kommer igång med Azure Machine Learning-tjänsten [Slutför Snabbstart](quickstart-run-cloud-notebook.md). När du är klar har du en [Azure Machine Learning-arbetsyta](concept-azure-machine-learning-architecture.md#workspace) och alla våra [exempel anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml) inklusive de för utbildning dnn: er med TensorFlow och Keras.

## <a name="single-node-training"></a>Nod-utbildning

Om du vill köra ett TensorFlow-jobb, skapa en instans av en [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) objekt och skickar det som ett experiment.

Följande kod skapar en instans av en TensorFlow kostnadsuppskattning och skickar den som ett experiment. Skriptet utbildning `train.py` ska köras med hjälp av parametrarna givet skript. Jobbet ska köras på en GPU-aktiverade [beräkningsmålet](how-to-set-up-training-targets.md), och scikit-Läs kommer installeras som ett beroende för `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Distribuerad utbildning

Den [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) kostnadsuppskattning stöder också distribuerad utbildning i Processorn och GPU-kluster. Du kan enkelt köra distribuerade TensorFlow-jobb och Azure Machine Learning-tjänsten hanterar infrastrukturen och orchestration åt dig.

Azure Machine Learning-tjänsten stöder två metoder för distribuerad utbildning i TensorFlow:

* [MPI-baserade](https://www.open-mpi.org/) distribuerade utbildning med hjälp av den [Horovod](https://github.com/uber/horovod) framework
* Interna [distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed) med metoden parametern server

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) är ett ramverk för öppen källkod för distribuerad utbildning har utvecklats av Uber. Den erbjuder ett enkelt sätt att distribuerade GPU TensorFlow-jobb.

I följande exempel körs ett distribuerade utbildningsjobb med hjälp av Horovod med två arbetsroller som distribueras över två noder.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod och dess beroenden kommer att installeras åt dig, så kan du importera det i din utbildningsskript.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Parameterserver

Du kan också köra [intern distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed), som använder parametern server-modell. I den här metoden kan träna du i ett kluster med parametern-servrar och arbetare. ”Arbetarna” beräkna toningar vid träning, även om parametern servrar aggregera på toningar.

I följande exempel körs ett distribuerade utbildningsjobb med hjälp av parametern Servermetoden med fyra Worker som är distribuerade över två noder.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

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

För hög nivå av Tensorflow's [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow parsa detta `TF_CONFIG` variabeln och skapa klustret-specifikationen för dig.

Tensorflows på lägre nivå core API: er för utbildning, parsa den `TF_CONFIG` variabeln och skapa den `tf.train.ClusterSpec` i koden utbildning. I [det här exemplet](https://aka.ms/aml-notebook-tf-ps), gör du så i **dina utbildningsskript** på följande sätt:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Stöd för Keras

[Keras](https://keras.io/) är populära övergripande DNN Python API som stöder TensorFlow, CNTK och Theano som serverdelar. Om du använder TensorFlow som serverdel, att lägga till Keras är lika enkelt som bland annat en `pip_package` konstruktorparametern.

I följande exempel skapar en instans av en [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) kostnadsuppskattning och skickar den som ett experiment. Kostnadsuppskattning kör utbildning-skript Keras `keras_train.py`. Jobbet ska köras på en gpu-aktiverade [beräkningsmålet](how-to-set-up-training-targets.md) med Keras installeras som ett beroende via pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Exportera till ONNX

Att hämta optimerad inferensjobb med den [ONNX Runtime](concept-onnx.md), du kan konvertera din träningsmodell TensorFlow till ONNX-formatet. Se den [exempel](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Exempel

Du hittar fungerande kodexempel för både en nod och distribuerad TensorFlow-körningar med olika ramverk på [vår GitHub-sida](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Nästa steg

* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
