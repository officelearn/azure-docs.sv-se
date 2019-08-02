---
title: Hur och var modeller ska distribueras
titleSuffix: Azure Machine Learning service
description: 'Lär dig hur och var du distribuerar dina Azure Machine Learning tjänst modeller, inklusive: Azure Container Instances, Azure Kubernetes service, Azure IoT Edge och fält programmerings bara grindar.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: c7c2ba104b4d528cd3f8443e6f5615aa6ab3e672
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720375"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Lär dig hur du distribuerar din Machine Learning-modell som en webb tjänst i Azure-molnet, eller för att IoT Edge enheter. 

Arbets flödet liknar oavsett [var du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbered för distribution (ange till gångar, användning, beräknings mål)
1. Distribuera modellen till Compute-målet.
1. Testa den distribuerade modellen, även kallad webb tjänst.

Mer information om de begrepp som ingår i distributions arbets flödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

- En modell. Om du inte har en tränad modell kan du använda modell & beroende filer som finns i [den här](https://aka.ms/azml-deploy-cloud)självstudien.

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)eller [Azure Machine Learning Visual Studio Code-tillägget](how-to-vscode-tools.md).

## <a id="registermodel"></a>Registrera din modell

En logisk behållare för en registrerad modell för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. Efter registreringen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller kan komma från någon annan stans. Följande exempel visar hur du registrerar en modell från en fil:

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från en experiment körning

+ **Scikit – lär dig hur du använder SDK: n**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Om du vill inkludera flera filer i modell registreringen anger `model_path` du den katalog som innehåller filerna.

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  > [!TIP]
  > Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du den katalog som innehåller filerna.

+ **Använda VS Code**

  Registrera modeller med hjälp av modell filer eller mappar med [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) -tillägget.

### <a name="register-an-externally-created-model"></a>Registrera en externt skapad modell

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Du kan registrera en externt skapad modell genom att ange en **lokal sökväg** till modellen. Du kan antingen ange en mapp eller en enskild fil.

+ **ONNX-exempel med python SDK:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > Om du vill inkludera flera filer i modell registreringen anger `model_path` du den katalog som innehåller filerna.

+ **Använda CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Om du vill inkludera flera filer i modell registreringen anger `-p` du den katalog som innehåller filerna.

**Tids uppskattning**: Cirka 10 sekunder.

Mer information finns i referensdokumentationen för den [Modellklass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller som har tränats utanför Azure Machine Learning-tjänsten finns i [distribuera en befintlig modell](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Välj ett beräknings mål

Följande beräknings mål, eller beräknings resurser, kan användas som värd för din webb tjänst distribution. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Förbered distributionen

Om du vill distribuera som en webb tjänst måste du skapa en konfiguration för`InferenceConfig`konfigurations härledning () och distribution. Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för förutsägelse, oftast på produktions data. I konfigurations filen för konfigurations filen anger du de skript och beroenden som behövs för att hantera din modell. I distributions konfigurationen anger du information om hur du hanterar modellen på beräknings målet.

> [!IMPORTANT]
> Azure Machine Learning SDK tillhandahåller inte ett sätt för webb tjänst-eller IoT Edge distributioner för åtkomst till dina data lager eller data uppsättningar. Om du behöver distribuerad modell för att komma åt data som lagras utanför distributionen, t. ex. i ett Azure Storage konto, måste du utveckla en anpassad kod med hjälp av relevant SDK. Till exempel [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python).
>
> Ett annat alternativ som kan fungera för ditt scenario är [batch](how-to-run-batch-predictions.md)-förutsägelser, vilket ger åtkomst till data lager när poäng.

### <a id="script"></a> 1. Definiera ditt Entry-skript & beroenden

Inmatnings skriptet tar emot data som skickats till en distribuerad webb tjänst och skickar dem till modellen. Det tar sedan svaret som returneras av modellen och returnerar klienten. **Skriptet är bara för din modell**. den måste förstå de data som modellen förväntar sig och returnerar.

Skriptet innehåller två funktioner som läser in och kör modellen:

* `init()`: Den här funktionen laddar normalt modellen till ett globalt objekt. Den här funktionen körs bara en gång när Docker-behållaren för webb tjänsten startas.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Indata och utdata till körningen använder vanligt vis JSON för serialisering och avserialisering. Du kan också arbeta med rå data för rå data. Du kan transformera data innan du skickar dem till modellen eller innan du återgår till-klienten.

#### <a name="what-is-getmodelpath"></a>Vad är get_model_path?

När du registrerar en modell anger du ett modell namn som används för att hantera modellen i registret. Du kan använda det här namnet med [modellen. get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hämta sökvägen till modell filen (erna) i det lokala fil systemet. Om du registrerar en mapp eller en samling filer, returnerar detta API sökvägen till katalogen som innehåller filerna.

När du registrerar en modell ger du den ett namn som motsvarar var modellen placeras, antingen lokalt eller under tjänst distributionen.

I exemplet nedan returneras en sökväg till en enskild fil som heter `sklearn_mnist_model.pkl` (som har registrerats med namnet `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-swagger-schema-generation"></a>Valfritt Automatisk generering av Swagger-schema

För att automatiskt generera ett schema för webb tjänsten, ange ett exempel på indata och/eller utdata i konstruktorn för en av de definierade typ objekten och typen och exemplet används för att automatiskt skapa schemat. Azure Machine Learning-tjänsten skapar sedan en [openapi](https://swagger.io/docs/specification/about/) -specifikation (Swagger) för webb tjänsten under distributionen.

Följande typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* standard python-objekt

Om du vill använda schema generering inkluderar `inference-schema` du paketet i din Conda-miljö fil. Följande exempel används `[numpy-support]` eftersom Entry-skriptet använder en numpy-parameter typ: 

#### <a name="example-dependencies-file"></a>Exempel beroende fil
Följande YAML är ett exempel på en fil för Conda-beroenden.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Om du vill använda automatisk schema generering **måste** ditt Entry-skript importera `inference-schema` paketen. 

Definiera exempel formaten indata och utdata i `input_sample` variablerna och `output_sample` som representerar formatet för begäran och svar för webb tjänsten. Använd de här exemplen i funktionen indata och utdata dekoratörer `run()` i funktionen. I scikit – läras exemplet nedan används schema generering.

> [!TIP]
> När du har distribuerat tjänsten använder `swagger_uri` du egenskapen för att hämta schema-JSON-dokumentet.

#### <a name="example-entry-script"></a>Exempel på post skript

Följande exempel visar hur du accepterar och returnerar JSON-data:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Exempel skript med indatamängds ineffekt (support förbrukning från Power BI)

Följande exempel visar hur du definierar indata som < nyckel: värde > ord lista med Dataframe. Den här metoden stöds för att konsumera den distribuerade webb tjänsten från Power BI ([Läs mer om hur du använder webb tjänsten från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
Fler exempel skript finns i följande exempel:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Poäng mot binära data: [Använda en webb tjänst](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definiera din InferenceConfig

Konfigurations konfigurationen beskriver hur du konfigurerar modellen för att göra förutsägelser. Följande exempel visar hur du skapar en konfigurations konfiguration. Den här konfigurationen anger körnings miljön, start skriptet och (valfritt) Conda-miljöfilen:

```python
inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Mer information finns i klass referens för [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Information om hur du använder en anpassad Docker-avbildning med konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>CLI-exempel på InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet innehåller konfigurationen följande objekt:

* Den här modellen kräver python
* [Entry-skriptet](#script), som används för att hantera webb förfrågningar som skickas till den distribuerade tjänsten
* Den Conda-fil som beskriver de python-paket som behövs för att kunna utföra en härledning

Information om hur du använder en anpassad Docker-avbildning med konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definiera distributions konfigurationen

Innan du distribuerar måste du definiera distributions konfigurationen. __Distributions konfigurationen är specifika för det beräknings mål som ska vara värd för webb tjänsten__. När du distribuerar lokalt måste du till exempel ange den port där tjänsten accepterar begär Anden.

Du kan också behöva skapa beräknings resursen. Till exempel, om du inte redan har en Azure Kubernetes-tjänst som är kopplad till din arbets yta.

Följande tabell innehåller ett exempel på hur du skapar en distributions konfiguration för varje beräknings mål:

| Beräkningsmål | Exempel på distributions konfiguration |
| ----- | ----- |
| Lokala | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container-instans | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

> [!TIP]
> Innan du distribuerar din modell som en tjänst kanske du vill profilera den för att fastställa optimala processor-och minnes krav. Du kan profilera din modell med antingen SDK eller CLI. Mer information finns i [profilen profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) och [AZ ml modell profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) .
>
> Modell profilerings resultaten genereras som ett `Run` -objekt. Mer information finns i klass referens för [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

## <a name="deploy-to-target"></a>Distribuera till mål

Distributionen använder distributions konfigurationen för konfiguration av konfigurations konfiguration för att distribuera modell (er). Distributions processen är liknande oavsett beräknings målet. Distribution till AKS är något annorlunda, eftersom du måste ange en referens till AKS-klustret.

### <a id="local"></a>Lokal distribution

Om du vill distribuera lokalt måste du ha **Docker installerat** på den lokala datorn.

#### <a name="using-the-sdk"></a>Med SDK

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information finns i referens dokumentationen för [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)och WebService. [](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)

#### <a name="using-the-cli"></a>Använda CLI

Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i distributions referens för [AZ ml-modellen](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Se [distribuera till Azure Container instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes service (DEVTEST & produktion)

Se [distribuera till Azure Kubernetes-tjänsten](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webb tjänst tillhandahåller en REST API, så att du kan skapa klient program på flera olika programmeringsspråk. Om du har aktiverat Key Authentication för tjänsten måste du ange en tjänst nyckel som en token i ditt begär ande huvud.
Om du har aktiverat token-autentisering för tjänsten måste du ange en Azure Machine Learning JWT-token som Bearer-token i ditt begär ande huvud.

### <a name="request-response-consumption"></a>Användning av begäran-svar

Här är ett exempel på hur du anropar din tjänst i python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Mer information finns i [skapa klient program för att använda](how-to-consume-web-service.md)WebServices.


### <a id="azuremlcompute"></a>Batch-härledning
Azure Machine Learning beräknings mål skapas och hanteras av Azure Machine Learnings tjänsten. De kan användas för batch förutsägelse från Azure Machine Learning pipeliner.

En genom gång av batch-härledning med Azure Machine Learning Compute finns i artikeln [köra batch](how-to-run-batch-predictions.md) -förutsägelser.

### <a id="iotedge"></a>IoT Edge-härledning
Stöd för att distribuera till Edge är i för hands version. Mer information finns i artikeln [distribuera Azure Machine Learning som en IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Uppdatera webb tjänster

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Kontinuerlig modell distribution 

Du kan distribuera modeller kontinuerligt med Machine Learning-tillägget för [Azure-DevOps](https://azure.microsoft.com/services/devops/). Genom att använda Machine Learning-tillägget för Azure-DevOps kan du utlösa en distributions pipeline när en ny maskin inlärnings modell registreras i Azure Machine Learning service Workspace. 

1. Registrera dig för [Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)-pipelines, som möjliggör kontinuerlig integrering och leverans av ditt program till alla plattformar/moln som är möjliga. Azure-pipeliner [skiljer sig från ml](concept-ml-pipelines.md#compare)-pipeliner. 

1. [Skapa ett Azure DevOps-projekt.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installera [Machine Learning-tillägget för Azure](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) -pipeliner 

1. Använd __tjänst anslutningar__ för att konfigurera en tjänstens huvud namns anslutning till din Azure Machine Learning tjänst arbets yta för att få åtkomst till alla dina artefakter. Gå till projekt inställningar, klicka på tjänst anslutningar och välj Azure Resource Manager.

    ![Visa-tjänst-anslutning](media/how-to-deploy-and-where/view-service-connection.png) 

1. Definiera AzureMLWorkspace som omfångs __nivå__ och fyll i de efterföljande parametrarna.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. För att kontinuerligt distribuera din Machine Learning-modell med Azure-pipelines väljer du __släpp__i pipelines. Lägg till en ny artefakt, Välj AzureML modell artefakt och den tjänst anslutning som skapades i föregående steg. Välj modell och version för att utlösa en distribution. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Aktivera modell utlösaren i modell artefakten. Genom att aktivera utlösaren, varje gång den angivna versionen (dvs. den senaste versionen) av modellen registreras i din arbets yta, en Azure DevOps release-pipeline utlöses. 

    ![Aktivera-modell-utlösare](media/how-to-deploy-and-where/set-modeltrigger.png)

För exempel projekt och exempel kan du ta [en titt på MLOps](https://github.com/Microsoft/MLOps) -lagringsplatsen

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.
Ta bort registrerade modellen genom att använda `model.delete()`.

Mer information finns i referens dokumentationen för WebService [. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)och [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Nästa steg
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)

