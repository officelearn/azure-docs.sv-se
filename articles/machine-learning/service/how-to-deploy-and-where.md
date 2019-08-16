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
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c0c3ade3fd089a4819b8836b07e249fc32c06e0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543606"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Lär dig hur du distribuerar din Machine Learning-modell som en webb tjänst i Azure-molnet, eller för att IoT Edge enheter.

Arbets flödet liknar oavsett [var du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbered för distribution (ange till gångar, användning, Compute Target).
1. Distribuera modellen till Compute-målet.
1. Testa den distribuerade modellen, även kallad webb tjänst.

Mer information om de begrepp som ingår i distributions arbets flödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

- En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i [skapa en Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md).

- En modell. Om du inte har en tränad modell kan du använda modell & beroende filer som finns i [den här](https://aka.ms/azml-deploy-cloud)självstudien.

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)eller [Azure Machine Learning Visual Studio Code-tillägget](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Anslut till din arbets yta

Följande kod visar hur du ansluter till en Azure Machine Learning service-arbetsyta med hjälp av information som cachelagras i den lokala utvecklings miljön:

**Använda SDK: n**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Mer information om hur du använder SDK för att ansluta till en arbets yta finns i [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

**Använda CLI**

När du använder CLI använder du `-w` parametern eller `--workspace-name` för att ange arbets ytan för kommandot.

**Använda VS Code**

När du använder VS Code väljs arbets ytan med ett grafiskt gränssnitt. Mer information finns i [distribuera och hantera modeller](how-to-vscode-tools.md#deploy-and-manage-models) i vs Code Extension-dokumentationen.

## <a id="registermodel"></a>Registrera din modell

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. Efter registreringen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som har registrerats.

> [!TIP]
> När du registrerar en modell, anger du antingen en sökväg till en moln plats (från en tränings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [Vad är get_model_path](#what-is-get_model_path).

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller kan komma från någon annan stans. Följande exempel visar hur du registrerar en modell:

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från en experiment körning

Kodfragmenten i det här avsnittet visar hur man registrerar en modell från en utbildnings körning:

> [!IMPORTANT]
> De här kodfragmenten förutsätter att du tidigare har utfört en tränings körning och har åtkomst `run` till objektet (SDK-exempel) eller kör ID-värde (CLI-exempel). Mer information om tränings modeller finns i [skapa och använda beräknings mål för modell träning](how-to-set-up-training-targets.md).

+ **Använda SDK: n**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  `model_path` Refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `model_path` du den katalog som innehåller filerna.

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` Refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du den katalog som innehåller filerna.

+ **Använda VS Code**

  Registrera modeller med hjälp av modell filer eller mappar med [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) -tillägget.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

Du kan registrera en modell genom att ange en **lokal sökväg** till modellen. Du kan antingen ange en mapp eller en enskild fil. Du kan använda den här metoden för att registrera både modeller som har tränats med Azure Machine Learning tjänst och sedan laddats ned eller modeller som har tränats utanför Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **ONNX-exempel med python SDK:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Om du vill inkludera flera filer i modell registreringen anger `model_path` du den katalog som innehåller filerna.

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Om du vill inkludera flera filer i modell registreringen anger `-p` du den katalog som innehåller filerna.

**Tids uppskattning**: Cirka 10 sekunder.

Mer information finns i referensdokumentationen för den [Modellklass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller som har tränats utanför Azure Machine Learning-tjänsten finns i [distribuera en befintlig modell](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Välj ett beräknings mål

Följande beräknings mål, eller beräknings resurser, kan användas som värd för din webb tjänst distribution. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Förbered distributionen

Distribution av modellen kräver flera saker:

* Ett __Entry-skript__. Det här skriptet accepterar begär Anden, visar begäran med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > Start skriptet är bara för din modell. den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    > Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också omvandla svaret innan det returneras till klienten.

    > [!IMPORTANT]
    > Azure Machine Learning SDK tillhandahåller inte ett sätt för webb tjänst-eller IoT Edge distributioner för åtkomst till dina data lager eller data uppsättningar. Om du behöver distribuerad modell för att komma åt data som lagras utanför distributionen, t. ex. i ett Azure Storage konto, måste du utveckla en anpassad kod med hjälp av relevant SDK. Till exempel [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python).
    >
    > Ett annat alternativ som kan fungera för ditt scenario är [batch](how-to-run-batch-predictions.md)-förutsägelser, vilket ger åtkomst till data lager när poäng.

* **Beroenden**, till exempel hjälp skript eller python/Conda-paket som krävs för att köra registrerings skriptet eller modellen

* __Distributions konfigurationen__ för det beräknings mål som är värd för den distribuerade modellen. Den här konfigurationen beskriver saker som minnes-och processor krav som krävs för att köra modellen.

Dessa entiteter kapslas in i en konfiguration för en __konfiguration__och en __distributions konfiguration__. Konfigurations konfigurationen refererar till Start skriptet och andra beroenden. Dessa konfigurationer definieras program mässigt när du använder SDK och som JSON-filer när du använder CLI för att utföra distributionen.

### <a id="script"></a> 1. Definiera ditt Entry-skript & beroenden

Inmatnings skriptet tar emot data som skickats till en distribuerad webb tjänst och skickar dem till modellen. Det tar sedan svaret som returneras av modellen och returnerar klienten. **Skriptet är bara för din modell**. den måste förstå de data som modellen förväntar sig och returnerar.

Skriptet innehåller två funktioner som läser in och kör modellen:

* `init()`: Den här funktionen laddar normalt modellen till ett globalt objekt. Den här funktionen körs bara en gång när Docker-behållaren för webb tjänsten startas.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Indata och utdata till körningen använder vanligt vis JSON för serialisering och avserialisering. Du kan också arbeta med rå data för rå data. Du kan transformera data innan du skickar dem till modellen eller innan du återgår till-klienten.

#### <a name="what-is-get_model_path"></a>Vad är get_model_path?

När du registrerar en modell anger du ett modell namn som används för att hantera modellen i registret. Du kan använda det här namnet med [modellen. get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hämta sökvägen till modell filen (erna) i det lokala fil systemet. Om du registrerar en mapp eller en samling filer, returnerar detta API sökvägen till katalogen som innehåller filerna.

När du registrerar en modell ger du den ett namn som motsvarar var modellen placeras, antingen lokalt eller under tjänst distributionen.

I exemplet nedan returneras en sökväg till en enskild fil som heter `sklearn_mnist_model.pkl` (som har registrerats med namnet `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-schema-generation"></a>Valfritt Automatisk schema generering

För att automatiskt generera ett schema för webb tjänsten, ange ett exempel på indata och/eller utdata i konstruktorn för en av de definierade typ objekten och typen och exemplet används för att automatiskt skapa schemat. Azure Machine Learning-tjänsten skapar sedan en [openapi](https://swagger.io/docs/specification/about/) -specifikation (Swagger) för webb tjänsten under distributionen.

Följande typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* standard python-objekt

Om du vill använda schema generering inkluderar `inference-schema` du paketet i din Conda-miljö fil.

##### <a name="example-dependencies-file"></a>Exempel beroende fil

Följande YAML är ett exempel på en fil för Conda-beroenden som kan vara en härledning:

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

##### <a name="example-entry-script"></a>Exempel på post skript

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

Följande exempel visar hur du definierar indata som en `<key: value>` ord lista med hjälp av en Dataframe. Den här metoden stöds för att konsumera den distribuerade webb tjänsten från Power BI ([Läs mer om hur du använder webb tjänsten från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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

<a id="binary"></a>

#### <a name="binary-data"></a>Binära data

Om din modell accepterar binära data, till exempel en avbildning måste du ändra den `score.py` filen användes för distributionen för att godkänna raw HTTP-förfrågningar. Om du vill acceptera rå data använder `AMLRequest` du klassen i ditt Entry-skript och `@rawhttp` lägger till decorator `run()` i funktionen.

Här är ett exempel på en `score.py` som accepterar binära data:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` Klassen är`azureml.contrib` i namn området. Saker i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Därför bör allt i det här namn området betraktas som en för hands version och stöds inte fullt ut av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Resurs delning mellan ursprung (CORS)

Resurs delning mellan ursprung är ett sätt att tillåta att resurser på en webb sida begärs från en annan domän. CORS fungerar baserat på HTTP-huvuden som skickats med klientbegäran och returnerades med tjänst svaret. Mer information om CORS och giltiga huvuden finns i [resurs delning mellan ursprung](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) på wikipedia.

Om du vill konfigurera modell distributionen så att den stöder CORS `AMLResponse` använder du klassen i ditt Entry-skript. Med den här klassen kan du ange huvuden för objektet Response.

I följande exempel anges `Access-Control-Allow-Origin` rubriken för svaret från Entry-skriptet:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` Klassen är`azureml.contrib` i namn området. Saker i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Därför bör allt i det här namn området betraktas som en för hands version och stöds inte fullt ut av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definiera din InferenceConfig

Konfigurations konfigurationen beskriver hur du konfigurerar modellen för att göra förutsägelser. Den här konfigurationen ingår inte i ditt Entry-skript. den refererar till ditt Entry-skript och används för att hitta alla resurser som krävs för distributionen. Den används senare när modellen distribueras.

Följande exempel visar hur du skapar en konfigurations konfiguration. Den här konfigurationen anger körnings miljön, start skriptet och (valfritt) Conda-miljöfilen:

```python
from azureml.core.model import InferenceConfig

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

Innan du distribuerar måste du definiera distributions konfigurationen. __Distributions konfigurationen är specifika för det beräknings mål som ska vara värd för webb tjänsten__. När du distribuerar lokalt måste du till exempel ange den port där tjänsten accepterar begär Anden. Distributions konfigurationen ingår inte i ditt Entry-skript. Den används för att definiera egenskaperna för det beräknings mål som ska vara värd för modell-och registrerings skriptet.

Du kan också behöva skapa beräknings resursen. Till exempel, om du inte redan har en Azure Kubernetes-tjänst som är kopplad till din arbets yta.

Följande tabell innehåller ett exempel på hur du skapar en distributions konfiguration för varje beräknings mål:

| Beräkningsmål | Exempel på distributions konfiguration |
| ----- | ----- |
| Lokala | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container-instans | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Var och en av dessa klasser för lokala, ACI och AKS webb tjänster kan importeras från `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> Innan du distribuerar din modell som en tjänst kanske du vill profilera den för att fastställa optimala processor-och minnes krav. Du kan profilera din modell med antingen SDK eller CLI. Mer information finns i [profilen profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) och [AZ ml modell profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) .
>
> Modell profilerings resultaten genereras som ett `Run` -objekt. Mer information finns i klass referens för [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

## <a name="deploy-to-target"></a>Distribuera till mål

Distributionen använder distributions konfigurationen för konfigurations konfiguration för att distribuera modellerna. Distributions processen är liknande oavsett beräknings målet. Distribution till AKS är något annorlunda, eftersom du måste ange en referens till AKS-klustret.

### <a id="local"></a>Lokal distribution

Om du vill distribuera lokalt måste du ha Docker installerat på den lokala datorn.

#### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

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

### <a id="notebookvm"></a>NotebookVM-webbtjänst (DEVTEST)

Se [distribuera en modell till Notebook VM](how-to-deploy-local-container-notebook-vm.md): ar.

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Se [distribuera till Azure Container instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes service (DEVTEST & produktion)

Se [distribuera till Azure Kubernetes-tjänsten](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webb tjänst tillhandahåller en REST API, så att du kan skapa klient program på flera olika programmeringsspråk. Om du har aktiverat Key Authentication för tjänsten måste du ange en tjänst nyckel som en token i ditt begär ande huvud.
Om du har aktiverat token-autentisering för tjänsten måste du ange en Azure Machine Learning JWT-token som Bearer-token i ditt begär ande huvud.

> [!TIP]
> Du kan hämta schema-JSON-dokumentet när du har distribuerat tjänsten. Använd [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) från den distribuerade webb tjänsten, till exempel `service.swagger_uri`, för att hämta URI: n till den lokala webb tjänstens Swagger-fil.

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

### <a name="web-service-schema-openapi-specification"></a>Webb tjänst schema (OpenAPI-specifikation)

Om du använde den automatiska schema genereringen med distributionen kan du hämta adressen till OpenAPI-specifikationen för tjänsten med hjälp av [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Till exempel `print(service.swagger_uri)`. Använd en GET-begäran (eller öppna URI i en webbläsare) för att hämta specifikationen.

Följande JSON-dokument är ett exempel på ett schema (OpenAPI-specifikation) som genereras för en distribution:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Mer information om specifikationen finns i den [öppna API](https://swagger.io/specification/)-specifikationen.

Ett verktyg som kan skapa klient bibliotek från specifikationen finns i [Swagger-CODEGEN](https://github.com/swagger-api/swagger-codegen).

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

Fler exempel projekt och exempel finns i följande exempel databaser:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

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

