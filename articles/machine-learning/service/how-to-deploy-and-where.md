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
ms.openlocfilehash: 796118999041b2bef2d51657901e9e399578e97c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327043"
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
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Mer information finns i klass referens för [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Information om hur du använder en anpassad Docker-avbildning med konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>CLI-exempel på InferenceConfig

Följande JSON-dokument är ett exempel på en konfiguration som kan användas med Machine Learning CLI:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

Följande entiteter är giltiga i den här filen:

* __entryScript__: Sökväg till lokal fil som innehåller koden som ska köras för avbildningen.
* __körning__: Vilken körnings miljö som ska användas för avbildningen. Aktuella körningar som stöds är "Spark-py" och "python".
* __condaFile__ (valfritt): Sökväg till en lokal fil som innehåller en Conda-miljö definition som ska användas för avbildningen.
* __extraDockerFileSteps__ (valfritt): Sökväg till lokal fil som innehåller ytterligare Docker-steg att köra när avbildningen konfigureras.
* __sourceDirectory__ (valfritt): Sökväg till mappar som innehåller alla filer för att skapa avbildningen.
* __enableGpu__ (valfritt): Huruvida GPU-stöd ska aktive ras i avbildningen. GPU-avbildningen måste användas på Microsoft Azure tjänster som Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Standardvärdet är false.
* __baseImage__ (valfritt): En anpassad bild som ska användas som bas avbildning. Om ingen bas avbildning anges används bas avbildningen baserat på den aktuella körnings parametern.
* __baseImageRegistry__ (valfritt): Avbildnings register som innehåller bas avbildningen.
* __cudaVersion__ (valfritt): Version av CUDA som ska installeras för avbildningar som behöver GPU-stöd. GPU-avbildningen måste användas på Microsoft Azure tjänster som Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Versioner som stöds är 9,0, 9,1 och 10,0. Om ' enable_gpu ' är inställt, är standardvärdet ' 9,1 '.

Dessa entiteter mappas till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Thee följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet innehåller konfigurationen följande objekt:

* En katalog som innehåller till gångar som behövs för att kunna översättas
* Den här modellen kräver python
* [Entry-skriptet](#script), som används för att hantera webb förfrågningar som skickas till den distribuerade tjänsten
* Den Conda-fil som beskriver de python-paket som behövs för att kunna utföra en härledning

Information om hur du använder en anpassad Docker-avbildning med konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definiera distributions konfigurationen

Innan du distribuerar måste du definiera distributions konfigurationen. Distributions konfigurationen är specifika för det beräknings mål som ska vara värd för webb tjänsten. När du distribuerar lokalt måste du till exempel ange den port där tjänsten accepterar begär Anden.

Du kan också behöva skapa beräknings resursen. Till exempel, om du inte redan har en Azure Kubernetes-tjänst som är kopplad till din arbets yta.

Följande tabell innehåller ett exempel på hur du skapar en distributions konfiguration för varje beräknings mål:

| Beräkningsmål | Exempel på distributions konfiguration |
| ----- | ----- |
| Lokala | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container-instans | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Följande avsnitt visar hur du skapar distributions konfigurationen och använder den för att distribuera webb tjänsten.

### <a name="optional-profile-your-model"></a>Valfritt: Profilera din modell

Innan du distribuerar din modell som en tjänst kan du profilera den för att fastställa optimala processor-och minnes krav med antingen SDK eller CLI.  Modell profilerings resultaten genereras som ett `Run` -objekt. Du hittar fullständig information om [modell profil schemat i API-dokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

Läs mer om [hur du profilerar din modell med SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-).

Använd [AZ ml modell profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)för att profilera modellen med hjälp av cli.

## <a name="deploy-to-target"></a>Distribuera till mål

### <a id="local"></a>Lokal distribution

Om du vill distribuera lokalt måste du ha **Docker installerat** på den lokala datorn.

+ **Använda SDK: n**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Använda CLI**

    Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Posterna i `deploymentconfig.json` dokument översikten till parametrarna för [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

    | JSON-entitet | Metod parameter | Beskrivning |
    | ----- | ----- | ----- |
    | `computeType` | Ej tillämpligt | Beräkningsmålet. För lokal måste värdet vara `local`. |
    | `port` | `port` | Den lokala porten där tjänstens HTTP-slutpunkt ska exponeras. |

    Följande JSON är ett exempel på en distributions konfiguration för användning med CLI:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Använd Azure Container Instances för att distribuera dina modeller som en webbtjänst om en eller flera av följande villkor är uppfyllt:
- Du behöver att snabbt distribuera och verifiera din modell.
- Du testar en modell som är under utveckling. 

Information om kvot-och regions tillgänglighet för ACI finns i avsnittet [kvoter och region tillgänglighet för Azure Container instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artikel.

+ **Använda SDK: n**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Använda CLI**

    Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    Posterna i `deploymentconfig.json` dokument översikten till parametrarna för [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

    | JSON-entitet | Metod parameter | Beskrivning |
    | ----- | ----- | ----- |
    | `computeType` | Ej tillämpligt | Beräkningsmålet. För ACI måste värdet vara `ACI`. |
    | `containerResourceRequirements` | Ej tillämpligt | Innehåller konfigurations element för processor och minne som allokerats för behållaren. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Antalet processor kärnor som ska allokeras för den här webb tjänsten. Appar`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) som ska allokeras för den här webb tjänsten. Objekt`0.5` |
    | `location` | `location` | Azure-regionen som den här webservicen ska distribueras till. Om den inte anges kommer arbets ytans plats användas. Du hittar mer information om tillgängliga regioner här: [ACI regioner](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | Huruvida autentisering ska aktive ras för den här webbtjänsten. Standardvärdet är false |
    | `sslEnabled` | `ssl_enabled` | Huruvida SSL ska aktive ras för den här webbtjänsten. Standardvärdet är false. |
    | `appInsightsEnabled` | `enable_app_insights` | Huruvida AppInsights ska aktive ras för den här webbtjänsten. Standardvärdet är false |
    | `sslCertificate` | `ssl_cert_pem_file` | Certifikat filen som behövs om SSL är aktiverat |
    | `sslKey` | `ssl_key_pem_file` | Nyckel filen som behövs om SSL är aktiverat |
    | `cname` | `ssl_cname` | CNAME för om SSL är aktiverat |
    | `dnsNameLabel` | `dns_name_label` | DNS-namnets etikett för bedömnings slut punkten. Om du inte anger en unik DNS-benämning kommer att genereras för bedömnings slut punkten. |

    Följande JSON är ett exempel på en distributions konfiguration för användning med CLI:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **Använda VS Code**

  Om du vill [Distribuera modeller med vs Code](how-to-vscode-tools.md#deploy-and-manage-models) behöver du inte skapa en ACI-behållare för att testa i förväg, eftersom ACI-behållare skapas i farten.

Mer information finns i referensdokumentationen för den [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) och [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) klasser.

### <a id="aks"></a>Azure Kubernetes service (DEVTEST & produktion)

Du kan använda ett befintligt AKS-kluster eller skapa en ny med SDK: N för Azure Machine Learning, CLI eller Azure-portalen.

<a id="deploy-aks"></a>

Om du redan har ett AKS-kluster anslutet kan du distribuera det till det. Om du inte har skapat eller kopplat ett AKS-kluster följer du processen för att <a href="#create-attach-aks">skapa ett nytt AKS-kluster</a>.

+ **Använda SDK: n**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Använda CLI**

    Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `myaks` med namnet på AKS Compute Target. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Posterna i `deploymentconfig.json` dokument översikten till parametrarna för [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

    | JSON-entitet | Metod parameter | Beskrivning |
    | ----- | ----- | ----- |
    | `computeType` | Ej tillämpligt | Beräkningsmålet. För AKS måste värdet vara `aks`. |
    | `autoScaler` | Ej tillämpligt | Innehåller konfigurations element för autoskalning. Se tabellen för autoskalning. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Huruvida autoskalning ska aktive ras för webb tjänsten. Om `numReplicas` , = i annat fall`False`. `0` `True` |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Det minsta antal behållare som ska användas när den här webb tjänsten autoskalas. Standard, `1`. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Det maximala antal behållare som ska användas när den här webb tjänsten autoskalas. Standard, `10`. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hur ofta den automatiska skalnings tjänsten försöker skala den här webb tjänsten. Standard, `1`. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Mål användningen (i procent av 100) som autoskalning ska försöka underhålla för den här webb tjänsten. Standard, `70`. |
    | `dataCollection` | Ej tillämpligt | Innehåller konfigurations element för data insamling. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Huruvida modell data insamling ska aktive ras för webb tjänsten. Standard, `False`. |
    | `authEnabled` | `auth_enabled` | Huruvida autentisering ska aktive ras för webb tjänsten. Standard, `True`. |
    | `containerResourceRequirements` | Ej tillämpligt | Innehåller konfigurations element för processor och minne som allokerats för behållaren. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Antalet processor kärnor som ska allokeras för den här webb tjänsten. Appar`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) som ska allokeras för den här webb tjänsten. Objekt`0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Huruvida Application Insights loggning ska aktive ras för webb tjänsten. Standard, `False`. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | En tids gräns för att genomdriva för Poäng anrop till webb tjänsten. Standard, `60000`. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maximalt antal samtidiga förfrågningar per nod för den här webb tjänsten. Standard, `1`. |
    | `maxQueueWaitMs` | `max_request_wait_time` | Den längsta tid som en begäran kommer att finnas kvar i Thee-kön (i millisekunder) innan ett 503-fel returneras. Standard, `500`. |
    | `numReplicas` | `num_replicas` | Antalet behållare som ska allokeras för den här webb tjänsten. Inget standardvärde. Om den här parametern inte anges är autoskalning aktive rad som standard. |
    | `keys` | Ej tillämpligt | Innehåller konfigurations element för nycklar. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | En primär autentiserings nyckel som ska användas för den här webbtjänsten |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | En sekundär autentiserings nyckel som ska användas för den här webbtjänsten |
    | `gpuCores` | `gpu_cores` | Antalet GPU-kärnor som ska allokeras för den här webbtjänsten. Standardvärdet är 1. |
    | `livenessProbeRequirements` | Ej tillämpligt | Innehåller konfigurations element för Live-avsöknings krav. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | Hur ofta (i sekunder) som utsökningen av Direktmigrering ska utföras. Standardvärdet är 10 sekunder. Minimalt värde är 1. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Antal sekunder efter att containern har startats innan direktmigreringen avsökningar initieras. Standardvärdet är 310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Antal sekunder efter vilket tids gränsen för direktmigreringen avsökningen uppnåddes. Standardvärdet är 2 sekunder. Minimalt värde är 1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | Minimi kraven på efterföljande lyckade försök för att avsökningen av Direktmigrering ska anses vara lyckad efter att ha misslyckats. Standardvärdet är 1. Minimalt värde är 1. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | När en POD startar och Live-avsökningen Miss lyckas, kommer Kubernetes att försöka failureThreshold gånger innan den ger upp. Standardvärdet är 3. Minimalt värde är 1. |
    | `namespace` | `namespace` | Kubernetes-namnområdet som webservicen distribueras till. Upp till 63 gemena alfanumeriska tecken ("a-z", "0"-' 9 ') och bindestreck (-). Det första och sista tecknet får inte vara bindestreck. |

    Följande JSON är ett exempel på en distributions konfiguration för användning med CLI:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **Använda VS Code**

  Du kan också [distribuera till AKS via vs Code-tillägget](how-to-vscode-tools.md#deploy-and-manage-models), men du måste konfigurera AKS-kluster i förväg.

Läs mer om AKS-distribution och autoskalning i referensen [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) .

#### Skapa ett nytt AKS-kluster<a id="create-attach-aks"></a>
**Tids uppskattning**: Ungefär 20 minuter.

Att skapa eller ansluta ett AKS-kluster är en process för arbets ytan. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resurs gruppen som innehåller den måste du skapa ett nytt kluster nästa gången du behöver distribuera. Du kan ha flera AKS-kluster kopplade till din arbets yta.

Om du vill skapa ett AKS-kluster för utveckling, verifiering och testning ställer du in `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` när du använder. [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) Ett kluster som skapas med den här inställningen kommer bara att ha en nod.

> [!IMPORTANT]
> Inställningen `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` skapar ett AKS-kluster som inte är lämpligt för hantering av produktions trafik. En härlednings tid kan vara längre än på ett kluster som skapats för produktion. Fel tolerans garanterar inte heller för utvecklings-och test kluster.
>
> Vi rekommenderar att kluster som skapats för utveckling/testning använder minst två virtuella processorer.

Följande exempel visar hur du skapar ett nytt Azure Kubernetes service-kluster:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Mer information om hur du skapar ett AKS-kluster utanför Azure Machine Learning SDK finns i följande artiklar:
* [Skapa ett AKS-kluster](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Skapa ett AKS-kluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Mer information om `cluster_purpose` parametern finns i referensen [AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) .

> [!IMPORTANT]
> [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)Om du väljer anpassade värden för agent_count och vm_size måste du se till att agent_count multiplicerat med vm_size är större än eller lika med 12 virtuella processorer. Om du till exempel använder en vm_size av "Standard_D3_v2", som har 4 virtuella processorer, bör du välja en agent_count på 3 eller senare.
>
> Azure Machine Learning SDK ger inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. Du kan bara ändra antalet noder, inte klustrets virtuella dator storlek.

#### <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster
**Tids uppskattning:** Cirka 5 minuter.

Om du redan har AKS-kluster i din Azure-prenumeration och det är version 1.12. # # kan du använda det för att distribuera avbildningen.

> [!WARNING]
> När du kopplar ett AKS-kluster till en arbets yta kan du definiera hur du ska använda klustret genom att ange `cluster_purpose` parametern.
>
> Om du inte anger `cluster_purpose` parametern eller anger `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`så måste klustret ha minst 12 virtuella processorer tillgängliga.
>
> Om du ställer `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`in behöver klustret inte ha 12 virtuella processorer. Ett kluster som har kon figurer ATS för utveckling/testning är dock inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider.

Följande kod visar hur du kopplar en befintlig AKS-1.12. # #-kluster till din arbets yta:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Mer information `attack_configuration()`finns i referensen [AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) .

Mer information om `cluster_purpose` parametern finns i referensen [AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) .

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webb tjänst tillhandahåller en REST API, så att du kan skapa klient program på flera olika programmeringsspråk. Om du har aktiverat autentisering för tjänsten måste du ange en tjänst nyckel som en token i ditt begär ande huvud.

### <a name="request-response-consumption"></a>Användning av begäran-svar

Här är ett exempel på hur du anropar din tjänst i python:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
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

När du skapar en ny modell måste du manuellt uppdatera varje tjänst som du vill använda den nya modellen för. Om du vill uppdatera webbtjänsten använder den `update` metoden. Följande kod visar hur du uppdaterar webb tjänsten för att använda en ny modell:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

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

