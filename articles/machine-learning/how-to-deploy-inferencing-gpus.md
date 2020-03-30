---
title: Distribuera en modell för slutledning med GPU
titleSuffix: Azure Machine Learning
description: I den här artikeln får du lära dig hur du använder Azure Machine Learning för att distribuera en GPU-aktiverad Tensorflow deep learning-modell som en webbtjänst.service och poänginferensbegäranden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398340"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuera en deep learning-modell för slutledning med GPU
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder Azure Machine Learning för att distribuera en GPU-aktiverad modell som en webbtjänst. Informationen i den här artikeln baseras på distribution av en modell på Azure Kubernetes Service (AKS). AKS-klustret tillhandahåller en GPU-resurs som används av modellen för slutledning.

Inferens, eller modellbedömning, är den fas där den distribuerade modellen används för att göra förutsägelser. Att använda GPU:er i stället för processorer ger prestandafördelar vid mycket parallelliserbar beräkning.

> [!IMPORTANT]
> För webbtjänstdistributioner stöds GPU-inferens endast på Azure Kubernetes Service. För inferens med hjälp av en pipeline för __maskininlärning__stöds GPU:er endast på Azure Machine Learning Compute. Mer information om hur du använder ML-pipelines finns i [Kör batchförutsägelser](how-to-use-parallel-run-step.md). 

> [!TIP]
> Även om kodavsnitten i den här artikeln använder en TensorFlow-modell kan du använda informationen på alla maskininlärningsramverk som stöder GPU:er.

> [!NOTE]
> Informationen i den här artikeln bygger på informationen i artikeln [Hur du distribuerar till Azure Kubernetes Service.](how-to-deploy-azure-kubernetes-service.md) Om artikeln vanligtvis täcker distribution till AKS täcker den här artikeln GPU-specifik distribution.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* En Python-utvecklingsmiljö med Azure Machine Learning SDK installerad. Mer information finns i [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* En registrerad modell som använder en GPU.

    * Mer information om hur du registrerar modeller finns i [Distribuera modeller](how-to-deploy-and-where.md#registermodel).

    * Om du vill skapa och registrera Tensorflow-modellen som används för att skapa det här dokumentet finns i Så här tränar du [en TensorFlow-modell](how-to-train-tensorflow.md).

* En allmän förståelse för [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

Om du vill ansluta till en befintlig arbetsyta använder du följande kod:

> [!IMPORTANT]
> Det här kodavsnittet förväntar sig att arbetsytans konfiguration ska sparas i den aktuella katalogen eller dess överordnade katalog. Mer information om hur du skapar en arbetsyta finns i [Skapa och hantera Azure Machine Learning-arbetsytor](how-to-manage-workspace.md).   Mer information om hur du sparar konfigurationen i filen finns i [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Skapa ett Kubernetes-kluster med GPU:er

Azure Kubernetes Service innehåller många olika GPU-alternativ. Du kan använda någon av dem för modell inferens. Se [listan över virtuella datorer i N-serien](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) för en fullständig uppdelning av funktioner och kostnader.

Följande kod visar hur du skapar ett nytt AKS-kluster för din arbetsyta:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

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
> Azure fakturerar dig så länge AKS-klustret finns. Se till att ta bort AKS-klustret när du är klar med det.

Mer information om hur du använder AKS med Azure Machine Learning finns i [Så här distribuerar du till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Skriv postskriptet

Inmatningsskriptet tar emot data som skickas till webbtjänsten, skickar det till modellen och returnerar poängresultaten. Följande skript läser in Tensorflow-modellen vid start och använder sedan modellen för att poängsätta data.

> [!TIP]
> Startskriptet är specifikt för din modell. Skriptet måste till exempel känna till ramverket för att använda med din modell, dataformat etc.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Den här `score.py`filen heter . Mer information om inmatningsskript finns i [Hur och var du ska distribuera](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definiera conda-miljön

Conda-miljöfilen anger beroenden för tjänsten. Den innehåller beroenden som krävs av både modellen och inmatningsskriptet. Observera att du måste ange azureml-defaults med verion >= 1.0.45 som ett pipberoende, eftersom det innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst. Följande YAML definierar miljön för en Tensorflow-modell. Den anger `tensorflow-gpu`, som kommer att använda sig av GPU som används i den här distributionen:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

I det här exemplet sparas filen som `myenv.yml`.

## <a name="define-the-deployment-configuration"></a>Definiera distributionskonfigurationen

Distributionskonfigurationen definierar azure Kubernetes Service-miljön som används för att köra webbtjänsten:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Mer information finns i referensdokumentationen för [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definiera inferenskonfigurationen

Inferenskonfigurationen pekar på inmatningsskriptet och ett miljöobjekt, som använder en dockeravbildning med GPU-stöd. Observera att YAML-filen som används för miljödefinition måste innehålla azureml-standardvärden med version >= 1.0.45 som pipberoende, eftersom den innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Mer information om miljöer finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).
Mer information finns i referensdokumentationen för [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Distribuera modellen

Distribuera modellen till AKS-klustret och vänta på att den ska skapa din tjänst.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Om `InferenceConfig` objektet `enable_gpu=True`har måste `deployment_target` parametern referera till ett kluster som tillhandahåller en GPU. Annars misslyckas distributionen.

Mer information finns i referensdokumentationen för [Modell](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Utfärda en exempelfråga till tjänsten

Skicka en testfråga till den distribuerade modellen. När du skickar en jpeg-bild till modellen får den bilden att göra. Följande kodexempel hämtar testdata och väljer sedan en slumpmässig testavbildning som ska skickas till tjänsten.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Mer information om hur du skapar ett klientprogram finns i [Skapa klient för att använda distribuerad webbtjänst](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Rensa resurserna

Om du har skapat AKS-klustret specifikt för det här exemplet tar du bort dina resurser när du är klar.

> [!IMPORTANT]
> Azure fakturerar dig baserat på hur länge AKS-klustret distribueras. Se till att städa upp när du är klar med det.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera modell på FPGA](how-to-deploy-fpga-web-service.md)
* [Distribuera modell med ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Train Tensorflow DNN Modeller](how-to-train-tensorflow.md)
