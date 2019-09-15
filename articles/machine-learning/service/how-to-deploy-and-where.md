---
title: Hur och var modeller ska distribueras
titleSuffix: Azure Machine Learning service
description: Lär dig hur och var du kan distribuera Azure Machine Learning tjänst modeller, inklusive Azure Container Instances, Azure Kubernetes service, Azure IoT Edge och fält-programmerbara grind mat ris.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: ff3a19a543f87833420f585bbdf7891cc7589746
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997194"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Lär dig hur du distribuerar din Machine Learning-modell som en webb tjänst i Azure-molnet eller Azure IoT Edge enheter.

Arbets flödet är ungefär oavsett [var du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbered för distribution. (Ange till gångar, användning, Compute-mål.)
1. Distribuera modellen till Compute-målet.
1. Testa den distribuerade modellen, även kallad en webb tjänst.

Mer information om de begrepp som ingår i distributions arbets flödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

- En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i [skapa en Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md).

- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).

- [Azure CLI-tillägget för Machine Learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Anslut till din arbets yta

Följande kod visar hur du ansluter till en Azure Machine Learning service-arbetsyta med hjälp av information som cachelagras i den lokala utvecklings miljön:

+ **Använda SDK: n**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Mer information om hur du använder SDK för att ansluta till en arbets yta finns i dokumentationen [för Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Använda CLI**

   När du använder CLI använder du `-w` parametern eller `--workspace-name` för att ange arbets ytan för kommandot.

+ **Använda VS Code**

   När du använder VS Code väljer du arbets ytan med hjälp av ett grafiskt gränssnitt. Mer information finns i [distribuera och hantera modeller](how-to-vscode-tools.md#deploy-and-manage-models) i vs Code Extension-dokumentationen.

## <a id="registermodel"></a>Registrera din modell

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en moln plats (från en utbildnings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. Den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [Vad är get_model_path?](#what-is-get_model_path).

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller från någon annan stans. Följande exempel visar hur du registrerar en modell.

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från en experiment körning

Kodfragmenten i det här avsnittet visar hur du registrerar en modell från en utbildnings körning:

> [!IMPORTANT]
> Om du vill använda dessa kodfragment måste du tidigare ha utfört en utbildning och du måste ha åtkomst till `Run` objektet (SDK-exempel) eller värdet körnings-ID (CLI-exempel). Mer information om tränings modeller finns i [Konfigurera beräknings mål för modell träning](how-to-set-up-training-targets.md).

+ **Använda SDK: n**

  När du använder SDK för att träna en modell kan du ta emot antingen ett [körnings](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) objekt eller ett [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) -objekt, beroende på hur du tränade modellen. Varje-objekt kan användas för att registrera en modell som skapats av en experiment körning.

  + Registrera en modell från ett `azureml.core.Run` objekt:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `model_path` du sökvägen till en mapp som innehåller filerna. Mer information finns i dokumentationen för [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Registrera en modell från ett `azureml.train.automl.run.AutoMLRun` objekt:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    I det här exemplet `metric` har parametrarna och `iteration` inte angetts, så iterationen med bästa primära mått kommer att registreras. `model_id` Värdet som returneras från körningen används i stället för ett modell namn.

    Mer information finns i dokumentationen för [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du sökvägen till en mapp som innehåller filerna.

+ **Använda VS Code**

  Registrera modeller med hjälp av en modell fil eller mappar med hjälp av [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) -tillägget.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

Du kan registrera en modell genom att ange den lokala sökvägen till modellen. Du kan ange sökvägen till antingen en mapp eller en enskild fil. Du kan använda den här metoden för att registrera modeller som har tränats med Azure Machine Learning tjänsten och sedan laddats ned. Du kan också använda den här metoden för att registrera modeller som har tränats utanför Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Använda SDK och ONNX**

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

  Om du vill inkludera flera filer i modell registreringen anger `model_path` du sökvägen till en mapp som innehåller filerna.

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Om du vill inkludera flera filer i modell registreringen anger `-p` du sökvägen till en mapp som innehåller filerna.

**Tids uppskattning**: Cirka 10 sekunder.

Mer information finns i dokumentationen för [modell klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller som har tränats utanför Azure Machine Learning-tjänsten finns i [distribuera en befintlig modell](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Välj ett beräknings mål

Du kan använda följande beräknings mål eller beräknings resurser för att vara värd för webb tjänst distributionen:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Förbered distributionen

För att distribuera modellen behöver du följande objekt:

* **Ett Entry-skript**. Det här skriptet accepterar begär Anden, visar förfrågningarna med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > * Start skriptet är bara för din modell. Den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    >   Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också transformera svaret innan det returneras till klienten.
    >
    > * Azure Machine Learning SDK är inte ett sätt för webb tjänster eller IoT Edge distributioner för åtkomst till data lagret eller data uppsättningar. Om den distribuerade modellen behöver åtkomst till data som lagras utanför distributionen, t. ex. data i ett Azure Storage-konto, måste du utveckla en anpassad kod lösning med hjälp av relevant SDK. Till exempel [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python).
    >
    >   Ett alternativ som kan fungera för ditt scenario är [batch-förutsägelse](how-to-run-batch-predictions.md), vilket ger åtkomst till data lager under poängsättningen.

* **Beroenden**, t. ex. hjälp skript eller python/Conda-paket som krävs för att köra start skriptet eller modellen.

* **Distributions konfigurationen** för det beräknings mål som är värd för den distribuerade modellen. Den här konfigurationen beskriver saker som minnes-och processor krav som krävs för att köra modellen.

Dessa objekt kapslas in i en *konfiguration* för konfiguration och *distribution*. Konfigurations konfigurationen refererar till Start skriptet och andra beroenden. Du definierar dessa konfigurationer program mässigt när du använder SDK: n för att utföra distributionen. Du definierar dem i JSON-filer när du använder CLI.

### <a id="script"></a> 1. Definiera ditt post skript och beroenden

Inmatnings skriptet tar emot data som skickats till en distribuerad webb tjänst och skickar dem till modellen. Det tar sedan svaret som returneras av modellen och returnerar klienten. *Skriptet är bara för din modell*. Den måste förstå de data som modellen förväntar sig och returnerar.

Skriptet innehåller två funktioner som läser in och kör modellen:

* `init()`: Den här funktionen laddar normalt modellen till ett globalt objekt. Den här funktionen körs bara en gång, när Docker-behållaren för webb tjänsten startas.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Indata och utdata för körningen använder vanligt vis JSON för serialisering och deserialisering. Du kan också arbeta med rå data för rå data. Du kan transformera data innan du skickar dem till modellen eller innan du returnerar den till klienten.

#### <a name="what-is-get_model_path"></a>Vad är get_model_path?

När du registrerar en modell anger du ett modell namn som används för att hantera modellen i registret. Du använder det här namnet med metoden [Model. get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hämta sökvägen till modell filen eller filerna i det lokala fil systemet. Om du registrerar en mapp eller en samling filer, returnerar detta API sökvägen till katalogen som innehåller filerna.

När du registrerar en modell ger du den ett namn. Namnet motsvarar var modellen placeras, antingen lokalt eller under tjänst distributionen.

> [!IMPORTANT]
> Om du använde automatisk maskin inlärning för att träna en modell, `model_id` används ett värde som modell namn. Ett exempel på hur man registrerar och distribuerar en modell som är utbildad med automatisk maskin inlärning finns i [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment) på GitHub.

I följande exempel returneras en sökväg till en enda fil som heter `sklearn_mnist_model.pkl` (som registrerades med namnet `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Valfritt Automatisk schema generering

För att automatiskt generera ett schema för webb tjänsten, ange ett exempel på indata och/eller utdata i konstruktorn för ett av de definierade typ objekten. Typen och exemplet används för att automatiskt skapa schemat. Azure Machine Learnings tjänsten skapar sedan en [openapi](https://swagger.io/docs/specification/about/) -specifikation (Swagger) för webb tjänsten under distributionen.

Dessa typer stöds för närvarande:

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

Om du vill använda automatisk schema generering måste ditt Entry-skript importera `inference-schema` paketen.

Definiera exempel formaten indata och utdata i `input_sample` variablerna och `output_sample` som representerar formatet för begäran och svar för webb tjänsten. Använd de här exemplen i funktionen indata och utdata dekoratörer `run()` i funktionen. Följande scikit – lära använder schema generering.

##### <a name="example-entry-script"></a>Exempel på post skript

Följande exempel visar hur du accepterar och returnerar JSON-data:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # Note that here "sklearn_regression_model.pkl" is the name of the model registered under.
    # This is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Följande exempel visar hur du definierar indata som en `<key: value>` ord lista med hjälp av en DataFrame. Den här metoden stöds för att konsumera den distribuerade webb tjänsten från Power BI. ([Läs mer om hur du använder webb tjänsten från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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
    # Replace model_name with your actual model name, if necessary.
    model_path = Model.get_model_path('model_name')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Fler exempel finns i följande skript:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Binära data

Om din modell accepterar binära data, till exempel en bild, måste du `score.py` ändra den fil som används för distributionen för att acceptera obehandlade HTTP-begäranden. Om du vill acceptera rå data använder `AMLRequest` du klassen i ditt Entry-skript och `@rawhttp` lägger till decorator `run()` i funktionen.

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` Klassen är`azureml.contrib` i namn området. Entiteter i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namn området bör betraktas som en för hands version som inte fullt ut stöds av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Resurs delning mellan ursprung (CORS)

Resurs delning mellan ursprung är ett sätt att tillåta att resurser på en webb sida begärs från en annan domän. CORS fungerar via HTTP-huvuden som skickas med klientbegäran och som returneras med tjänstens svar. Mer information om CORS och giltiga huvuden finns i [resurs delning mellan ursprung](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) i wikipedia.

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` Klassen är`azureml.contrib` i namn området. Entiteter i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namn området bör betraktas som en för hands version som inte fullt ut stöds av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definiera din InferenceConfig

Konfigurations konfigurationen beskriver hur du konfigurerar modellen för att göra förutsägelser. Den här konfigurationen ingår inte i ditt Entry-skript. Den refererar till ditt Entry-skript och används för att hitta alla resurser som krävs för distributionen. Den används senare när du distribuerar modellen.

Konfiguration av en konfigurations miljö kan använda Azure Machine Learning miljöer för att definiera de program beroenden som behövs för distributionen. Med miljöer kan du skapa, hantera och återanvända program beroenden som krävs för utbildning och distribution. I följande exempel visas hur du läser in en miljö från arbets ytan och sedan använder den med konfigurations konfigurationen:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Du kan också ange beroenden direkt utan att använda en miljö. Följande exempel visar hur du skapar en konfigurations konfiguration som laddar program varu beroenden från en Conda-fil:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Mer information finns i [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Class-dokumentationen.

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>CLI-exempel på InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet anger konfigurationen följande inställningar:

* Som modellen kräver python.
* [Entry-skriptet](#script), som används för att hantera webb förfrågningar som skickas till den distribuerade tjänsten.
* Den Conda-fil som beskriver de python-paket som behövs för att kunna användas.

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definiera distributions konfigurationen

Innan du distribuerar din modell måste du definiera distributions konfigurationen. *Distributions konfigurationen är specifika för det beräknings mål som ska vara värd för webb tjänsten.* Om du till exempel distribuerar en modell lokalt måste du ange den port där tjänsten accepterar begär Anden. Distributions konfigurationen ingår inte i ditt Entry-skript. Den används för att definiera egenskaperna för det beräknings mål som ska vara värd för modell-och registrerings skriptet.

Du kan också behöva skapa beräknings resursen, om du till exempel inte redan har en AKS-instans (Azure Kubernetes service) kopplad till din arbets yta.

Följande tabell innehåller ett exempel på hur du skapar en distributions konfiguration för varje beräknings mål:

| Beräkningsmål | Exempel på distributions konfiguration |
| ----- | ----- |
| Lokala | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasserna för lokala, Azure Container Instances-och AKS-webbtjänster kan importeras från `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilering

Innan du distribuerar din modell som en tjänst kanske du vill profilera den för att fastställa optimala processor-och minnes krav. Du kan använda antingen SDK eller CLI för att profilera din modell. I följande exempel visas hur du profilerar en modell med hjälp av SDK.

> [!IMPORTANT]
> När du använder profilering kan den konfiguration av den härledning som du anger inte referera till en Azure Machine Learning-miljö. Definiera i stället program beroenden genom att `conda_file` använda-parametern `InferenceConfig` för objektet.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(true)
profiling_results = profile.get_results()
print(profiling_results)
```

Den här koden visar ett resultat som liknar följande utdata:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Modell profilerings resultaten genereras som ett `Run` -objekt.

Information om hur du använder profilering från CLI finns i [AZ ml modell Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Mer information finns i följande dokument:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profil ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Fil schema för konfigurations härledning](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Distribuera till mål

Distributionen använder distributions konfigurationen för konfigurations konfiguration för att distribuera modellerna. Distributions processen är liknande oavsett beräknings målet. Distribution till AKS är något annorlunda eftersom du måste ange en referens till AKS-klustret.

### <a id="local"></a>Lokal distribution

Om du vill distribuera en modell lokalt måste du ha Docker installerat på den lokala datorn.

#### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information finns i dokumentationen för [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)och [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Använda CLI

Använd följande kommando om du vill distribuera en modell med hjälp av CLI. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i dokumentationen för [AZ ml-modellen Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Webb tjänst för notebook VM (dev/test)

Se [distribuera en modell till Notebook VM](how-to-deploy-local-container-notebook-vm.md): ar.

### <a id="aci"></a>Azure Container Instances (dev/test)

Se [distribuera till Azure Container instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes-tjänsten (utveckling/testning och produktion)

Se [distribuera till Azure Kubernetes-tjänsten](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webb tjänst tillhandahåller en REST API, så att du kan skapa klient program på flera olika programmeringsspråk.
Om du har aktiverat Key Authentication för tjänsten måste du ange en tjänst nyckel som en token i ditt begär ande huvud.
Om du har aktiverat token-autentisering för tjänsten måste du ange en Azure Machine Learning JWT-token som Bearer-token i ditt begär ande huvud.

> [!TIP]
> Du kan hämta schema-JSON-dokumentet när du har distribuerat tjänsten. Använd [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) från den distribuerade webb tjänsten (till exempel `service.swagger_uri`) för att hämta URI: n till den lokala webb tjänstens Swagger-fil.

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

Mer information finns i [skapa klient program för att använda webb tjänster](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Webb tjänst schema (OpenAPI-specifikation)

Om du har använt automatisk schema generering med din distribution kan du hämta adressen till OpenAPI-specifikationen för tjänsten med hjälp av [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Till exempel `print(service.swagger_uri)`.) Använd en GET-begäran eller öppna URI: n i en webbläsare för att hämta specifikationen.

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

Mer information finns i [openapi-specifikation](https://swagger.io/specification/).

Ett verktyg som kan skapa klient bibliotek från specifikationen finns i [Swagger-CODEGEN](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Batch-härledning
Azure Machine Learning beräknings mål skapas och hanteras av Azure Machine Learnings tjänsten. De kan användas för batch förutsägelse från Azure Machine Learning pipeliner.

En genom gång av batch-härledning med Azure Machine Learning Compute finns i [så här kör du batch-förutsägelser](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a>IoT Edge-härledning
Stöd för att distribuera till Edge är i för hands version. Mer information finns i [distribuera Azure Machine Learning som en IoT Edge modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Uppdatera webb tjänster

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Distribuera modeller kontinuerligt

Du kan distribuera modeller kontinuerligt med Machine Learning-tillägget för [Azure-DevOps](https://azure.microsoft.com/services/devops/). Du kan använda Machine Learning-tillägget för Azure-DevOps för att utlösa en distributions pipeline när en ny maskin inlärnings modell registreras i en Azure Machine Learning service-arbetsyta.

1. Registrera dig för [Azure-pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), som möjliggör kontinuerlig integrering och leverans av ditt program till vilken plattform eller moln som möjligt. (Observera att Azure-pipeliner inte är samma som [Machine Learning pipelines](concept-ml-pipelines.md#compare).)

1. [Skapa ett Azure DevOps-projekt.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installera [Machine Learning-tillägget för Azure-pipeliner](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Använd tjänst anslutningar för att konfigurera en tjänstens huvud namns anslutning till din Azure Machine Learning tjänst arbets yta så att du kan komma åt dina artefakter. Gå till projekt inställningar, Välj **tjänst anslutningar**och välj sedan **Azure Resource Manager**:

    [![Välj Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. I listan **omfattnings nivå** väljer du **AzureMLWorkspace**och anger sedan resten av värdena:

    ![Välj AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Om du vill distribuera din Machine Learning-modell kontinuerligt med hjälp av Azure-pipelines väljer du **version**under pipelines. Lägg till en ny artefakt och välj sedan den **azureml modell** artefakt och den tjänst anslutning som du skapade tidigare. Välj modell och version för att utlösa en distribution:

    [![Välj AzureML-modell](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Aktivera modell utlösaren i modell artefakten. När du aktiverar utlösaren utlöses en pipeline för Azure DevOps release varje gång den angivna versionen (det vill säga den senaste versionen) av modellen registreras i din arbets yta.

    [![Aktivera modell utlösaren](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Fler exempel projekt och exempel finns i följande exempel-databaser i GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Ladda ned en modell
Om du vill ladda ned din modell för att använda den i din egen körnings miljö kan du göra det med följande SDK/CLI-kommandon:

GRÄNSSNITTET
```python
model_path = Model(ws,'mymodel').download()
```

CLI
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="package-models"></a>Paket modeller

I vissa fall kanske du vill skapa en Docker-avbildning utan att distribuera modellen (om du till exempel planerar att [distribuera till Azure App Service](how-to-deploy-app-service.md)). Eller så kanske du vill ladda ned avbildningen och köra den på en lokal Docker-installation. Du kanske till och med vill ladda ned filerna som används för att bygga avbildningen, inspektera dem, ändra dem och bygga avbildningen manuellt.

Med modell paketering kan du göra dessa saker. Den hanterar alla till gångar som krävs för att vara värd för en modell som en webb tjänst och gör att du kan ladda ned antingen en helt inbyggd Docker-avbildning eller de filer som behövs för att skapa en. Det finns två sätt att använda modell förpackningar:

**Ladda ned en packad modell:** Hämta en Docker-avbildning som innehåller modellen och andra filer som behövs för att vara värd för den som en webb tjänst.

**Generera en Dockerfile:** Ladda ned Dockerfile, modell, Entry-skript och andra till gångar som behövs för att bygga en Docker-avbildning. Du kan sedan granska filerna eller göra ändringar innan du skapar avbildningen lokalt.

Båda paketen kan användas för att hämta en lokal Docker-avbildning.

> [!TIP]
> Att skapa ett paket liknar att distribuera en modell. Du använder en registrerad modell och en konfiguration för konfigurations störningar.

> [!IMPORTANT]
> Om du vill ladda ned en helt skapad avbildning eller skapa en avbildning lokalt måste du ha [Docker](https://www.docker.com) installerat i utvecklings miljön.

### <a name="download-a-packaged-model"></a>Ladda ned en packad modell

I följande exempel skapas en avbildning som är registrerad i Azure Container Registry för din arbets yta:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

När du har skapat ett paket kan du använda `package.pull()` för att hämta avbildningen till din lokala Docker-miljö. Utdata från det här kommandot visar namnet på bilden. Exempel: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

När du har laddat ned modellen använder `docker images` du kommandot för att lista de lokala avbildningarna:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Starta en lokal behållare baserat på den här avbildningen genom att använda följande kommando för att starta en namngiven behållare från gränssnittet eller kommando raden. Ersätt värdet med det bild-ID `docker images` som kommandot returnerade. `<imageid>`

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Det här kommandot startar den senaste versionen av avbildningen `myimage`med namnet. Den mappar den lokala port 6789 till porten i behållaren där webb tjänsten lyssnar (5001). Det tilldelar också namnet `mycontainer` till behållaren, vilket gör det enklare att stoppa behållaren. När behållaren har startats kan du skicka begär anden till `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Generera en Dockerfile och beroenden

I följande exempel visas hur du hämtar Dockerfile, modellen och andra till gångar som behövs för att bygga en avbildning lokalt. `generate_dockerfile=True` Parametern anger att du vill att filerna, inte en helt skapad avbildning, ska visas.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Den här koden laddar ned de filer som krävs för att bygga `imagefiles` avbildningen till katalogen. Dockerfile som ingår i de sparade filerna hänvisar till en bas avbildning som lagras i ett Azure Container Registry. När du skapar avbildningen på den lokala Docker-installationen måste du använda adressen, användar namnet och lösen ordet för att autentisera till registret. Använd följande steg för att bygga avbildningen med hjälp av en lokal Docker-installation:

1. Använd följande kommando för att autentisera Docker med Azure Container Registry från en Shell-eller kommando rads-session. Ersätt `<address>`, `<username>` `package.get_container_registry()`och medvärdenasomhämtadesav`<password>` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Använd följande kommando för att bygga avbildningen. Ersätt `<imagefiles>` med sökvägen till katalogen där `package.save()` filerna sparades.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Det här kommandot anger avbildningens namn `myimage`till.

Om du vill kontrol lera att avbildningen har skapats `docker images` använder du kommandot. Du bör se `myimage` avbildningen i listan:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Om du vill starta en ny behållare baserat på den här avbildningen använder du följande kommando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Det här kommandot startar den senaste versionen av avbildningen `myimage`med namnet. Den mappar den lokala port 6789 till porten i behållaren där webb tjänsten lyssnar (5001). Det tilldelar också namnet `mycontainer` till behållaren, vilket gör det enklare att stoppa behållaren. När behållaren har startats kan du skicka begär anden till `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Exempel klient för att testa den lokala behållaren

Följande kod är ett exempel på en python-klient som kan användas med behållaren:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Till exempel klienter i andra programmeringsspråk, se [använda modeller som distribueras som webb tjänster](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Stoppa Docker-behållaren

Om du vill stoppa behållaren använder du följande kommando från ett annat gränssnitt eller en annan kommando rad:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.
Ta bort registrerade modellen genom att använda `model.delete()`.

Mer information finns i dokumentationen för [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) och [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Nästa steg
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)

