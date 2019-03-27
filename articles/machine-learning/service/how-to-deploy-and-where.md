---
title: Distribuera modeller som webbtjänster
titleSuffix: Azure Machine Learning service
description: 'Lär dig hur och var att distribuera dina modeller för Azure Machine Learning-tjänsten som bland annat: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge och Field-programmable gate-matriser.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 370fb17e9f00d64db847e49c48c6f03cb329612d
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472947"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Azure Machine Learning SDK finns flera sätt du kan distribuera den tränade modellen. I det här dokumentet lär du dig hur du distribuerar din modell som en webbtjänst i Azure-molnet, eller IoT Edge-enheter.

Du kan distribuera modeller till följande beräkning:

| Beräkningsmål | Distributionstyp | Beskrivning |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | I realtid inferens | Bra för Produktionsdistribution av hög skalbarhet. Tillhandahåller automatisk skalning och snabba svarstider. |
| [Azure Machine Learning Compute (amlcompute)](#azuremlcompute) | Batch inferens | Kör batch-förutsägelse på beräkning utan server. Stöder normalt och lågprioriterade virtuella datorer. |
| [Azure Container Instances (ACI)](#aci) | Testning | Bra för utveckling och testning. **Inte lämplig för produktionsarbetsbelastningar.** |
| [Azure IoT Edge](#iotedge) | (Förhandsversion) IoT-modul | Distribuera modeller på IoT-enheter. Inferensjobb sker på enheten. |
| [Fältet-programmable gate array FPGA)](#fpga) | (Förhandsversion) Webbtjänst | Extremt låg latens för i realtid inferensjobb. |

Processen för att distribuera en modell är liknande för alla beräkningsmål:

1. Träna och registrera en modell.
1. Konfigurera och registrera en avbildning som använder modellen.
1. Distribuera avbildningen till ett beräkningsmål.
1. Testa distributionen

Följande videoklipp visar hur du distribuerar till Azure Container Instances:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Mer information om begrepp som ingår i arbetsflödet finns i [hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

- En arbetsyta för Azure Machine Learning-tjänsten och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md).

- En tränad modell. Om du inte har en tränad modell, Följ stegen i den [träna modeller](tutorial-train-models-with-aml.md) självstudie för att träna och registrera en med Azure Machine Learning-tjänsten.

    > [!NOTE]
    > Medan Azure Machine Learning-tjänsten kan arbeta med valfri allmän modell som kan läsas in i Python 3, visar exemplen i det här dokumentet med hjälp av en modell som lagras i Python pickle-format.
    >
    > Mer information om hur du använder ONNX-modeller finns i den [ONNX och Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentet.

## <a id="registermodel"></a> Registrera en träningsmodell

Modell-registret är ett sätt att lagra och organisera dina tränade modeller i Azure-molnet. Modeller är registrerade i din arbetsyta för Azure Machine Learning-tjänsten. Modellen tränas med hjälp av Azure Machine Learning eller en annan tjänst. Följande kod visar hur du registrerar en modell från filen genom att ange ett namn, taggar och en beskrivning:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Uppskattad tidsåtgång**: Cirka 10 sekunder.

Ett exempel med att registrera en modell finns i [träna en bild-klassificerare](tutorial-train-models-with-aml.md).

Mer information finns i referensdokumentationen för den [Modellklass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Skapa och registrera en avbildning

Distribuerade modeller paketeras i en bild. Bilden innehåller de beroenden som behövs för att köra modellen.

För **Azure-Behållarinstans**, **Azure Kubernetes Service**, och **Azure IoT Edge** distributioner, den [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) klassen används för att skapa en konfiguration för avbildningen. Bild-konfigurationen används sedan för att skapa en ny dockeravbildning.

Följande kod visar hur du skapar en ny bildkonfiguration:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Uppskattad tidsåtgång**: Cirka 10 sekunder.

Viktiga parametrar i det här exemplet som beskrivs i följande tabell:

| Parameter | Beskrivning |
| ----- | ----- |
| `execution_script` | Anger ett pythonskript som används för att ta emot förfrågningar som skickas till tjänsten. I det här exemplet finns skriptet i den `score.py` filen. Mer information finns i den [körning skriptet](#script) avsnittet. |
| `runtime` | Anger att avbildningen använder Python. Ett annat alternativ är `spark-py`, som använder Python med Apache Spark. |
| `conda_file` | Används för att ange en fil för conda-miljö. Den här filen definierar conda-miljö för distribuerad modell. Mer information om hur du skapar den här filen finns i [skapar du en miljöfil (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Ett exempel för att skapa en avbildning konfiguration finns i [distribuera en avbildning klassificerare](tutorial-deploy-models-with-aml.md).

Mer information finns i referensdokumentationen för [ContainerImage-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="script"></a> Körning av skript

Körning av skriptet tar emot data som skickas till en distribuerade avbildningen och skickar dem till modellen. Sedan tar svaret som returnerades av modellen och returnerar som till klienten. **Skriptet är specifik för din modell**; den måste förstå de data som modellen förväntas och returnerar. Ett exempelskript som fungerar med en modell för klassificering av bild, se [distribuera en avbildning klassificerare](tutorial-deploy-models-with-aml.md).

Skriptet innehåller två funktioner som att läsa in och kör modellen:

* `init()`: Den här funktionen läses vanligtvis in modellen till ett globala objekt. Den här funktionen körs endast en gång när Docker-containern startas.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Indata och utdata kör du vanligtvis använda JSON för serialisering och deserialisering. Du kan också arbeta med binära rådata. Du kan omvandla data innan du skickar till modellen eller innan det returneras till klienten.

#### <a name="working-with-json-data"></a>Arbeta med JSON-data

Följande exempelskript godkänner och returnerar JSON-data. Den `run` funktionen omvandlar data från JSON till ett format att modellen förväntar sig och sedan omvandlar JSON svaret innan det returneras:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Arbeta med binära data

Om din modell accepterar __binärdata__, använda `AMLRequest`, `AMLResponse`, och `rawhttp`. Följande exempelskript accepterar binära data och Returnerar inverterad byte för POST-förfrågningar. För GET-begäranden returnerar den fullständiga URL i svarstexten:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Den `azureml.contrib` namnområde ändras ofta, när vi arbetar för att förbättra tjänsten. Därför ska någonting i det här namnområdet räknas som en förhandsversion, och stöds inte fullt ut av Microsoft.
>
> Om du vill testa detta på din lokala utvecklingsmiljö kan du installera komponenterna i den `contrib` namnområde med hjälp av följande kommando:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Registrera avbildningen

När du har skapat avbildningen konfigurationen, kan du använda det för att registrera en bild. Den här avbildningen finns sparad i behållarregister för arbetsytan. När du skapat kan du distribuera samma avbildning för flera tjänster.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Uppskattad tidsåtgång**: Ca 3 minuter.

Bilder är version automatiskt när du registrerar flera avbildningar med samma namn. Till exempel den första bilden registrerad som `myimage` tilldelas ID `myimage:1`. Nästa gång du registrerar en bild som `myimage`, ID för den nya avbildningen är `myimage:2`.

Mer information finns i referensdokumentationen för [ContainerImage klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Distribuera som en webbtjänst

När du kommer till distribution, är processen variera beroende på beräkningsmål som du distribuerar till. Använd informationen i följande avsnitt för att lära dig hur du distribuerar till:

| Beräkningsmål | Distributionstyp | Beskrivning |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Webbtjänst (i realtid inferens)| Bra för Produktionsdistribution av hög skalbarhet. Tillhandahåller automatisk skalning och snabba svarstider. |
| [Azure ML Compute](#azuremlcompute) | Webbtjänsten (Batch inferens)| Kör batch-förutsägelse på beräkning utan server. Stöder normalt och lågprioriterade virtuella datorer. |
| [Azure Container Instances (ACI)](#aci) | Webbtjänsten (utveckling/testning)| Bra för utveckling och testning. **Inte lämplig för produktionsarbetsbelastningar.** |
| [Azure IoT Edge](#iotedge) | (Förhandsversion) IoT-modul | Distribuera modeller på IoT-enheter. Inferensjobb sker på enheten. |
| [Fältet-programmable gate array FPGA)](#fpga) | (Förhandsversion) Webbtjänst | Extremt låg latens för i realtid inferensjobb. |

> [!IMPORTANT]
> Cross-origin resource sharing (CORS) stöds inte för närvarande när du distribuerar en modell som en webbtjänst.

I exemplen i det här avsnittet används [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), vilket kräver att du att registrera modellen och avbildning innan du gör en distribution. Mer information om andra distributionsmetoder finns [distribuera](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) och [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Distribuera till Azure Container Instances (DEVTEST)

Använd Azure Container Instances för att distribuera dina modeller som en webbtjänst om en eller flera av följande villkor är uppfyllt:

- Du behöver att snabbt distribuera och verifiera din modell. ACI-distributionen är klar på mindre än 5 minuter.
- Du testar en modell som är under utveckling. Kvoter och regional tillgänglighet för ACI finns i den [kvoter och regiontillgänglighet för Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentet.

Om du vill distribuera till Azure Container Instances, använder du följande steg:

1. Definiera distributionskonfigurationen. Den här konfigurationen är beroende av kraven i din modell. I följande exempel definierar en konfiguration som använder en processorkärna och 1 GB minne:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Distribuera den avbildning som skapats i den [skapa avbildningen](#createimage) avsnitt i det här dokumentet, Använd följande kod:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Uppskattad tidsåtgång**: Cirka 5 minuter.

Mer information finns i referensdokumentationen för den [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) och [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) klasser.

### <a id="aks"></a> Distribuera till Azure Kubernetes Service (produktion)

Använd Azure Kubernetes Service (AKS) för att distribuera din modell som en webbtjänst i produktionsmiljön för hög skalbarhet. Du kan använda ett befintligt AKS-kluster eller skapa en ny med SDK: N för Azure Machine Learning, CLI eller Azure-portalen.

Skapa ett AKS-kluster är en tid processen för arbetsytan. Du kan återanvända det här klustret för flera distributioner.

> [!IMPORTANT]
> Om du tar bort klustret, måste du skapa ett nytt kluster nästa gång du behöver distribuera.

Azure Kubernetes Service innehåller följande funktioner:

* Automatisk skalning
* Loggning
* Insamling av modelldata
* Snabba svarstider för webbtjänster
* TLS-avslutning
* Authentication

#### <a name="autoscaling"></a>Automatisk skalning

Automatisk skalning kan kontrolleras genom att ange `autoscale_target_utilization`, `autoscale_min_replicas`, och `autoscale_max_replicas` för AKS-webbtjänsten. I följande exempel visar hur du aktiverar automatisk skalning:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Beslut genom att skala upp och ned baserat på användning av de aktuella behållaren replikerna. Antal repliker som är upptagna (bearbetar en begäran) dividerat med totalen antalet aktuella repliker är den aktuella användningen. Om det här värdet överskrider målanvändning, skapas fler repliker. Om den är lägre, minskas repliker. Som standard är målanvändning 70%.

Beslut att lägga till repliker görs och implementeras snabbt (cirka 1 sekund). Beslut att ta bort replikerna tar längre tid (cirka 1 minut). Det här beteendet från inaktiva repliker runt för en minut om nya förfrågningarna kommer att de kan hantera.

Du kan beräkna krävs replikerna med hjälp av följande kod:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Mer information om hur `autoscale_target_utilization`, `autoscale_max_replicas`, och `autoscale_min_replicas`, finns i den [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) referens.

#### <a name="create-a-new-cluster"></a>Skapa ett nytt kluster

Använd följande kod för att skapa ett nytt Azure Kubernetes Service-kluster:

> [!IMPORTANT]
> Skapa AKS-klustret är en tid processen för arbetsytan. När du skapat kan du återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resursgruppen som innehåller den, måste du skapa ett nytt kluster nästa gång du behöver distribuera.
> För [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), om du väljer Anpassad värden för agent_count och vm_size, måste du se till att agent_count multiplicerat med vm_size är större än eller lika med 12 virtuella processorer. Till exempel om du använder en vm_size av ”Standard_D3_v2” som har 4 virtuella processorer, bör sedan du välja en agent_count 3 eller högre.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Uppskattad tidsåtgång**: Ungefär 20 minuter.

#### <a name="use-an-existing-cluster"></a>Använd ett befintligt kluster

Om du redan har AKS-kluster i Azure-prenumerationen och det är version 1.12. ## och har minst 12 virtuella processorer, du kan använda den för att distribuera din avbildning. Följande kod visar hur du ansluter en befintlig AKS 1.12. ## klustret till din arbetsyta:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Uppskattad tidsåtgång**: Ca 3 minuter.

Mer information om hur du skapar ett AKS-kluster utanför Azure Machine Learning SDK finns i följande artiklar:

* [Skapa ett AKS-kluster](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Skapa ett AKS-kluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Distribuera avbildningen

Distribuera den avbildning som skapats i den [skapa avbildningen](#createimage) avsnitt i det här dokumentet i Azure Kubernetes serverkluster, Använd följande kod:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Uppskattad tidsåtgång**: Ca 3 minuter.

Mer information finns i referensdokumentationen för den [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) och [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) klasser.

### <a id="azuremlcompute"></a> Inferens med Azure ML-beräkning

Azure ML-beräkningsmål skapas och hanteras av Azure Machine Learning-tjänsten. De kan användas för batch-förutsägelse från Azure ML-Pipelines.

En genomgång av batch inferens med Azure ML Compute läsa den [hur du kör Batch-förutsägelser](how-to-run-batch-predictions.md) dokumentet.


### <a id="fpga"></a> Distribuera till fältet-programmable gate matriser (FPGA)

Project Brainwave gör det möjligt att uppnå extremt låg latens för i realtid inferensjobb begäranden. Project Brainwave går det snabbare djupa neurala nätverk (DNN) som har distribuerats på fält-programmable gate matriser i Azure-molnet. Vanligaste dnn: er är tillgängliga som featurizers för induktiv inlärning eller anpassningsbara med vikterna tränas från dina egna data.

En genomgång för att distribuera en modell med Project Brainwave finns i den [distribuera till en FPGA](how-to-deploy-fpga-web-service.md) dokumentet.

## <a name="define-schema"></a>Definiera schema

Anpassade dekoratörer kan användas för [OpenAPI](https://swagger.io/docs/specification/about/) specifikationen generation och indata skriver manipulering av när du distribuerar webbtjänsten. I den `score.py` -fil du anger ett exempel på indata/utdata i konstruktorn eller för en definierad typ.-objekt och typ och exemplet används för att generera schemat automatiskt. Följande typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* standard Python

Kontrollera först beroenden som behövs för den `inference-schema` paket som ingår i din `env.yml` conda miljöfil. Det här exemplet används den `numpy` parametertypen för schemat, så det extra pip `[numpy-support]` installeras också.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Sedan ändrar den `score.py` fil att importera den `inference-schema` paket. Definiera indata och utdataformat exemplet i den `input_sample` och `output_sample` variabler, som representerar format för begäran och svar för webbtjänsten. Använd de här exemplen i indata och utdata funktionen dekoratörer på den `run()` funktion.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

När du har genomfört normala registrering och web service distribution av avbildningar med det uppdaterade `score.py` fil, hämta Swagger-uri från tjänsten. Begär den här URI: n returnerar den `swagger.json` filen.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



När du skapar en ny avbildning måste du manuellt uppdatera varje tjänst som du vill använda den nya avbildningen. Om du vill uppdatera webbtjänsten använder den `update` metoden. Följande kod visar hur du uppdaterar webbtjänsten för att använda en ny avbildning:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Mer information finns i referensdokumentationen för den [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) klass.

## <a name="test-web-service-deployments"></a>Testa webbtjänstdistributioner

Om du vill testa en distribution av webbtjänster som du kan använda den `run` -metoden i Webservice-objektet. I följande exempel anges JSON-dokument till en webbtjänst och resultatet visas. Data som skickas måste matcha modellen förväntas. I det här exemplet matchar dataformatet indata som förväntas av diabetes modellen.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Webbtjänsten är ett REST-API så du kan skapa klientprogram på flera olika programmeringsspråk. Mer information finns i [skapa klient program kan använda webservices](how-to-consume-web-service.md).

## <a id="update"></a> Uppdatera webbtjänsten

När du skapar en ny avbildning måste du manuellt uppdatera varje tjänst som du vill använda den nya avbildningen. Om du vill uppdatera webbtjänsten använder den `update` metoden. Följande kod visar hur du uppdaterar webbtjänsten för att använda en ny avbildning:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Mer information finns i referensdokumentationen för den [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) klass.

## <a id="iotedge"></a> Distribuera till Azure IoT Edge

En Azure IoT Edge-enhet är en Linux eller Windows-baserad enhet som kör Azure IoT Edge-körningen. Med Azure IoT Hub kan distribuera du machine learning-modeller till dessa enheter som IoT Edge-moduler. Distribuera en modell till en IoT Edge-enhet gör att enheten använder modellen direkt, i stället för att skicka data till molnet för bearbetning. Du får kortare svarstider och överföra mindre data.

Azure IoT Edge-moduler distribueras till enheten från ett behållarregister. När du skapar en avbildning från din modell, lagras den i behållarregister för arbetsytan.

> [!IMPORTANT]
> Informationen i det här avsnittet förutsätter att du redan är bekant med Azure IoT Hub och Azure IoT Edge-moduler. Del av informationen i det här avsnittet är specifika för Azure Machine Learning-tjänsten, sker merparten av processen att distribuera till en edge-enhet i Azure IoT-tjänster.
>
> Om du inte känner till Azure IoT [grunderna i Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/) och [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) grundläggande information. Använd andra länkarna i det här avsnittet för mer information om specifika åtgärder.

### <a name="set-up-your-environment"></a>Konfigurera din miljö

* En utvecklingsmiljö. Mer information finns i den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.

* En [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen.

* En tränad modell. Ett exempel på hur du tränar en modell finns i den [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentet. En förtränade modellen är tillgänglig på den [AI-verktyg för Azure IoT Edge GitHub-lagringsplatsen](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> Hämta autentiseringsuppgifter för registret

För att distribuera en IoT Edge-modul till din enhet, måste autentiseringsuppgifterna för behållarregistret som Azure Machine Learning-tjänsten lagrar docker-avbildningar i i Azure IoT.

Du kan hämta autentiseringsuppgifterna på två sätt:

+ **I Azure-portalen**:

  1. Logga in på [Azure Portal](https://portal.azure.com/signin/index).

  1. Gå till din arbetsyta för Azure Machine Learning-tjänsten och välj __översikt__. Gå till behållaren för registerinställningarna genom att välja den __registret__ länk.

     ![En bild av registerposten behållare](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Välj en gång i behållarregistret **åtkomstnycklar** och sedan aktivera administratörsanvändare.

     ![En bild av skärmen för åtkomst-nycklar](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Spara värdena för **inloggningsserver**, **användarnamn**, och **lösenord**.

+ **Med en Python-skriptet**:

  1. Använd följande skript i Python efter koden som du körde ovan för att skapa en behållare:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Spara värdena för ContainerURL, servernamn, användarnamn och lösenord.

     Dessa autentiseringsuppgifter krävs för att tillhandahålla IoT Edge Enhetsåtkomst till avbildningar i ditt privata behållarregister.

### <a name="prepare-the-iot-device"></a>Förbereda IoT-enhet

Registrera din enhet med Azure IoT Hub och sedan installera IoT Edge-körningen på enheten. Om du inte är bekant med den här processen finns i [snabbstarten: Distribuera din första IoT Edge-modul till en enhet med Linux x64](../../iot-edge/quickstart-linux.md).

Andra metoder för att registrera en enhet är:

* [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Distribuera modellen till enheten

För att distribuera modellen till enheten, använder du informationen i registret som samlats in den [hämta autentiseringsuppgifter för container-registret](#getcontainer) med distribution av principmodul steg för IoT Edge-moduler. Till exempel när [distribuera Azure IoT Edge-moduler från Azure portal](../../iot-edge/how-to-deploy-modules-portal.md), måste du konfigurera den __registerinställningar__ för enheten. Använd den __inloggningsserver__, __användarnamn__, och __lösenord__ för behållarregistret arbetsyta.

Du kan också distribuera med hjälp av [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) och [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Rensa

Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.

Om du vill ta bort en avbildning, använder `image.delete()`.

Ta bort registrerade modellen genom att använda `model.delete()`.

Mer information finns i referensdokumentationen för [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--), och [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Nästa steg

* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Hur du kör batch-förutsägelser](how-to-run-batch-predictions.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Azure Machine Learning service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Använda Azure Machine Learning-tjänsten med Azure-nätverk](how-to-enable-virtual-network.md)
* [Metodtips för att bygga rekommendationssystem](https://github.com/Microsoft/Recommenders)
* [Skapa en i realtid rekommendation API på Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
