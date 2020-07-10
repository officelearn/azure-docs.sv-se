---
title: Hur och var modeller ska distribueras
titleSuffix: Azure Machine Learning
description: Lär dig hur och var du kan distribuera Azure Machine Learning-modeller, inklusive Azure Container Instances, Azure Kubernetes service, Azure IoT Edge och fält-programmerbara grind mat ris.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.custom: seoapril2019, tracking-python
ms.openlocfilehash: 57e1ecb080d816898b862951846b15a4b5709e38
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146555"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuera modeller med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar din Machine Learning-modell som en webb tjänst i Azure-molnet eller Azure IoT Edge enheter.

Arbets flödet är ungefär oavsett [var du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbered distributionen. (Ange tillgångar, användning och beräkningsmål.)
1. Distribuera modellen till beräkningsmålet.
1. Testa den distribuerade modellen, även kallad en webb tjänst.

Mer information om de begrepp som ingår i distributions arbets flödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).

- [Azure CLI-tillägget för Machine Learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

Följande kod visar hur du ansluter till en Azure Machine Learning-arbetsyta med hjälp av information som cachelagras i den lokala utvecklings miljön:

+ **Med SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Mer information om hur du använder SDK för att ansluta till en arbets yta finns i dokumentationen [för Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Använda CLI**

   När du använder CLI använder du `-w` parametern eller `--workspace-name` för att ange arbets ytan för kommandot.

+ **Använda Visual Studio Code**

   När du använder Visual Studio Code väljer du arbets ytan med hjälp av ett grafiskt gränssnitt. Mer information finns i [distribuera och hantera modeller](how-to-manage-resources-vscode.md#endpoints) i Visual Studio Code Extension-dokumentationen.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrera din modell

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en moln plats (från en utbildnings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. Den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [hitta modell filer i ditt Entry-skript](#load-model-files-in-your-entry-script).

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller från någon annan stans. När du registrerar en modell kan du välja att ange metadata om modellen. De `tags` `properties` ord listor som du använder för en modell registrering kan sedan användas för att filtrera modeller.

Följande exempel visar hur du registrerar en modell.

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från en experiment körning

Kodfragmenten i det här avsnittet visar hur du registrerar en modell från en utbildnings körning:

> [!IMPORTANT]
> Om du vill använda dessa kodfragment måste du tidigare ha utfört en utbildning och du måste ha åtkomst till `Run` objektet (SDK-exempel) eller värdet körnings-ID (CLI-exempel). Mer information om tränings modeller finns i [Konfigurera beräknings mål för modell träning](how-to-set-up-training-targets.md).

+ **Med SDK**

  När du använder SDK för att träna en modell kan du ta emot antingen ett [körnings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objekt eller ett [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) -objekt, beroende på hur du tränade modellen. Varje-objekt kan användas för att registrera en modell som skapats av en experiment körning.

  + Registrera en modell från ett `azureml.core.Run` objekt:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `model_path` du sökvägen till en mapp som innehåller filerna. Mer information finns i dokumentationen för [Kör. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrera en modell från ett `azureml.train.automl.run.AutoMLRun` objekt:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    I det här exemplet har `metric` `iteration` parametrarna och inte angetts, så iterationen med bästa primära mått kommer att registreras. `model_id`Värdet som returneras från körningen används i stället för ett modell namn.

    Mer information finns i dokumentationen för [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  `--asset-path`Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du sökvägen till en mapp som innehåller filerna.

+ **Använda Visual Studio Code**

  Registrera modeller med hjälp av modell filer eller mappar med [Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) -tillägget.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

Du kan registrera en modell genom att ange den lokala sökvägen till modellen. Du kan ange sökvägen till antingen en mapp eller en enskild fil. Du kan använda den här metoden för att registrera modeller som har tränats med Azure Machine Learning och sedan laddats ned. Du kan också använda den här metoden för att registrera modeller som har tränats utanför Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Använda SDK och ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
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

**Tids uppskattning**: cirka 10 sekunder.

Mer information finns i dokumentationen för [modell klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller som har tränats utanför Azure Machine Learning finns i [distribuera en befintlig modell](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>En eller flera slut punkter i flera modeller
Azure ML stöder distribution av en eller flera modeller bakom en enda slut punkt.

Slut punkter för flera modeller använder en delad behållare som värd för flera modeller. Detta bidrar till att minska kostnaderna, förbättra användningen och gör att du kan koppla ihop moduler till ensembler. Modeller som du anger i distributions skriptet monteras och görs tillgängliga på disken för behållar behållaren – du kan läsa in dem i minnet på begäran och Poäng utifrån den aktuella modell som begärs vid tidpunkten för åtgärden.

I [det här exemplet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model) visas ett E2E-exempel som visar hur du använder flera modeller bakom en enda behållares slut punkt.

## <a name="prepare-to-deploy"></a>Förbered distributionen

Om du vill distribuera modellen som en tjänst behöver du följande komponenter:

* **Definiera en miljö för härledning**. Den här miljön kapslar in de beroenden som krävs för att köra din modell för härledning.
* **Definiera bedömnings kod**. Det här skriptet accepterar begär Anden, visar förfrågningarna med hjälp av modellen och returnerar resultatet.
* **Definiera konfiguration av härledning**. Konfigurationen av konfigurationen anger miljö konfigurationen, start skriptet och andra komponenter som behövs för att köra modellen som en tjänst.

När du har de nödvändiga komponenterna kan du profilera tjänsten som skapas på grund av distributionen av din modell för att förstå processor-och minnes kraven.

### <a name="1-define-inference-environment"></a>1. definiera en miljö för härledning

En konfiguration för en härledning beskriver hur du konfigurerar webb tjänsten som innehåller din modell. Den används senare när du distribuerar modellen.

Konfiguration av konfigurations miljön använder Azure Machine Learning miljöer för att definiera de program beroenden som behövs för distributionen. Med miljöer kan du skapa, hantera och återanvända program beroenden som krävs för utbildning och distribution. Du kan skapa en miljö från anpassade beroende filer eller använda någon av de granskade Azure Machine Learning miljöerna. Följande YAML är ett exempel på en fil för Conda-beroenden. Observera att du måste ange azureml-defaults med version >= 1.0.45 som ett pip-beroende, eftersom det innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst. Om du vill använda automatisk schema generering måste ditt Entry-skript också importera `inference-schema` paketen.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Om beroendet är tillgängligt via både Conda och pip (från PyPi) rekommenderar Microsoft att du använder Conda-versionen, eftersom Conda-paket vanligt vis levereras med färdiga binärfiler som gör installationen mer tillförlitlig.
>
> Mer information finns i [förstå Conda och pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Om du vill kontrol lera om beroendet är tillgängligt via Conda använder du `conda search <package-name>` kommandot eller så använder du paket indexen på [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) och [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Du kan använda beroende filen för att skapa ett miljö objekt och spara det på din arbets yta för framtida bruk:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. definiera bedömnings koden

Startskriptet tar emot data som skickas till en distribuerad webbtjänst och skickar dem vidare till modellen. Skriptet tar sedan emot svaret som returneras av modellen och returnerar det till klienten. *Skriptet är bara för din modell*. Den måste förstå de data som modellen förväntar sig och returnerar.

Skriptet innehåller två funktioner som läser in och kör modellen:

* `init()`: Den här funktionen laddar normalt modellen till ett globalt objekt. Den här funktionen körs bara en gång, när Docker-behållaren för webb tjänsten startas.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Körningens in- och utdata använder vanligtvis JSON för serialisering och deserialisering. Du kan också arbeta med binärdata i råformat. Du kan transformera data innan du skickar dem till modellen eller innan du returnerar dem till klienten.

#### <a name="load-model-files-in-your-entry-script"></a>Läsa in modell filer i ditt post skript

Det finns två sätt att hitta modeller i ditt Entry-skript:
* `AZUREML_MODEL_DIR`: En miljö variabel som innehåller sökvägen till modell platsen.
* `Model.get_model_path`: Ett API som returnerar sökvägen till modell filen med namnet på den registrerade modellen.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR är en miljö variabel som skapas under tjänst distributionen. Du kan använda den här miljövariabeln för att hitta platsen för de distribuerade modellerna.

I följande tabell beskrivs värdet för AZUREML_MODEL_DIR beroende på hur många modeller som distribueras:

| Distribution | Miljö variabel värde |
| ----- | ----- |
| Enskild modell | Sökvägen till mappen som innehåller modellen. |
| Flera modeller | Sökvägen till mappen som innehåller alla modeller. Modeller finns efter namn och version i den här mappen ( `$MODEL_NAME/$VERSION` ) |

Under modell registrering och distribution placeras modeller i AZUREML_MODEL_DIR Sök väg och deras ursprungliga fil namn bevaras.

Om du vill hämta sökvägen till en modell fil i ditt Entry-skript kombinerar du miljövariabeln med den fil Sök väg som du letar efter.

**Exempel på en modell**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exempel på flera modeller**

I det här scenariot registreras två modeller med arbets ytan:

* `my_first_model`: Innehåller en fil ( `my_first_model.pkl` ) och det finns bara en version ( `1` ).
* `my_second_model`: Innehåller en fil ( `my_second_model.pkl` ) och det finns två versioner, `1` och `2` .

När tjänsten distribuerades anges båda modellerna i distributions åtgärden:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

I Docker-avbildningen som är värd för tjänsten `AZUREML_MODEL_DIR` innehåller miljövariabeln den katalog där modellerna finns.
I den här katalogen finns varje modell i en katalog Sök väg för `MODEL_NAME/VERSION` . Där `MODEL_NAME` är namnet på den registrerade modellen och `VERSION` är versionen av modellen. De filer som utgör den registrerade modellen lagras i dessa kataloger.

I det här exemplet är Sök vägarna `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` och `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

När du registrerar en modell anger du ett modell namn som används för att hantera modellen i registret. Du använder det här namnet med metoden [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hämta sökvägen till modell filen eller filerna i det lokala fil systemet. Om du registrerar en mapp eller en samling filer, returnerar detta API sökvägen till katalogen som innehåller filerna.

När du registrerar en modell ger du den ett namn. Namnet motsvarar var modellen placeras, antingen lokalt eller under tjänst distributionen.

#### <a name="optional-define-model-web-service-schema"></a>Valfritt Definiera ett modell webb tjänst schema

För att automatiskt generera ett schema för webb tjänsten, ange ett exempel på indata och/eller utdata i konstruktorn för ett av de definierade typ objekten. Typen och exemplet används för att automatiskt skapa schemat. Azure Machine Learning skapar sedan en [openapi](https://swagger.io/docs/specification/about/) -specifikation (Swagger) för webb tjänsten under distributionen.

Dessa typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* Standard python-objekt

Om du vill använda schema generering inkluderar du paketet med öppen källkod `inference-schema` i beroende filen. Mer information om det här paketet finns i [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Definiera exempel formaten indata och utdata i `input_sample` `output_sample` variablerna och som representerar formatet för begäran och svar för webb tjänsten. Använd de här exemplen i funktionen indata och utdata dekoratörer i `run()` funktionen. Följande scikit – lära använder schema generering.

##### <a name="example-entry-script"></a>Exempel på post skript

Följande exempel visar hur du accepterar och returnerar JSON-data:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
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

##### <a name="power-bi-compatible-endpoint"></a>Power BI kompatibel slut punkt 

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
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

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
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

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binära data](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. definiera en konfiguration för härledning
    
I följande exempel visas hur du läser in en miljö från arbets ytan och sedan använder den med konfigurations konfigurationen:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om konfiguration av konfiguration finns i [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -klassens dokumentation.

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>CLI-exempel på InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet anger konfigurationen följande inställningar:

* Som modellen kräver python.
* [Entry-skriptet](#script), som används för att hantera webb förfrågningar som skickas till den distribuerade tjänsten.
* Den Conda-fil som beskriver de python-paket som behövs för att kunna användas.

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (valfritt) profilera din modell för att fastställa resursutnyttjande

När du har registrerat din modell och för berett de andra komponenterna som krävs för distributionen kan du fastställa den processor och det minne som den distribuerade tjänsten kommer att behöva. Profilering testar tjänsten som kör din modell och returnerar information som processor användning, minnes användning och svars fördröjning. Den innehåller också en rekommendation för CPU och minne baserat på resursanvändningen.

För att du ska kunna profilera din modell behöver du:
* En registrerad modell.
* En konfigurations konfiguration som baseras på definitionen av ditt Start skript och en härlednings miljö.
* En tabell data uppsättning med en kolumn, där varje rad innehåller en sträng som representerar exempel begär ande data.

> [!IMPORTANT]
> Nu stöder vi bara profilering av tjänster som förväntar sig att deras begär ande data ska vara en sträng, till exempel: sträng serialiserad JSON, text, sträng serialiserad bild osv. Innehållet för varje rad i data uppsättningen (sträng) placeras i bröd texten i HTTP-begäran och skickas till tjänsten som kapslar in modellen för poängsättning.

Nedan visas ett exempel på hur du kan skapa en data uppsättning för indata för att profilera en tjänst som förväntar sig att inkommande begär ande data ska innehålla serialiserad JSON. I det här fallet skapade vi en data uppsättning baserade 100 instanser av samma data innehåll för begäran. I verkliga scenarier rekommenderar vi att du använder större data uppsättningar som innehåller olika indata, särskilt om din resursanvändning/beteende för modellen är indata beroende.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

När du har data uppsättningen som innehåller exempel på begär ande data, skapar du en konfigurations konfiguration. Konfigurationen av konfigurationen baseras på score.py och miljö definitionen. Följande exempel visar hur du skapar en konfiguration för konfiguration och körning av en konfigurations profil:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Följande kommando visar hur du profilerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Om du vill spara informationen som returneras av profilering använder du taggar eller egenskaper för modellen. Genom att använda taggar eller egenskaper lagras data med modellen i modell registret. Följande exempel visar hur du lägger till en ny tagg som innehåller `requestedCpu` `requestedMemoryInGb` informationen och:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Distribuera till mål

Distributionen använder distributions konfigurationen för konfigurations konfiguration för att distribuera modellerna. Distributions processen är liknande oavsett beräknings målet. Distribution till AKS är något annorlunda eftersom du måste ange en referens till AKS-klustret.

### <a name="choose-a-compute-target"></a>Välj ett beräknings mål

Du kan använda följande beräknings mål eller beräknings resurser för att vara värd för webb tjänst distributionen:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

> [!NOTE]
> * ACI passar bara för små modeller <1 GB i storlek. 
> * Vi rekommenderar att du använder en enda Node-AKS för utveckling och testning av större modeller.

### <a name="define-your-deployment-configuration"></a>Definiera distributions konfigurationen

Innan du distribuerar din modell måste du definiera distributions konfigurationen. *Distributions konfigurationen är specifika för det beräknings mål som ska vara värd för webb tjänsten.* Om du till exempel distribuerar en modell lokalt måste du ange den port där tjänsten accepterar begär Anden. Distributions konfigurationen ingår inte i ditt Entry-skript. Den används för att definiera egenskaperna för det beräknings mål som ska vara värd för modell-och registrerings skriptet.

Du kan också behöva skapa beräknings resursen, om du till exempel inte redan har en AKS-instans (Azure Kubernetes service) kopplad till din arbets yta.

Följande tabell innehåller ett exempel på hur du skapar en distributions konfiguration för varje beräknings mål:

| Beräkningsmål | Exempel på distributions konfiguration |
| ----- | ----- |
| Lokal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasserna för lokala, Azure Container Instances-och AKS-webbtjänster kan importeras från `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Skydda distributioner med TLS

Mer information om hur du skyddar en webb tjänst distribution finns i [Aktivera TLS och distribuera](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Lokal distribution

Om du vill distribuera en modell lokalt måste du ha Docker installerat på den lokala datorn.

#### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information finns i dokumentationen för [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)och [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Använda CLI

Använd följande kommando om du vill distribuera en modell med hjälp av CLI. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i dokumentationen för [AZ ml-modellen Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="understanding-service-state"></a>Förstå tjänst tillstånd

Under modell distributionen kan tjänst tillstånds ändringen visas när den distribueras fullständigt.

I följande tabell beskrivs de olika tjänst tillstånden:

| Webservice-tillstånd | Beskrivning | Slutligt tillstånd?
| ----- | ----- | ----- |
| Övergår | Tjänsten håller på att distribueras. | Nej |
| Ohälsosamt | Tjänsten har distribuerats men är för närvarande inte tillgänglig.  | Nej |
| Unschedulable | Det går inte att distribuera tjänsten för tillfället på grund av bristande resurser. | Nej |
| Misslyckad | Det gick inte att distribuera tjänsten på grund av ett fel eller en krasch. | Ja |
| Felfri | Tjänsten är felfri och slut punkten är tillgänglig. | Ja |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Beräknings instans webb tjänst (dev/test)

Se [distribuera en modell för att Azure Machine Learning beräknings instans](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure Container Instances (dev/test)

Se [distribuera till Azure Container instances](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Azure Kubernetes-tjänsten (utveckling/testning och produktion)

Se [distribuera till Azure Kubernetes-tjänsten](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>A/B-test (kontrollerad distribution)
Mer information finns i [den kontrollerade distributionen av ml-modeller](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) .

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webb tjänst tillhandahåller en REST-slutpunkt, så att du kan skapa klient program i valfritt programmeringsspråk.
Om du har aktiverat nyckelbaserad autentisering för tjänsten måste du ange en tjänst nyckel som en token i ditt begär ande huvud.
Om du har aktiverat tokenbaserad autentisering för tjänsten måste du ange ett Azure Machine Learning JSON Web Token (JWT) som en Bearer-token i ditt begär ande huvud. 

Den främsta skillnaden är att **nycklarna är statiska och kan återskapas manuellt**, och **token måste uppdateras när de upphör att gälla**. Nyckelbaserad autentisering stöds för Azure Container instance och Azure Kubernetes-tjänsten distribuerade webb tjänster, och tokenbaserad autentisering är **bara** tillgängligt för Azure Kubernetes service-distributioner. Mer information och vissa kod exempel finns i avsnittet om [att](how-to-setup-authentication.md#web-service-authentication) autentisera.

> [!TIP]
> Du kan hämta schema-JSON-dokumentet när du har distribuerat tjänsten. Använd [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) från den distribuerade webb tjänsten (till exempel `service.swagger_uri` ) för att hämta URI: n till den lokala webb tjänstens Swagger-fil.

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

Om du har använt automatisk schema generering med din distribution kan du hämta adressen till OpenAPI-specifikationen för tjänsten med hjälp av [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Till exempel `print(service.swagger_uri)` .) Använd en GET-begäran eller öppna URI: n i en webbläsare för att hämta specifikationen.

Följande JSON-dokument är ett exempel på ett schema (OpenAPI-specifikation) som genereras för en distribution:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
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

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Batch-härledning
Azure Machine Learning beräknings mål skapas och hanteras av Azure Machine Learning. De kan användas för batch förutsägelse från Azure Machine Learning pipeliner.

En genom gång av batch-härledning med Azure Machine Learning Compute finns i [så här kör du batch-förutsägelser](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge-härledning
Stöd för att distribuera till Edge är i för hands version. Mer information finns i [distribuera Azure Machine Learning som en IoT Edge modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Uppdatera webb tjänster

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Distribuera modeller kontinuerligt

Du kan distribuera modeller kontinuerligt med Machine Learning-tillägget för [Azure-DevOps](https://azure.microsoft.com/services/devops/). Du kan använda Machine Learning-tillägget för Azure-DevOps för att utlösa en distributions pipeline när en ny maskin inlärnings modell registreras i en Azure Machine Learning arbets yta.

1. Registrera dig för [Azure-pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), som möjliggör kontinuerlig integrering och leverans av ditt program till vilken plattform eller moln som möjligt. (Observera att Azure-pipeliner inte är samma som [Machine Learning pipelines](concept-ml-pipelines.md#compare).)

1. [Skapa ett Azure DevOps-projekt.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installera [Machine Learning-tillägget för Azure-pipeliner](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Använd tjänst anslutningar för att konfigurera en tjänstens huvud namns anslutning till din Azure Machine Learning-arbetsyta så att du kan komma åt dina artefakter. Gå till projekt inställningar, Välj **tjänst anslutningar**och välj sedan **Azure Resource Manager**:

    [![Välj Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. I listan **omfattnings nivå** väljer du **AzureMLWorkspace**och anger sedan resten av värdena:

    ![Välj AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

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

## <a name="preview-no-code-model-deployment"></a>Förhandsgranskningsvyn Distribution utan kod modell

Ingen kod modell distribution är för närvarande en för hands version och stöder följande ramverk för Machine Learning:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel-format
Tensorflow-modeller måste registreras i **SavedModel-format** för att fungera med modell distribution utan kod.

Information om hur du skapar en SavedModel finns i [den här länken](https://www.tensorflow.org/guide/saved_model) .

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX-modeller

ONNX-modell registrering och-distribution stöds för ett ONNX-diagram för härledning. Det finns för närvarande inte stöd för preprocess-och postprocess-stegen.

Här är ett exempel på hur du registrerar och distribuerar en MNIST ONNX-modell:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Om du använder Pytorch, [exporterar modeller från Pytorch till ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) information om konvertering och begränsningar. 

### <a name="scikit-learn-models"></a>Scikit – lär dig modeller

Ingen distribution av kod modeller stöds för alla inbyggda scikit – lär dig modell typer.

Här är ett exempel på hur du registrerar och distribuerar en sklearn-modell utan extra kod:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Obs: modeller som stöder predict_proba använder den metoden som standard. Om du vill åsidosätta detta om du vill använda predict kan du ändra INLÄGGs texten enligt nedan:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Obs! de här beroendena ingår i den förbyggda scikit-lärens härlednings behållare:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
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

När du har laddat ned modellen använder du `docker images` kommandot för att lista de lokala avbildningarna:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Starta en lokal behållare baserat på den här avbildningen genom att använda följande kommando för att starta en namngiven behållare från gränssnittet eller kommando raden. Ersätt `<imageid>` värdet med det bild-ID som kommandot returnerade `docker images` .

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Det här kommandot startar den senaste versionen av avbildningen med namnet `myimage` . Den mappar den lokala port 6789 till porten i behållaren där webb tjänsten lyssnar (5001). Det tilldelar också namnet `mycontainer` till behållaren, vilket gör det enklare att stoppa behållaren. När behållaren har startats kan du skicka begär anden till `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generera en Dockerfile och beroenden

I följande exempel visas hur du hämtar Dockerfile, modellen och andra till gångar som behövs för att bygga en avbildning lokalt. `generate_dockerfile=True`Parametern anger att du vill att filerna, inte en helt skapad avbildning, ska visas.

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

Den här koden laddar ned de filer som krävs för att bygga avbildningen till `imagefiles` katalogen. Dockerfile som ingår i de sparade filerna hänvisar till en bas avbildning som lagras i ett Azure Container Registry. När du skapar avbildningen på den lokala Docker-installationen måste du använda adressen, användar namnet och lösen ordet för att autentisera till registret. Använd följande steg för att bygga avbildningen med hjälp av en lokal Docker-installation:

1. Använd följande kommando för att autentisera Docker med Azure Container Registry från en Shell-eller kommando rads-session. Ersätt `<address>` , `<username>` och `<password>` med värdena som hämtades av `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Använd följande kommando för att bygga avbildningen. Ersätt `<imagefiles>` med sökvägen till katalogen där `package.save()` filerna sparades.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Det här kommandot anger avbildningens namn till `myimage` .

Om du vill kontrol lera att avbildningen har skapats använder du `docker images` kommandot. Du bör se `myimage` avbildningen i listan:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Om du vill starta en ny behållare baserat på den här avbildningen använder du följande kommando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Det här kommandot startar den senaste versionen av avbildningen med namnet `myimage` . Den mappar den lokala port 6789 till porten i behållaren där webb tjänsten lyssnar (5001). Det tilldelar också namnet `mycontainer` till behållaren, vilket gör det enklare att stoppa behållaren. När behållaren har startats kan du skicka begär anden till `http://localhost:6789/score` .

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

Använd om du vill ta bort en distribuerad webb tjänst `service.delete()` .
Använd om du vill ta bort en registrerad modell `model.delete()` .

Mer information finns i dokumentationen för [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) och [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Avancerad post skript redigering

<a id="binary"></a>

### <a name="binary-data"></a>Binära data

Om din modell accepterar binära data, till exempel en bild, måste du ändra den `score.py` fil som används för distributionen för att acceptera obehandlade HTTP-begäranden. Om du vill acceptera rå data använder du `AMLRequest` klassen i ditt Entry-skript och lägger till `@rawhttp` decorator i `run()` funktionen.

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
> `AMLRequest`Klassen är i `azureml.contrib` namn området. Entiteter i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namn området bör betraktas som en för hands version som inte fullt ut stöds av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

Med `AMLRequest` klassen kan du bara komma åt rå data som har anslagits i score.py, men det finns ingen komponent på klient sidan. Från en klient skickar du data som vanligt. Följande python-kod läser till exempel en avbildnings fil och publicerar data:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Resurs delning mellan ursprung (CORS)

Resurs delning mellan ursprung är ett sätt att tillåta att resurser på en webb sida begärs från en annan domän. CORS fungerar via HTTP-huvuden som skickas med klientbegäran och som returneras med tjänstens svar. Mer information om CORS och giltiga huvuden finns i [resurs delning mellan ursprung](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) i wikipedia.

Om du vill konfigurera modell distributionen så att den stöder CORS använder du `AMLResponse` klassen i ditt Entry-skript. Med den här klassen kan du ange huvuden för objektet Response.

I följande exempel anges `Access-Control-Allow-Origin` rubriken för svaret från Entry-skriptet:

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse`Klassen är i `azureml.contrib` namn området. Entiteter i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namn området bör betraktas som en för hands version som inte fullt ut stöds av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning dirigerar bara POST-och GET-begäranden till behållarna som kör bedömnings tjänsten. Detta kan orsaka fel på grund av att webbläsare använder OPTIONs-förfrågningar till före flygning-CORS-begäranden.
> 

## <a name="next-steps"></a>Nästa steg

* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Använd TLS för att skydda en webb tjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Konsumera en Azure Machine Learning-modell som distribuerats som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)

