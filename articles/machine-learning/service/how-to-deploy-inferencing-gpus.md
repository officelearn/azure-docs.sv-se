---
title: Distribuera en modell för inferens med GPU
titleSuffix: Azure Machine Learning service
description: Lär dig hur du distribuerar en deep learning-modell som en webbtjänst som använder en GPU för inferens. I den här artikeln distribueras en Tensorflow-modell till en Azure Kubernetes Service-kluster. Klustret använder en GPU-aktiverad dator till värden webbtjänsten och poäng inferens begäranden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388927"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuera en modell för djupinlärning för inferens med GPU

Lär dig hur du använder GPU inferens för en maskininlärningsmodell som distribueras som en webbtjänst. Inferens eller modell bedömning är fasen där distribuerade modellen används för förutsägelse oftast på produktionsdata.

Den här artikeln lär du dig hur du använder Azure Machine Learning-tjänsten för att distribuera ett exempel Tensorflow deep learning-modell till ett Azure Kubernetes Service (AKS)-kluster på en GPU-aktiverad virtuell dator (VM). När begäranden skickas till tjänsten, använder modellen GPU för att köra arbetsbelastningar för inferens.

GPU: er erbjuder prestandafördelar jämfört med processorer på mycket kan beräkning. Utmärkt användningsområden för GPU-aktiverade virtuella datorer är djupinlärning modellera utbildning och inferens, särskilt för stora batchar med begäranden.

Det här exemplet visar hur du distribuerar en TensorFlow spara modellen till Azure Machine Learning. Du kan utföra följande steg:

* Skapa en GPU-aktiverade AKS-kluster
* Distribuera en Tensorflow GPU-modell

## <a name="prerequisites"></a>Nödvändiga komponenter

* En arbetsyta för Azure Machine Learning-tjänster
* En Python-distribution
* En registrerad Tensorflow spara modellen. Läs hur du registrerar modeller i [distribuera modeller](../service/how-to-deploy-and-where.md#registermodel).

Den här artikeln är baserad på Jupyter-anteckningsboken [Tensorflow distribuera modeller till AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). Jupyter-anteckningsboken använder TensorFlow sparade modeller och distribuerar dem till ett AKS-kluster. Du kan också använda anteckningsboken för alla machine learning-ramverk som stöder GPU: er genom att göra små ändringar till bedömningsfil och miljöfil.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Etablera ett AKS-kluster med GPU: er

Azure har många olika GPU-alternativ. Du kan använda någon av dem för inferensjobb. Se [listan över virtuella datorer i N-serien](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) för en fullständig uppdelning av funktioner och kostnader.

Mer information om att använda AKS med Azure Machine Learning-tjänsten finns i [hur du distribuerar och var](../service/how-to-deploy-and-where.md#deploy-aks).

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure fakturerar så länge AKS-klustret har etablerats. Se till att ta bort ditt AKS-kluster när du är klar med den.

## <a name="write-the-entry-script"></a>Skriva skript för transaktion

Spara följande kod i din arbetskatalog som `score.py`. Den här filen poängsätter bilder när de skickas till din tjänst. Den läser in sparade TensorFlow-modellen, skickar in avbildningen till TensorFlow-session på varje POST-begäran och returnerar sedan resulterande poängen. Andra inferensjobb ramverk kräver olika bedömningsfilerna.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-the-conda-environment"></a>Definiera conda-miljö

Skapa en conda miljö-fil med namnet `myenv.yml` ange beroenden för din tjänst. Det är viktigt att ange att du använder `tensorflow-gpu` att uppnå högre prestanda.

```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Definiera GPU InferenceConfig-klass

Skapa en `InferenceConfig` objekt som gör att de GPU: er och säkerställer att CUDA har installerats med en Docker-avbildning.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
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

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Utfärda en exempelfråga i din distribuerade modell

Skicka en Testfråga till distribuerad modell. När du skickar en JPEG-bild till modellen poängsätter avbildningen.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
