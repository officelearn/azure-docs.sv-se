---
title: Distribuera en modell för härledning med GPU
titleSuffix: Azure Machine Learning service
description: Den här artikeln lär dig hur du använder Azure Machine Learnings tjänsten för att distribuera en GPU-aktiverad Tensorflow djup inlärnings modell som en webb tjänst. tjänst-och Poäng härlednings begär Anden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326990"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuera en djup inlärnings modell för en härledning med GPU

Den här artikeln lär dig hur du använder Azure Machine Learning-tjänsten för att distribuera en GPU-aktiverad Tensorflow djup inlärnings modell som en webb tjänst.

Distribuera din modell till ett Azure Kubernetes service-kluster (AKS) för att göra GPU-aktiverad inferencing. Inferencing eller modell poängsättning är fasen där den distribuerade modellen används för förutsägelse. Att använda GPU: er i stället för processorer ger prestanda för delar med mycket kan göras parallella beräkning.

Även om det här exemplet använder en TensorFlow-modell kan du tillämpa följande steg för alla Machine Learning-ramverk som stöder GPU: er genom att göra små ändringar i bedömnings filen och miljö filen. 

I den här självstudien gör du följande:

* Skapa ett GPU-aktiverat AKS-kluster
* Distribuera en Tensorflow GPU-modell
* Skicka en exempel fråga till din distribuerade modell

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning Services-arbetsyta.
* En python-distribution.
* En registrerad Tensorflow-Sparad modell.
    * Information om hur du registrerar modeller finns i [Distribuera modeller](../service/how-to-deploy-and-where.md#registermodel).

Du kan slutföra en del av den här instruktions serien, [hur du tränar en TensorFlow-modell](how-to-train-tensorflow.md)för att uppfylla de nödvändiga förutsättningarna.

## <a name="provision-an-aks-cluster-with-gpus"></a>Etablera ett AKS-kluster med GPU: er

Azure har många olika GPU-alternativ. Du kan använda dem för inferencing. Se [listan över virtuella datorer i N-serien](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) för en fullständig analys av funktioner och kostnader.

Mer information om hur du använder AKS med Azure Machine Learning-tjänsten finns i [distribuera och var](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure kommer att debiteras så länge AKS-klustret är etablerad. Se till att ta bort ditt AKS-kluster när du är klar.

## <a name="write-the-entry-script"></a>Skriv start skriptet

Spara följande kod i din arbets katalog som `score.py`. Den här filen visar bilder som de skickas till din tjänst. Den läser in den sparade modellen TensorFlow, överför indata-avbildningen till TensorFlow-sessionen på varje POST-begäran och returnerar sedan resultaten. Andra inferencing-ramverk kräver olika bedömnings filer.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Definiera Conda-miljön

Skapa en Conda-miljö fil `myenv.yml` med namnet för att ange beroenden för din tjänst. Det är viktigt att ange att du använder `tensorflow-gpu` för att uppnå accelererade prestanda.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Definiera GPU-InferenceConfig-klassen

Skapa ett `InferenceConfig` objekt som aktiverar GPU: er och säkerställer att CUDA installeras med Docker-avbildningen.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

Mer information finns i:

- [InferenceConfig-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Distribuera modellen

Distribuera modellen till ditt AKS-kluster och vänta tills den har skapat din tjänst.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning tjänsten distribuerar inte en modell med ett `InferenceConfig` objekt som förväntar sig att GPU är aktiverat för ett kluster som inte har någon GPU.

Mer information finns i [modell klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Skicka en exempel fråga till din modell

Skicka en test fråga till den distribuerade modellen. När du skickar en JPEG-bild till modellen, visas bilden. I följande kod exempel används en extern funktion för att läsa in bilder. Du hittar relevant kod i pir TensorFlow- [exemplet på GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> Kontrol lera att klienten finns i samma Azure-region som slut punkten för att minimera svars tiden och optimera data flödet. I det här exemplet skapas API: erna i Azure-regionen USA, östra.

## <a name="clean-up-the-resources"></a>Rensa resurserna

Om du har skapat AKS-klustret specifikt för det här exemplet tar du bort dina resurser när du är klar.

> [!IMPORTANT]
> Azure-räkningar baseras på hur länge AKS-klustret distribueras. Se till att rensa upp den när du är klar.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera modell på FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Distribuera modell med ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Träna Tensorflow DNN-modeller](../service/how-to-train-tensorflow.md)
