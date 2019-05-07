---
title: Hur du distribuerar en modell med djupinlärning för inferensjobb med GPU
titleSuffix: Azure Machine Learning service
description: Lär dig hur du distribuerar en deep learning-modell som en webbtjänst som använder en GPU för inferensjobb. I den här artikeln distribueras en Tensorflow-modell till en Azure Kubernetes Service-kluster. Klustret använder en GPU-aktiverad dator till värden webbtjänsten och poäng inferensjobb begäranden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160657"
---
# <a name="how-to-do-gpu-inferencing"></a>Hur du gör GPU inferensjobb

Lär dig hur du använder GPU inferensjobb för en maskininlärningsmodell som distribueras som en webbtjänst. I den här artikeln får du lära dig hur du använder Azure Machine Learning-tjänsten för att distribuera ett exempel Tensorflow deep learning-modell. Modellen har distribuerats till ett kluster i Azure Kubernetes Service (AKS) som använder en GPU-aktiverad virtuell dator som värd för tjänsten. Modellen använder GPU för att utföra inferensjobb när begäranden skickas till tjänsten.

GPU: er erbjuder prestandafördelar jämfört med processorer på mycket kan beräkning. Utbildnings- och inferensjobb deep learning-modeller (särskilt för stora batchar av begäranden) är utmärkt användningsområden för GPU: er.  

Det här exemplet visas hur du distribuerar en modell för TensorFlow som sparats till Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Mål och krav

Följ anvisningarna för att:
* Skapa en GPU aktiverat AKS-kluster
* Distribuera en modell med Tensorflow-GPU

Krav:
* Azure Machine Learning services-arbetsyta
* Python
* Tensorflow SavedModel registrerad. Lär dig hur du registrerar modeller finns i [distribuera modeller](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Den här artikeln är baserad på [Tensorflow distribuera modeller till AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), som använder TensorFlow sparade modeller och distribuerar till ett AKS-kluster. Men med små ändringar av bedömningsfilen och miljöfil är det gäller för alla machine learning-ramverk som stöd för GPU: er.  

## <a name="provision-aks-cluster-with-gpus"></a>Etablera AKS-kluster med GPU: er
Azure har många olika alternativ i GPU, som kan användas för Inferensjobb. Se [listan med N-serien](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) för en fullständig uppdelning av funktioner och kostnader. 

Mer information om att använda AKS med Azure Machine Learning-tjänsten finns i den [hur du distribuerar och där artikel.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

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


## <a name="write-entry-script"></a>Skriva skript för transaktion

Spara följande till din arbetskatalog som `score.py`. Den här filen används för att bedöma bilder när de skickas till din tjänst. Den här filen läser in TensorFlow spara modellen och sedan begäran på varje INLÄGG skickar inmatad bild till TensorFlow-session och returnerar de resulterande poäng.
Andra inferensjobb ramverk kräver olika bedömningsfilerna.

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

## <a name="define-conda-environment"></a>Definiera Conda-miljö
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

## <a name="define-gpu-inferenceconfig"></a>Definiera GPU InferenceConfig

Skapa en [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) som anger att du aktiverar GPU. Det säkerställer att CUDA har installerats med din avbildning.

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

Mer information finns i [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) och [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Distribuera modellen

Distribuera modellen till AKS-klustret och vänta tills det för att skapa din tjänst.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning-tjänsten kommer inte att distribuera en modell med en `InferenceConfig` som förväntar sig GPU till ett kluster utan GPU.

Mer information finns i [modellen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Problemet exempelfråga till distribuerade modell

Utfärda en exempelfråga i din distribuerade modell. Den här modellen kommer att bedöma alla jpeg-bild som du skickar till den som en post-begäran. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Din klient ska vara i samma Azure-region som slutpunkt för att optimera svarstid och dataflöde.  För närvarande skapas av API: er i regionen östra USA Azure.

## <a name="cleaning-up-the-resources"></a>Rensa resurser

Ta bort resurserna när du är klar med demon.

> [!IMPORTANT]
> Azure fakturerar dig baserat på hur länge AKS-klustret distribueras. Se till att rensa upp när du är klar med den.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera modellen på FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Distribuera modell med ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Träna DNN Tensorflow-modeller](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
