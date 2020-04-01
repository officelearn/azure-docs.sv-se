---
title: Hur och var modeller ska distribueras
titleSuffix: Azure Machine Learning
description: Lär dig hur och var du kan distribuera dina Azure Machine Learning-modeller, inklusive Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge och fältprogrammerbara gate-matriser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 0deace98c5be0b2ce2f29abce4c8a804145afdb1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475618"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuera modeller med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar din maskininlärningsmodell som en webbtjänst i Azure-molnet eller till Azure IoT Edge-enheter.

Arbetsflödet är liknande oavsett [var du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbered distributionen. (Ange tillgångar, användning och beräkningsmål.)
1. Distribuera modellen till beräkningsmålet.
1. Testa den distribuerade modellen, även kallad webbtjänst.

Mer information om de begrepp som ingår i distributionsarbetsflödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Krav

- En Azure Machine Learning-arbetsyta. Mer information finns i [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En modell. Om du inte har en tränad modell kan du använda modell- och beroendefilerna i den [här självstudien](https://aka.ms/azml-deploy-cloud).

- [Azure CLI-tillägget för machine learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller Azure Machine Learning Visual Studio [Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

Följande kod visar hur du ansluter till en Azure Machine Learning-arbetsyta med hjälp av information som cachelagrats till den lokala utvecklingsmiljön:

+ **Med SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Mer information om hur du använder SDK för att ansluta till en arbetsyta finns i [Azure Machine Learning SDK for](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) Python-dokumentationen.

+ **Använda CLI**

   När du använder CLI `-w` `--workspace-name` använder du parametern eller för att ange arbetsytan för kommandot.

+ **Använda Visual Studio Code**

   När du använder Visual Studio-kod väljer du arbetsytan med hjälp av ett grafiskt gränssnitt. Mer information finns i [Distribuera och hantera modeller](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) i dokumentationen till Tillägget Visual Studio-kod.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrera din modell

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enda modell på arbetsytan. När du har registrerat filerna kan du sedan hämta eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en molnplats (från en utbildningskörning) eller en lokal katalog. Den här sökvägen är bara att hitta filerna för uppladdning som en del av registreringsprocessen. Den behöver inte matcha sökvägen som används i inmatningsskriptet. Mer information finns [i Hitta modellfiler i ditt postskript](#load-model-files-in-your-entry-script).

Machine learning-modeller registreras på din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller från någon annanstans. När du registrerar en modell kan du också ange metadata om modellen. De `tags` `properties` ordlistor och ordlistor som du använder för en modellregistrering kan sedan användas för att filtrera modeller.

Följande exempel visar hur du registrerar en modell.

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från en experimentkörning

Kodavsnitten i det här avsnittet visar hur du registrerar en modell från en utbildningskörning:

> [!IMPORTANT]
> Om du vill använda dessa kodavsnitt måste du tidigare ha kört en träningskörning och du måste ha åtkomst till `Run` objektet (SDK-exempel) eller körnings-ID-värdet (CLI-exempel). Mer information om träningsmodeller finns i [Konfigurera beräkningsmål för modellutbildning](how-to-set-up-training-targets.md).

+ **Med SDK**

  När du använder SDK för att träna en modell kan du få antingen ett [Run-objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) eller ett [AutoMLRun-objekt,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) beroende på hur du tränade modellen. Varje objekt kan användas för att registrera en modell som skapats av en experimentkörning.

  + Registrera en modell `azureml.core.Run` från ett objekt:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Parametern `model_path` refererar till modellens molnplats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera `model_path` filer i modellregistreringen anger du sökvägen till en mapp som innehåller filerna. Mer information finns i dokumentationen [för Run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registrera en modell `azureml.train.automl.run.AutoMLRun` från ett objekt:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    I det här `metric` `iteration` exemplet anges inte parametrarna och, så iterationen med det bästa primära måttet registreras. Värdet `model_id` som returneras från körningen används i stället för ett modellnamn.

    Mer information finns i dokumentationen [för AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  Parametern `--asset-path` refererar till modellens molnplats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera `--asset-path` filer i modellregistreringen anger du sökvägen till en mapp som innehåller filerna.

+ **Använda Visual Studio Code**

  Registrera modeller med modellfiler eller mappar med hjälp av [tillägget Visual Studio-kod.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

Du kan registrera en modell genom att ange modellens lokala sökväg. Du kan ange sökvägen till antingen en mapp eller en enskild fil. Du kan använda den här metoden för att registrera modeller som tränas med Azure Machine Learning och sedan hämtas. Du kan också använda den här metoden för att registrera modeller som tränas utanför Azure Machine Learning.

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

  Om du vill inkludera flera `model_path` filer i modellregistreringen anger du sökvägen till en mapp som innehåller filerna.

+ **Använda CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Om du vill inkludera flera `-p` filer i modellregistreringen anger du sökvägen till en mapp som innehåller filerna.

**Tidsuppskattning**: Cirka 10 sekunder.

Mer information finns i dokumentationen för [klassen Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller som utbildats utanför Azure Machine Learning finns i [Så här distribuerar](how-to-deploy-existing-model.md)du en befintlig modell .

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Slutpunkter med en modell jämfört med flera modeller
Azure ML stöder distribution av en eller flera modeller bakom en enda slutpunkt.

Slutpunkter med flera modeller använder en delad behållare som värd för flera modeller. Detta bidrar till att minska omkostnaderna, förbättrar utnyttjandet och gör att du kan kedja moduler tillsammans i ensembler. Modeller som du anger i distributionsskriptet monteras och görs tillgängliga på disken i serveringsbehållaren – du kan läsa in dem i minnet på begäran och poäng baserat på den specifika modell som begärs vid bedömningstillfället.

Ett E2E-exempel, som visar hur du använder flera modeller bakom en enda behållarslutpunkt, finns i [det här exemplet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Förbered distributionen

Om du vill distribuera modellen som en tjänst behöver du följande komponenter:

* **Definiera inferensmiljö**. Den här miljön kapslar in de beroenden som krävs för att köra din modell för slutledning.
* **Definiera poängkod**. Det här skriptet accepterar begäranden, poängför begäranden med hjälp av modellen och returnerar resultaten.
* **Definiera inferenskonfiguration**. Inferenskonfigurationen anger miljökonfiguration, inmatningsskript och andra komponenter som behövs för att köra modellen som en tjänst.

När du har nödvändiga komponenter kan du profilera tjänsten som skapas som ett resultat av att distribuera din modell för att förstå dess CPU- och minneskrav.

### <a name="1-define-inference-environment"></a>1. Definiera inferensmiljö

En slutledningskonfiguration beskriver hur du ställer in webbtjänsten som innehåller din modell. Den används senare när du distribuerar modellen.

Inferenskonfiguration använder Azure Machine Learning-miljöer för att definiera de programvaruberoenden som behövs för distributionen. Med miljöer kan du skapa, hantera och återanvända de programvaruberoenden som krävs för utbildning och distribution. Du kan skapa en miljö från anpassade beroendefiler eller använda någon av de kurerade Azure Machine Learning-miljöerna. Följande YAML är ett exempel på en Conda beroendefil för slutledning. Observera att du måste ange azureml-defaults med verion >= 1.0.45 som ett pipberoende, eftersom det innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst. Om du vill använda automatisk schemagenerering måste ditt `inference-schema` postskript också importera paketen.

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
> Om ditt beroende är tillgängligt via både Conda och pip (från PyPi) rekommenderar Microsoft att du använder Conda-versionen, eftersom Conda-paket vanligtvis levereras med förbyggda binärfiler som gör installationen mer tillförlitlig.
>
> Mer information finns i [Förstå Conda och Pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Om du vill kontrollera om ditt beroende `conda search <package-name>` är tillgängligt via Conda [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) använder [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)du kommandot eller använder paketindexen på och .

Du kan använda beroendefilen för att skapa ett miljöobjekt och spara det på arbetsytan för framtida bruk:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Definiera poängkod

Startskriptet tar emot data som skickas till en distribuerad webbtjänst och skickar dem vidare till modellen. Skriptet tar sedan emot svaret som returneras av modellen och returnerar det till klienten. *Skriptet är specifikt för din modell*. Den måste förstå de data som modellen förväntar sig och returnerar.

Skriptet innehåller två funktioner som läser in och kör modellen:

* `init()`: Vanligtvis läser den här funktionen in modellen i ett globalt objekt. Den här funktionen körs bara en gång när Docker-behållaren för webbtjänsten startas.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Körningens in- och utdata använder vanligtvis JSON för serialisering och deserialisering. Du kan också arbeta med binärdata i råformat. Du kan transformera data innan du skickar dem till modellen eller innan du returnerar dem till klienten.

#### <a name="load-model-files-in-your-entry-script"></a>Läsa in modellfiler i ditt inmatningsskript

Det finns två sätt att hitta modeller i ditt postskript:
* `AZUREML_MODEL_DIR`: En miljövariabel som innehåller sökvägen till modellplatsen.
* `Model.get_model_path`: Ett API som returnerar sökvägen till modellfilen med det registrerade modellnamnet.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR är en miljövariabel som skapas under tjänstdistributionen. Du kan använda den här miljövariabeln för att hitta platsen för de distribuerade erna.

I följande tabell beskrivs värdet på AZUREML_MODEL_DIR beroende på antalet modeller som distribueras:

| Distribution | Miljövariabelvärde |
| ----- | ----- |
| Enda modell | Sökvägen till mappen som innehåller modellen. |
| Flera modeller | Sökvägen till mappen som innehåller alla modeller. Modellerna finns efter namn och version`$MODEL_NAME/$VERSION`i den här mappen ( ) |

Under modellregistrering och distribution placeras modeller i AZUREML_MODEL_DIR sökvägen och deras ursprungliga filnamn bevaras.

Om du vill hämta sökvägen till en modellfil i ditt postskript kombinerar du miljövariabeln med den filsökväg du letar efter.

**Exempel på en modell**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Flera modellexempel**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

När du registrerar en modell anger du ett modellnamn som används för att hantera modellen i registret. Du använder det här namnet med metoden [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hämta sökvägen till modellfilen eller filerna i det lokala filsystemet. Om du registrerar en mapp eller en samling filer returnerar det här API:et sökvägen till den katalog som innehåller filerna.

När du registrerar en modell ger du den ett namn. Namnet motsvarar var modellen placeras, antingen lokalt eller under tjänstdistributionen.

#### <a name="optional-define-model-web-service-schema"></a>(Valfritt) Definiera modellwebbtjänstschema

Om du automatiskt vill generera ett schema för webbtjänsten anger du ett exempel på indata och/eller utdata i konstruktorn för ett av de definierade typobjekten. Typen och exemplet används för att automatiskt skapa schemat. Azure Machine Learning skapar sedan en [OpenAPI(Swagger)](https://swagger.io/docs/specification/about/) specifikation för webbtjänsten under distributionen.

Dessa typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objekt

Om du vill använda schemagenerering `inference-schema` inkluderar du paketet med öppen källkod i beroendefilen. Mer information om det [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)här paketet finns i . Definiera exempelformaten indata och `input_sample` `output_sample` utdata i variablerna och som representerar webbtjänstens format för begäran och svar. Använd dessa prover i in- och utdatafunktionsdekoratörerna på `run()` funktionen. I följande scikit-learn-exempel används schemagenerering.

##### <a name="example-entry-script"></a>Exempel på inmatningsskript

I följande exempel visas hur du accepterar och returnerar JSON-data:

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

I följande exempel visas hur du definierar indata som en `<key: value>` ordlista med hjälp av en DataFrame. Den här metoden stöds för att använda den distribuerade webbtjänsten från Power BI. ([Läs mer om hur du använder webbtjänsten från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binära data](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Definiera inferenskonfiguration
    
I följande exempel visas att en miljö läses in från arbetsytan och sedan använda den med inferenskonfigurationen:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Mer information om miljöer finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om inferenskonfiguration finns i dokumentationen [till inferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

Information om hur du använder en anpassad Docker-avbildning med en slutledningskonfiguration finns i [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>CLI exempel på InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet anger konfigurationen följande inställningar:

* Att modellen kräver Python.
* [Postskriptet](#script), som används för att hantera webbbegäranden som skickas till den distribuerade tjänsten.
* Conda-filen som beskriver Python-paket som behövs för slutledning.

Information om hur du använder en anpassad Docker-avbildning med en slutledningskonfiguration finns i [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Valfritt) Profilera din modell för att fastställa resursutnyttjande

När du har registrerat din modell och förberett de andra komponenter som krävs för distributionen kan du bestämma vilken CPU och minne som den distribuerade tjänsten behöver. Profilering testar tjänsten som kör din modell och returnerar information som CPU-användning, minnesanvändning och svarssvarstid. Det ger också en rekommendation för CPU och minne baserat på resursanvändning.

För att profilera din modell behöver du:
* En registrerad modell.
* En inferenskonfiguration baserad på ditt inmatningsskript och inferensmiljödefinition.
* En tabelldatauppsättning med en kolumn, där varje rad innehåller en sträng som representerar exempelbegärandedata.

> [!IMPORTANT]
> Vid denna punkt stöder vi bara profilering av tjänster som förväntar sig att deras begäran data vara en sträng, till exempel: sträng serialiserad json, text, sträng serialiserad bild, etc. Innehållet i varje rad i datauppsättningen (strängen) placeras i brödtexten för HTTP-begäran och skickas till tjänsten som kapslar in modellen för bedömning.

Nedan följer ett exempel på hur du kan konstruera en indatauppsättning för att profilera en tjänst som förväntar sig att dess inkommande begäran data ska innehålla serialiserad json. I det här fallet skapade vi en datauppsättning som baseras hundra instanser av samma begäran datainnehåll. I verkliga scenarier föreslår vi att du använder större datauppsättningar som innehåller olika indata, särskilt om din modell resursanvändning /beteende är indataberoende.

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

När du har datauppsättningen som innehåller exempelbegärandedata klar skapar du en slutledningskonfiguration. Inferenskonfigurationen baseras på score.py och miljödefinitionen. I följande exempel visas hur du skapar inferenskonfigurationen och kör profilering:

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
> Om du vill spara informationen som returneras genom profilering använder du taggar eller egenskaper för modellen. Om du använder taggar eller egenskaper lagras data med modellen i modellregistret. Följande exempel visar att du `requestedCpu` lägger till en ny tagg som innehåller information och `requestedMemoryInGb` information:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Distribuera till målet

Distributionen använder konfigurationskonfigurationskonfigurationskonfigurationskonfigurationen för att distribuera modellerna. Distributionsprocessen är liknande oavsett beräkningsmålet. Distribution till AKS är något annorlunda eftersom du måste ange en referens till AKS-klustret.

### <a name="choose-a-compute-target"></a>Välj ett beräkningsmål

Du kan använda följande beräkningsmål, eller beräkningsresurser, för att vara värd för distributionen av webbtjänsten:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Definiera distributionskonfigurationen

Innan du distribuerar din modell måste du definiera distributionskonfigurationen. *Distributionskonfigurationen är specifik för beräkningsmålet som ska vara värd för webbtjänsten.* När du till exempel distribuerar en modell lokalt måste du ange den port där tjänsten accepterar begäranden. Distributionskonfigurationen är inte en del av ditt postskript. Den används för att definiera egenskaperna för beräkningsmålet som ska vara värd för modellen och inmatningsskriptet.

Du kan också behöva skapa beräkningsresursen om du till exempel inte redan har en AKS-instans (Azure Kubernetes Service) som är associerad med arbetsytan.

Följande tabell innehåller ett exempel på hur du skapar en distributionskonfiguration för varje beräkningsmål:

| Beräkningsmål | Exempel på distributionskonfiguration |
| ----- | ----- |
| Lokal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasserna för lokala Azure-behållarinstanser och AKS-webbtjänster kan importeras från: `azureml.core.webservice`

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Skydda distributioner med TLS

Mer information om hur du skyddar en webbtjänstdistribution finns i [Aktivera TLS och distribuera](how-to-secure-web-service.md#enable).

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

Mer information finns i dokumentationen för [LocalWebservice,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py) [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)och [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Använda CLI

Om du vill distribuera en modell med hjälp av CLI använder du följande kommando. Ersätt `mymodel:1` med namn och version av den registrerade modellen:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i dokumentationen för [az ml-modellen distribuera.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>Förstå tjänsttillstånd

Under modelldistributionen kan du se att tjänsttillståndet ändras när den distribueras helt.

I följande tabell beskrivs de olika tjänsttillstånden:

| Webbtjänsttillstånd | Beskrivning | Sista staten?
| ----- | ----- | ----- |
| Övergång | Tjänsten håller på att distribueras. | Inga |
| Inte felfri | Tjänsten har distribuerats men kan för närvarande inte nås.  | Inga |
| Oplanerbar | Tjänsten kan inte distribueras just nu på grund av brist på resurser. | Inga |
| Misslyckades | Tjänsten kunde inte distribueras på grund av ett fel eller krasch. | Ja |
| Felfri | Tjänsten är felfri och slutpunkten är tillgänglig. | Ja |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Webbtjänst för beräkningsinstans (utveckling/test)

Se [Distribuera en modell till Azure Machine Learning-beräkningsinstans](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure-behållarinstanser (utveckling/test)

Se [Distribuera till Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Azure Kubernetes Service (utveckling/test och produktion)

Se [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>A/B-testning (kontrollerad utrullning)
Mer information [finns i Kontrollerad utrullning av ML-modeller.](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webbtjänst tillhandahåller en REST-slutpunkt, så att du kan skapa klientprogram på alla programmeringsspråk.
Om du har aktiverat nyckelbaserad autentisering för din tjänst måste du ange en tjänstnyckel som en token i ditt begärandehuvud.
Om du har aktiverat tokenbaserad autentisering för din tjänst måste du ange en Azure Machine Learning JSON Web Token (JWT) som en bäraretoken i ditt begärandehuvud. 

Den primära skillnaden är att **nycklarna är statiska och kan återskapas manuellt**och token måste **uppdateras vid förfallodatum**. Nyckelbaserad autentisering stöds för Azure Container Instance och Azure Kubernetes Service distribuerade webbtjänster, och tokenbaserad auth är **endast** tillgänglig för Azure Kubernetes Service-distributioner. Mer information och specifika [kodexempel](how-to-setup-authentication.md#web-service-authentication) finns i hur du gör om autentisering.

> [!TIP]
> Du kan hämta schemat JSON-dokumentet när du har distribuerat tjänsten. Använd [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) från den distribuerade webbtjänsten `service.swagger_uri`(till exempel) för att hämta URI:n till den lokala webbtjänstens Swagger-fil.

### <a name="request-response-consumption"></a>Förbrukning av begäran-svar

Här är ett exempel på hur du anropar din tjänst i Python:
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

Mer information finns i [Skapa klientprogram för att använda webbtjänster](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Webbtjänstschema (OpenAPI-specifikation)

Om du använde automatisk schemagenerering med distributionen kan du hämta adressen till OpenAPI-specifikationen för tjänsten med hjälp av [egenskapen swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Till exempel `print(service.swagger_uri)`.) Använd en GET-begäran eller öppna URI i en webbläsare för att hämta specifikationen.

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

Mer information finns i [OpenAPI-specifikationen](https://swagger.io/specification/).

Ett verktyg som kan skapa klientbibliotek från specifikationen finns i [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Slutsatser om batch
Azure Machine Learning Compute-mål skapas och hanteras av Azure Machine Learning. De kan användas för batchförutsägels från Azure Machine Learning-pipelines.

En genomgång av batchinferens med Azure Machine Learning Compute finns i [Så här kör du batchprognoser](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge-slutledning
Stöd för distribution till kanten är i förhandsversion. Mer information finns i [Distribuera Azure Machine Learning som en IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Uppdatera webbtjänster

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Distribuera modeller kontinuerligt

Du kan kontinuerligt distribuera modeller med hjälp av machine learning-tillägget för [Azure DevOps](https://azure.microsoft.com/services/devops/). Du kan använda machine learning-tillägget för Azure DevOps för att utlösa en distributionspipeline när en ny maskininlärningsmodell har registrerats på en Azure Machine Learning-arbetsyta.

1. Registrera dig för [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), vilket gör kontinuerlig integrering och leverans av ditt program till alla plattformar eller moln möjliga. (Observera att Azure Pipelines inte är samma sak som [Machine Learning-pipelines](concept-ml-pipelines.md#compare).)

1. [Skapa ett Azure DevOps-projekt.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installera [maskininlärningstillägget för Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Använd tjänstanslutningar för att konfigurera en huvudanslutning till din Azure Machine Learning-arbetsyta så att du kan komma åt dina artefakter. Gå till projektinställningar, välj **Tjänstanslutningar**och välj sedan **Azure Resource Manager:**

    [![Välj Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Välj **AzureMLWorkspace**i listan **Scope-nivå** och ange sedan resten av värdena:

    ![Välj AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Om du vill distribuera din maskininlärningsmodell kontinuerligt med hjälp av Azure Pipelines väljer du **släpp**under pipelines . Lägg till en ny artefakt och välj sedan **AzureML-modellartefakten** och tjänstanslutningen som du skapade tidigare. Välj den modell och version som ska utlösa en distribution:

    [![Välj AzureML-modell](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Aktivera modellutlösaren på modellartefakten. När du aktiverar utlösaren utlöses en Azure DevOps-versionspipeline varje gång den angivna versionen (d.v.s. den senaste versionen) av den modellen registreras på arbetsytan.

    [![Aktivera modellutlösaren](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Mer exempel på exempel på exempel finns i dessa exempelrepor i GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Ladda ner en modell
Om du vill ladda ner din modell för att använda den i din egen exekveringsmiljö kan du göra det med följande SDK / CLI-kommandon:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Cli:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Förhandsgranska) Distribution av no-code-modell

Distribution av no-code-modell är för närvarande i förhandsversion och stöder följande ramverk för maskininlärning:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel-format
Tensorflow-modeller måste registreras i **SavedModel-format** för att fungera med distribution utan kodmodell.

Se [den här länken](https://www.tensorflow.org/guide/saved_model) för information om hur du skapar en SavedModel.

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

ONNX-modellregistrering och distribution stöds för alla ONNX-inferensdiagram. Förprocess- och efterprocesssteg stöds för närvarande inte.

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

Om du använder Pytorch har [exporterande modeller från PyTorch till ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) information om konvertering och begränsningar. 

### <a name="scikit-learn-models"></a>Scikit-lära modeller

Ingen kodmodelldistribution stöds för alla inbyggda scikit-learn-modelltyper.

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

Modeller som stöder predict_proba använder den metoden som standard. För att åsidosätta detta för att använda förutsäga kan du ändra POST kroppen enligt nedan:
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

Obs: Dessa beroenden ingår i den fördefinierade sklearn inferensbehållaren:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Paketmodeller

I vissa fall kanske du vill skapa en Docker-avbildning utan att distribuera modellen (om du till exempel planerar att [distribuera till Azure App Service](how-to-deploy-app-service.md)). Eller så kanske du vill hämta avbildningen och köra den på en lokal Docker-installation. Du kanske till och med vill hämta de filer som används för att skapa avbildningen, inspektera dem, ändra dem och skapa avbildningen manuellt.

Modellförpackning gör att du kan göra dessa saker. Det paketerar alla tillgångar som behövs för att vara värd för en modell som en webbtjänst och låter dig ladda ner antingen en fullt byggd Docker-avbildning eller de filer som behövs för att bygga en. Det finns två sätt att använda modellförpackningar:

**Ladda ner en paketerad modell:** Hämta en Docker-avbildning som innehåller modellen och andra filer som behövs för att vara värd för den som en webbtjänst.

**Generera en Dockerfile:** Hämta Dockerfile, modell, postskript och andra resurser som behövs för att skapa en Docker-avbildning. Du kan sedan granska filerna eller göra ändringar innan du skapar avbildningen lokalt.

Båda paketen kan användas för att hämta en lokal Docker-avbildning.

> [!TIP]
> Att skapa ett paket liknar distribution av en modell. Du använder en registrerad modell och en slutledningskonfiguration.

> [!IMPORTANT]
> Om du vill hämta en fullt utvecklad avbildning eller skapa en avbildning lokalt måste du ha [Docker](https://www.docker.com) installerat i utvecklingsmiljön.

### <a name="download-a-packaged-model"></a>Ladda ner en paketerad modell

I följande exempel skapas en avbildning som är registrerad i Azure-behållarregistret för din arbetsyta:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

När du har skapat ett `package.pull()` paket kan du använda för att hämta avbildningen till din lokala Docker-miljö. Utdata från det här kommandot visar namnet på bilden. Ett exempel: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

När du har hämtat `docker images` modellen använder du kommandot för att lista de lokala bilderna:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Om du vill starta en lokal behållare baserat på den här avbildningen använder du följande kommando för att starta en namngiven behållare från skalet eller kommandoraden. Ersätt `<imageid>` värdet med det bild-ID `docker images` som returneras av kommandot.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Det här kommandot startar den `myimage`senaste versionen av bilden med namnet . Den mappar lokal port 6789 till porten i behållaren där webbtjänsten lyssnar (5001). Det tilldelar också `mycontainer` namnet till behållaren, vilket gör behållaren lättare att stoppa. När behållaren har startats kan `http://localhost:6789/score`du skicka begäranden till .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generera en Dockerfile och beroenden

I följande exempel visas hur du hämtar Dockerfile, modell och andra resurser som behövs för att skapa en avbildning lokalt. Parametern `generate_dockerfile=True` anger att du vill ha filerna, inte en fullt byggd bild.

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

Den här koden hämtar de filer som `imagefiles` behövs för att skapa avbildningen till katalogen. Dockerfile som ingår i de sparade filerna refererar till en basavbildning som lagras i ett Azure-behållarregister. När du skapar avbildningen på den lokala Docker-installationen måste du använda adressen, användarnamnet och lösenordet för att autentisera till registret. Gör så här för att skapa avbildningen med hjälp av en lokal Docker-installation:

1. Från en skal- eller kommandoradssession använder du följande kommando för att autentisera Docker med Azure-behållarregistret. Ersätt `<address>` `<username>`, `<password>` och med de `package.get_container_registry()`värden som hämtas av .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Använd följande kommando om du vill skapa avbildningen. Ersätt `<imagefiles>` med sökvägen till `package.save()` katalogen där filerna sparades.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Det här kommandot anger `myimage`bildnamnet till .

Om du vill kontrollera att `docker images` avbildningen är byggd använder du kommandot. Du bör `myimage` se bilden i listan:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Om du vill starta en ny behållare baserat på den här avbildningen använder du följande kommando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Det här kommandot startar den `myimage`senaste versionen av bilden med namnet . Den mappar lokal port 6789 till porten i behållaren där webbtjänsten lyssnar (5001). Det tilldelar också `mycontainer` namnet till behållaren, vilket gör behållaren lättare att stoppa. När behållaren har startats kan `http://localhost:6789/score`du skicka begäranden till .

### <a name="example-client-to-test-the-local-container"></a>Exempel på klient för att testa den lokala behållaren

Följande kod är ett exempel på en Python-klient som kan användas med behållaren:

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

Klienter i andra programmeringsspråk finns till exempel [Förbruka modeller som distribueras som webbtjänster](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Stoppa Docker-behållaren

Om du vill stoppa behållaren använder du följande kommando från ett annat skal eller kommandorad:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta `service.delete()`bort en distribuerad webbtjänst använder du .
Om du vill ta `model.delete()`bort en registrerad modell använder du .

Mer information finns i dokumentationen för [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) och [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Redigering av avancerade inmatningsskript

<a id="binary"></a>

### <a name="binary-data"></a>Binära data

Om din modell accepterar binära data, till `score.py` exempel en avbildning, måste du ändra filen som används för distributionen för att acceptera råa HTTP-begäranden. Om du vill acceptera `AMLRequest` rådata använder du `@rawhttp` klassen i ditt `run()` inmatningsskript och lägger till dekoratören i funktionen.

Här är ett exempel `score.py` på en som accepterar binära data:

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
> Klassen `AMLRequest` finns i `azureml.contrib` namnområdet. Entiteter i den här namnytan ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namnområdet bör betraktas som en förhandsgranskning som inte stöds fullt ut av Microsoft.
>
> Om du behöver testa detta i den lokala utvecklingsmiljön kan du installera komponenterna med följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Gemensamt för resursdelning (CORS)

Resursdelning med flera ursprung är ett sätt att tillåta att resurser på en webbsida begärs från en annan domän. CORS fungerar via HTTP-huvuden som skickas med klientbegäran och returneras med servicesvaret. Mer information om CORS och giltiga rubriker finns i [Resursdelning över flera ursprung](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) på Wikipedia.

Om du vill konfigurera modelldistributionen `AMLResponse` så att den stöder CORS använder du klassen i ditt postskript. I den här klassen kan du ställa in rubrikerna på svarsobjektet.

I följande exempel `Access-Control-Allow-Origin` anges sidhuvudet för svaret från inmatningsskriptet:

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
> Klassen `AMLResponse` finns i `azureml.contrib` namnområdet. Entiteter i den här namnytan ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namnområdet bör betraktas som en förhandsgranskning som inte stöds fullt ut av Microsoft.
>
> Om du behöver testa detta i den lokala utvecklingsmiljön kan du installera komponenterna med följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en Azure Machine Learning-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelseaviseringar och utlösare för modelldistributioner](how-to-use-event-grid.md)

