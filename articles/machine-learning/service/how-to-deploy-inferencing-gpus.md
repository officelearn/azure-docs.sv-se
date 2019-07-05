---
title: Distribuera en modell för inferens med GPU
titleSuffix: Azure Machine Learning service
description: Den här artikeln lär du dig hur du använder Azure Machine Learning-tjänsten för att distribuera en GPU-aktiverade Tensorflow som deep learning-modell som en web-service.service och poäng inferens begäranden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543806"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuera en modell för djupinlärning för inferens med GPU

Den här artikeln lär du dig hur du använder Azure Machine Learning-tjänsten för att distribuera en GPU-aktiverade Tensorflow som deep learning-modell som en webbtjänst.

Distribuera din modell till ett Azure Kubernetes Service (AKS)-kluster för att göra GPU-aktiverade inferensjobb. Inferensjobb eller modell bedömning är fasen där distribuerade modellen används för förutsägelse. Med GPU: er i stället för processorer erbjudandet prestandafördelar på mycket kan beräkning.

Även om det här exemplet används en TensorFlow-modell, kan du använda följande steg till alla machine learning-ramverk som stöder GPU: er genom att göra små ändringar till bedömningsfil och miljöfil. 

I den här självstudien gör du följande:

* Skapa en GPU-aktiverade AKS-kluster
* Distribuera en Tensorflow GPU-modell
* Utfärda en exempelfråga i din distribuerade modell

## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänster.
* En Python-distribution.
* En registrerad Tensorflow spara modellen.
    * Läs hur du registrerar modeller i [distribuera modeller](../service/how-to-deploy-and-where.md#registermodel).

Du kan slutföra första delen i det här instruktionsserie [hur du tränar en modell för TensorFlow](how-to-train-tensorflow.md), för att uppfylla kraven.

## <a name="provision-an-aks-cluster-with-gpus"></a>Etablera ett AKS-kluster med GPU: er

Azure har många olika GPU-alternativ. Du kan använda någon av dem för inferensjobb. Se [listan över virtuella datorer i N-serien](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) för en fullständig uppdelning av funktioner och kostnader.

Mer information om att använda AKS med Azure Machine Learning-tjänsten finns i [hur du distribuerar och var](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure fakturerar så länge AKS-klustret har etablerats. Se till att ta bort ditt AKS-kluster när du är klar med den.

## <a name="write-the-entry-script"></a>Skriva skript för transaktion

Spara följande kod i din arbetskatalog som `score.py`. Den här filen poängsätter bilder när de skickas till din tjänst. Den läser in sparade TensorFlow-modellen, skickar in avbildningen till TensorFlow-session på varje POST-begäran och returnerar sedan resulterande poängen. Andra inferensjobb ramverk kräver olika bedömningsfilerna.

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
## <a name="define-the-conda-environment"></a>Definiera conda-miljö

Skapa en conda miljö-fil med namnet `myenv.yml` ange beroenden för din tjänst. Det är viktigt att ange att du använder `tensorflow-gpu` att uppnå högre prestanda.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definiera GPU InferenceConfig-klass

Skapa en `InferenceConfig` objekt som gör att de GPU: er och säkerställer att CUDA har installerats med en Docker-avbildning.

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

Distribuera modellen till AKS-klustret och vänta tills det för att skapa din tjänst.

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
> Azure Machine Learning-tjänsten inte kommer att distribuera en modell med en `InferenceConfig` objekt som förväntar sig GPU aktiveras till ett kluster som inte har en GPU.

Mer information finns i [Modellklass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Utfärda en exempelfråga i din modell

Skicka en Testfråga till distribuerad modell. När du skickar en JPEG-bild till modellen poängsätter avbildningen. Följande kodexempel används en funktion för externa verktyg för att läsa in bilder. Du hittar den relevanta koden på pir [TensorFlow-exemplet på GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
> Kontrollera att klienten är i samma Azure-region som slutpunkt för att minimera svarstiden och optimera genomflödet. I det här exemplet skapas av API: er i regionen östra USA Azure.

## <a name="clean-up-the-resources"></a>Rensa resurserna

Ta bort resurserna när du är klar med det här exemplet.

> [!IMPORTANT]
> Azure fakturerar dig baserat på hur länge AKS-klustret distribueras. Se till att rensa upp när du är klar med den.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera modellen på FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Distribuera modell med ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Träna DNN Tensorflow-modeller](../service/how-to-train-tensorflow.md)
