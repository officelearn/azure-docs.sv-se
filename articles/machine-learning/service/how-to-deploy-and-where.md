---
title: Hur och var du vill distribuera modeller
titleSuffix: Azure Machine Learning service
description: 'Lär dig hur och var att distribuera dina modeller för Azure Machine Learning-tjänsten som bland annat: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge och Field-programmable gate-matriser.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: fb23e61142a639420d74c08e5a9a41324acab18b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706284"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Lär dig hur du distribuerar din machine learning-modell som en webbtjänst i Azure-molnet, eller IoT Edge-enheter. 

Arbetsflödet är liknande oavsett [där du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbereda för distribution av (Ange tillgångar, användning, beräkningsmål)
1. Distribuera modellen till beräkningsmål.
1. Testa den distribuerade modellen, även kallat webbtjänsten.

Mer information om begrepp som ingår i arbetsflödet finns i [hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

- En modell. Om du inte har en tränad modell, du kan använda modellen och beroendefiler som anges i [den här självstudien](https://aka.ms/azml-deploy-cloud).

- Den [Azure CLI-tillägg för Machine Learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk), eller [Azure Machine Learning Visual Studio Code-tillägg](how-to-vscode-tools.md).

## <a id="registermodel"></a> Registrera din modell

En logisk behållare registrerade modellen för en eller flera filer som utgör din modell. Om du har en modell som lagras i flera filer, kan du exempelvis registrera dem som en enda modell på arbetsytan. Efter registreringen, kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som registrerades.

Machine learning-modeller är registrerade i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller kan komma från en annan plats. Följande exempel visar hur du registrerar en modell från filen:

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från ett Experiment som kör

+ **Scikit-Läs exempel med hjälp av SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Om du vill ta med flera filer i modellen registreringen, ange `model_path` till den katalog som innehåller filerna.

+ **Med hjälp av CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Om du vill ta med flera filer i modellen registreringen, ange `--asset-path` till den katalog som innehåller filerna.

+ **Använder VS Code**

  Registrera modeller med modellfiler eller mappar med den [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) tillägget.

### <a name="register-an-externally-created-model"></a>Registrera ett externt skapade modellen

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Du kan registrera ett externt skapade modellen genom att tillhandahålla en **lokal sökväg** i modellen. Du kan ange en mapp eller en enskild fil.

+ **Exempel på ONNX med Python-SDK:**
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
  > Om du vill ta med flera filer i modellen registreringen, ange `model_path` till den katalog som innehåller filerna.

+ **Med hjälp av CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Om du vill ta med flera filer i modellen registreringen, ange `-p` till den katalog som innehåller filerna.

**Uppskattad tidsåtgång**: Cirka 10 sekunder.

Mer information finns i referensdokumentationen för den [Modellklass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller tränas utanför Azure Machine Learning-tjänsten, finns i [så här distribuerar du en befintlig modell](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Välj ett beräkningsmål

Följande beräkningsmål, eller beräkna resurser, kan användas som värd för din distribution av webbtjänster. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Förbered distributionen

Om du vill distribuera som en webbtjänst, måste du skapa en konfiguration för inferens (`InferenceConfig`) och en distributionskonfiguration. Inferens eller modell bedömning är fasen där distribuerade modellen används för förutsägelse oftast på produktionsdata. I inferens-config anger du de skript och beroenden som behövs för att hantera din modell. I distributionskonfiguration anger du information om hur du hantera modellen på beräkningsmål.


### <a id="script"></a> 1. Definiera din post skript och beroenden

Posten skriptet tar emot data som skickas till en distribuerad webbtjänst och skickar dem till modellen. Sedan tar svaret som returnerades av modellen och returnerar som till klienten. **Skriptet är specifik för din modell**; den måste förstå de data som modellen förväntas och returnerar.

Skriptet innehåller två funktioner som att läsa in och kör modellen:

* `init()`: Den här funktionen läses vanligtvis in modellen till ett globala objekt. Den här funktionen körs en gång när Docker-behållare för webbtjänsten har startats.

* `run(input_data)`: Den här funktionen använder modellen för att förutsäga ett värde baserat på indata. Indata och utdata kör du vanligtvis använda JSON för serialisering och deserialisering. Du kan också arbeta med binära rådata. Du kan omvandla data innan du skickar till modellen eller innan det returneras till klienten.

#### <a name="what-is-getmodelpath"></a>Vad är get_model_path?

När du registrerar en modell kan ange du ett modellnamn som används för att hantera modellen i registret. Du använder det här namnet med den [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) att hämta sökvägen till filen modellen på det lokala filsystemet. Den här API returnerar sökvägen till den katalog som innehåller de filerna om du registrerar en mapp eller en uppsättning filer.

När du registrerar en modell kan ge du den ett namn som motsvarar där modellen är placerad, antingen lokalt eller under distributionen av tjänster.

I exemplet nedan returnerar en sökväg till en enskild fil kallad `sklearn_mnist_model.pkl` (som har registrerats med namnet `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(Valfritt) Automatisk generering av Swagger-schemat

För att automatiskt genererar ett schema för webbtjänsten, anger ett exempel på indata och utdata i konstruktorn för en definierad typ.-objekt, typ och exempel för att automatiskt skapa schemat. Azure Machine Learning-tjänsten skapar sedan en [OpenAPI](https://swagger.io/docs/specification/about/) -specifikationen (Swagger) för webbtjänsten under distributionen.

Följande typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* standard Python-objekt

Om du vill använda scheman innehåller den `inference-schema` paketet i filen conda-miljö. I följande exempel används `[numpy-support]` eftersom posten skriptet använder en numpy parametertyp: 

#### <a name="example-dependencies-file"></a>Exempelfil för beroenden
Följande YAML är ett exempel på en fil med Conda-beroenden för inferens.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Om du vill använda automatiska schemat generering, post skriptet **måste** importera den `inference-schema` paket. 

Definiera indata och utdataformat exemplet i den `input_sample` och `output_sample` variabler, som representerar format för begäran och svar för webbtjänsten. Använd de här exemplen i indata och utdata funktionen dekoratörer på den `run()` funktion. Scikit-Läs exemplet nedan används scheman.

> [!TIP]
> När du distribuerar tjänsten kan använda den `swagger_uri` egenskap för att hämta JSON-dokument schématu.

#### <a name="example-entry-script"></a>Exempel på post-skript

I följande exempel visar hur du godkänner och returnerar JSON-data:

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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Exempelskript med ordlistan indata (stöd för förbrukning från Power BI)

Följande exempel visar hur du definierar indata som < nyckel: värde > ordlista, med hjälp av dataramar. Den här metoden stöds för användning av den distribuerade webbtjänsten från Power BI ([Läs mer om hur du förbrukar webbtjänsten från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
Fler exempelskript finns i följande exempel:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Bedömning mot binära data: [Hur du använder en webbtjänst](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definiera din InferenceConfig

Konfigurationen av inferens beskriver hur du konfigurerar modellen för att göra förutsägelser. I följande exempel visar hur du skapar en inferens konfiguration. Den här konfigurationen anger körningen, post-skript och (valfritt) filen conda-miljö:

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Mer information finns i den [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) klassen referens.

Information om hur du använder en anpassad Docker-avbildning med inferens konfiguration finns i [hur du distribuerar en modell med en anpassad dockeravbildning](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>CLI-exempel på InferenceConfig

Följande JSON-dokumentet är en exempelkonfiguration inferens för användning med de machine learning CLI:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

Följande entiteter är giltiga i den här filen:

* __entryScript__: Sökväg till en lokal fil som innehåller koden för att köra för avbildningen.
* __Runtime__: Vilka runtime för avbildningen. Aktuella körningar som stöds är spark-py och ”python”.
* __condaFile__ (valfritt): Sökväg till en lokal fil som innehåller en definition av conda miljö som ska användas för bilden.
* __extraDockerFileSteps__ (valfritt): Sökväg till en lokal fil som innehåller ytterligare Docker-åtgärder för att köra när du konfigurerar avbildningen.
* __sourceDirectory__ (valfritt): Sökväg till mappar som innehåller alla filer för att skapa avbildningen.
* __enableGpu__ (valfritt): Om du ska aktivera GPU eller inte har stöd i avbildningen. GPU-avbildningen måste användas på Microsoft Azure-tjänster som Azure Container Instances, beräkning av Azure Machine Learning, Azure virtuella datorer och Azure Kubernetes Service. Standardvärdet är FALSKT.
* __baseImage__ (valfritt): En anpassad avbildning som ska användas som basavbildning. Om inga basavbildningen anges används basavbildningen baserat ut från de angivna runtime-parametern.
* __baseImageRegistry__ (valfritt): Avbildningsregister som innehåller basavbildningen.
* __cudaVersion__ (valfritt): Version av CUDA du installerar för avbildningar som måste GPU-stöd. GPU-avbildningen måste användas på Microsoft Azure-tjänster som Azure Container Instances, beräkning av Azure Machine Learning, Azure virtuella datorer och Azure Kubernetes Service. Versioner som stöds är 9.0, 9.1 och 10.0. Om ”enable_gpu” anges som standard ”9.1'.

Dessa entiteter mappar till parametrarna för den [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) klass.

Dig följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet innehåller konfigurationen följande objekt:

* En katalog som innehåller resurser som behövs för att inferens
* Att den här modellen kräver Python
* Den [post skriptet](#script), som används för att hantera begäranden skickas till den distribuerade tjänsten
* Conda-fil som beskriver Python-paket som behövs för att inferens

Information om hur du använder en anpassad Docker-avbildning med inferens konfiguration finns i [hur du distribuerar en modell med en anpassad dockeravbildning](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definiera din distributionskonfiguration

Innan du distribuerar, måste du definiera distributionskonfigurationen. Distributionskonfigurationen är specifik för beräkningsmål som är värd för webbtjänsten. När du distribuerar lokalt måste du ange den port där tjänsten tar emot förfrågningar.

Du kan även behöva skapa beräkningsresursen. Till exempel om du gör inte redan har ett Azure Kubernetes Service som är associerade med din arbetsyta.

I följande tabell innehåller ett exempel på hur du skapar en distributionskonfiguration för varje beräkningsmål:

| Beräkningsmål | Konfigurationsexempel för distribution |
| ----- | ----- |
| Lokala | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container-instans | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

I följande avsnitt visar hur du skapar distributionskonfigurationen och sedan använda den för att distribuera webbtjänsten.

### <a name="optional-profile-your-model"></a>Valfritt: Profilera din modell
Innan du distribuerar modellen som en tjänst, kan du profilera den för att fastställa optimal processor och minneskrav med SDK eller CLI.  Modeller profilering resultat genereras som en `Run` objekt. Fullständig information om [modellen profil-schemat finns i API-dokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

Läs mer på [hur du profilera din modell med hjälp av SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)

## <a name="deploy-to-target"></a>Distribuera till mål

### <a id="local"></a> Lokal distribution

Om du vill distribuera lokalt, måste du ha **Docker installerat** på den lokala datorn.

+ **Med hjälp av SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Med hjälp av CLI**

    Om du vill distribuera med hjälp av CLI, använder du följande kommando. Ersätt `mymodel:1` med namnet och versionen på den registrerade modellen:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Posterna i den `deploymentconfig.json` dokumentöversikten till parametrarna för [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrar för metoden:

    | JSON-entitet | Metodparameter | Beskrivning |
    | ----- | ----- | ----- |
    | `computeType` | Ej tillämpligt | Beräkningsmålet. För lokal, värdet måste vara `local`. |
    | `port` | `port` | Den lokala porten som du vill exponera tjänstens HTTP-slutpunkt. |

    Följande JSON är en exempelkonfiguration distribution för användning med CLI:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a> Azure Container Instances (DEVTEST)

Använd Azure Container Instances för att distribuera dina modeller som en webbtjänst om en eller flera av följande villkor är uppfyllt:
- Du behöver att snabbt distribuera och verifiera din modell.
- Du testar en modell som är under utveckling. 

Kvoter och regional tillgänglighet för ACI finns i den [kvoter och regiontillgänglighet för Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artikeln.

+ **Med hjälp av SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Med hjälp av CLI**

    Om du vill distribuera med hjälp av CLI, använder du följande kommando. Ersätt `mymodel:1` med namnet och versionen på den registrerade modellen. Ersätt `myservice` med namnet att ge den här tjänsten:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    Posterna i den `deploymentconfig.json` dokumentöversikten till parametrarna för [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrar för metoden:

    | JSON-entitet | Metodparameter | Beskrivning |
    | ----- | ----- | ----- |
    | `computeType` | Ej tillämpligt | Beräkningsmålet. Värdet måste vara för ACI, `ACI`. |
    | `containerResourceRequirements` | Ej tillämpligt | Innehåller konfigurationselement för CPU och minne som allokerats för behållaren. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Antal processorkärnor att allokera för den här webbtjänsten. Som standard, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) att allokera för den här webbtjänsten. Standard `0.5` |
    | `location` | `location` | Azure-regionen att distribuera den här webbtjänsten till. Om inget anges arbetsytan plats kommer att användas. Mer information om tillgängliga regioner hittar du här: [ACI-regioner](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | Om eller inte att aktivera autentisering för den här webbtjänsten. Standardvärdet är FALSKT |
    | `sslEnabled` | `ssl_enabled` | Om eller inte att aktivera SSL för den här webbtjänsten. Standardvärdet är FALSKT. |
    | `appInsightsEnabled` | `enable_app_insights` | Om eller inte att aktivera AppInsights för den här webbtjänsten. Standardvärdet är FALSKT |
    | `sslCertificate` | `ssl_cert_pem_file` | Certifikatfilen behövs om SSL är aktiverat |
    | `sslKey` | `ssl_key_pem_file` | Nyckelfilen behövs om SSL är aktiverat |
    | `cname` | `ssl_cname` | Cname för om SSL är aktiverat |
    | `dnsNameLabel` | `dns_name_label` | Dns-Namnetiketten för bedömnings-slutpunkten. Om inte anges kommer att genereras ett unikt dns-Namnetiketten för bedömnings-slutpunkten. |

    Följande JSON är en exempelkonfiguration distribution för användning med CLI:

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

+ **Använder VS Code**

  Att [distribuera dina modeller med VS Code](how-to-vscode-tools.md#deploy-and-manage-models) du behöver inte skapa en ACI-behållare för att testa i förväg, eftersom ACI behållare skapas i farten.

Mer information finns i referensdokumentationen för den [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) och [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) klasser.

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST och produktion)

Du kan använda ett befintligt AKS-kluster eller skapa en ny med SDK: N för Azure Machine Learning, CLI eller Azure-portalen.

<a id="deploy-aks"></a>

Om du redan har ett AKS-kluster som är ansluten kan du distribuera till den. Om du inte har skapat eller kopplat ett AKS-kluster, följer du processen att <a href="#create-attach-aks">skapar ett nytt kluster i AKS</a>.

+ **Med hjälp av SDK**

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

+ **Med hjälp av CLI**

    Om du vill distribuera med hjälp av CLI, använder du följande kommando. Ersätt `myaks` med namnet på AKS beräkningsmålet. Ersätt `mymodel:1` med namnet och versionen på den registrerade modellen. Ersätt `myservice` med namnet att ge den här tjänsten:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Posterna i den `deploymentconfig.json` dokumentöversikten till parametrarna för [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrar för metoden:

    | JSON-entitet | Metodparameter | Beskrivning |
    | ----- | ----- | ----- |
    | `computeType` | Ej tillämpligt | Beräkningsmålet. Värdet måste vara för AKS, `aks`. |
    | `autoScaler` | Ej tillämpligt | Innehåller konfigurationselement för autoskalning. Se tabellen autoskalningen. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Om du ska aktiveras eller inte automatisk skalning för webbtjänsten. Om `numReplicas`  =  `0`, `True`, annars `False`. |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Det minsta antalet behållare ska användas när automatisk skalning den här webbtjänsten. Som standard, `1`. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Det maximala antalet behållare ska användas när automatisk skalning den här webbtjänsten. Som standard, `10`. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hur ofta autoskalningen försöker skala den här webbtjänsten. Som standard, `1`. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Den målanvändning (i procent av 100) som autoskalningen ska försöka att underhålla för den här webbtjänsten. Som standard, `70`. |
    | `dataCollection` | Ej tillämpligt | Innehåller konfigurationselement för insamling av data. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Om du ska aktiveras eller inte insamling av modelldata för webbtjänsten. Som standard, `False`. |
    | `authEnabled` | `auth_enabled` | Om eller inte vill aktivera autentisering för webbtjänsten. Som standard, `True`. |
    | `containerResourceRequirements` | Ej tillämpligt | Innehåller konfigurationselement för CPU och minne som allokerats för behållaren. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Antal processorkärnor att allokera för den här webbtjänsten. Som standard, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) att allokera för den här webbtjänsten. Standard `0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Om du ska aktiveras eller inte Application Insights-loggning för webbtjänsten. Som standard, `False`. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | En tidsgräns för att upprätthålla för bedömning anrop till webbtjänsten. Som standard, `60000`. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Högsta antal samtidiga begäranden per nod för den här webbtjänsten. Som standard, `1`. |
    | `maxQueueWaitMs` | `max_request_wait_time` | Den längsta tid som en begäran att stanna kvar i dig kön (i millisekunder) innan en 503 fel returneras. Som standard, `500`. |
    | `numReplicas` | `num_replicas` | Antal behållare för att allokera för den här webbtjänsten. Inget standardvärde. Om den här parametern inte har angetts är autoskalningen aktiverad som standard. |
    | `keys` | Ej tillämpligt | Innehåller konfigurationselement för nycklar. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | En primär auth-nyckel som ska användas för den här webbtjänsten |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | En sekundär auth-nyckel som ska användas för den här webbtjänsten |
    | `gpuCores` | `gpu_cores` | Antal GPU-kärnor för att allokera för den här webbtjänsten. Standardvärdet är 1. |
    | `livenessProbeRequirements` | Ej tillämpligt | Innehåller konfigurationselement för liveness avsökningen krav. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | Hur ofta (i sekunder) att utföra hälsoavsökningen. Som standard 10 sekunder. Lägsta värdet är 1. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Antal sekunder efter att behållaren har startat innan liveness avsökningar initieras. Standardvärdet är 310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Antal sekunder efter hälsoavsökningen når sin tidsgräns. Standardvärdet är 2 sekunder. Lägsta värde är 1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | Minsta på varandra följande lyckade för hälsoavsökningen ska anses lyckat efter att ha misslyckades. Standardvärdet är 1. Lägsta värdet är 1. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | När en Pod startas och hälsoavsökningen misslyckas, försöker failureThreshold gånger innan den ger upp med Kubernetes. Standardvärdet är 3. Lägsta värdet är 1. |
    | `namespace` | `namespace` | Kubernetes-namnområde som webbtjänsten distribueras till. Upp till 63 gemena alfanumeriska (”a”-”z”, ”0”-”9”) och bindestreck (”-”) tecken. De första och sista tecknet får inte vara bindestreck. |

    Följande JSON är en exempelkonfiguration distribution för användning med CLI:

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

+ **Använder VS Code**

  Du kan också [distribuera till AKS via VS Code-tillägg](how-to-vscode-tools.md#deploy-and-manage-models), men du måste konfigurera AKS-kluster i förväg.

Läs mer om AKS-distributionen och skala automatiskt i den [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) referens.

#### Skapa ett nytt AKS-kluster<a id="create-attach-aks"></a>
**Uppskattad tidsåtgång**: Ungefär 20 minuter.

Skapa eller koppla ett AKS-kluster är en gång bearbetar för din arbetsyta. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resursgruppen som innehåller den, måste du skapa ett nytt kluster nästa gång du behöver distribuera. Du kan ha flera AKS-kluster som är kopplat till din arbetsyta.

Om du vill skapa ett AKS-kluster för utveckling, verifieringen och testningen kan du ställa in `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` när du använder [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Ett kluster som skapats med den här inställningen har endast en nod.

> [!IMPORTANT]
> Ange `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` skapar ett AKS-kluster som inte passar för hantering av produktionstrafik. Inferens tider kan vara längre än i ett kluster som skapats för produktion. Feltolerans garanteras inte också för utveckling/testning-kluster.
>
> Vi rekommenderar att kluster som skapas för utveckling och testning använder minst två virtuella processorer.

I följande exempel visar hur du skapar ett nytt Azure Kubernetes Service-kluster:

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

Mer information om den `cluster_purpose` parameter, finns i den [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) referens.

> [!IMPORTANT]
> För [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), om du väljer Anpassad värden för agent_count och vm_size, måste du se till att agent_count multiplicerat med vm_size är större än eller lika med 12 virtuella processorer. Till exempel om du använder en vm_size av ”Standard_D3_v2” som har 4 virtuella processorer, bör sedan du välja en agent_count 3 eller högre.
>
> SDK: N för Azure Machine Learning ger inte stöd för skalning ett AKS-kluster. Om du vill skala noderna i klustret, använder du Användargränssnittet för AKS-kluster i Azure-portalen. Du kan bara ändra antalet noder, inte VM-storleken för klustret.

#### <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster
**Uppskattad tidsåtgång:** Cirka 5 minuter.

Om du redan har AKS-kluster i Azure-prenumerationen och det är version 1.12. ##, du kan använda den för att distribuera din avbildning.

> [!WARNING]
> När du ansluter ett AKS-kluster till en arbetsyta, kan du definiera hur du använder klustret genom att ange den `cluster_purpose` parametern.
>
> Om du inte anger den `cluster_purpose` parametern eller ange `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, klustret måste ha minst 12 virtuella processorer som är tillgängliga.
>
> Om du ställer in `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, och sedan klustret inte behöver ha 12 virtuella processorer. Men ett kluster som har konfigurerats för utveckling och testning kan inte lämplig för trafiken på nätverksnivå för produktion och kan öka inferens gånger.

Följande kod visar hur du ansluter en befintlig AKS 1.12. ## klustret till din arbetsyta:

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

Mer information om `attack_configuration()`, finns i den [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) referens.

Mer information om den `cluster_purpose` parameter, finns i den [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) referens.

## <a name="consume-web-services"></a>Konsumera webbtjänster

Varje distribuerad webbtjänst har en REST-API så du kan skapa klientprogram på flera olika programmeringsspråk. Om du har aktiverat autentisering för din tjänst, måste du ange en nyckel som en token i förfrågans sidhuvud.

### <a name="request-response-consumption"></a>Begäran / svar-förbrukning

Här är ett exempel på hur du anropar din tjänst i Python:
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

Mer information finns i [skapa klient program kan använda webservices](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Batch inferens
Azure Machine Learning Compute mål skapas och hanteras av Azure Machine Learning-tjänsten. De kan användas för batch-förutsägelse från Azure Machine Learning Pipelines.

En genomgång av batch inferens med beräkning av Azure Machine Learning finns det [hur du kör Batch-förutsägelser](how-to-run-batch-predictions.md) artikeln.

### <a id="iotedge"></a> IoT Edge inferens
Stöd för att distribuera till edge genomgår förhandsgranskning. Mer information finns i den [distribuera Azure Machine Learning som en IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) artikeln.


## <a id="update"></a> Uppdatera webbtjänster

När du skapar en ny modell, måste du manuellt uppdatera varje tjänst som du vill använda den nya modellen. Om du vill uppdatera webbtjänsten använder den `update` metoden. Följande kod visar hur du uppdaterar webbtjänsten för att använda en ny modell:

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

## <a name="continuous-model-deployment"></a>Kontinuerlig modelldistribution 

Du kan distribuera modeller med Machine Learning-tillägget för kontinuerligt [Azure DevOps](https://azure.microsoft.com/services/devops/). Med Machine Learning-tillägget för Azure DevOps kan utlösa du en pipeline för distribution när en ny maskininlärningsmodell är registrerad i Azure Machine Learning-tjänstens arbetsyta. 

1. Registrera dig för [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), vilket möjliggör kontinuerlig integrering och leverans av ditt program till valfri plattform och alla moln. Azure Pipelines [skiljer sig från ML pipelines](concept-ml-pipelines.md#compare). 

1. [Skapa ett Azure DevOps-projekt.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installera den [Machine Learning-tillägg för Azure-Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Använd __anslutningar__ att ställa in en service principal anslutning till din arbetsyta för Azure Machine Learning-tjänsten för att få åtkomst till alla artefakter. Gå till inställningar, klicka på Tjänstanslutningar och välj Azure Resource Manager.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. Definiera AzureMLWorkspace som den __omfång nivå__ och Fyll i efterföljande parametrar.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. För att kontinuerligt distribuera din modell för maskininlärning med hjälp av Azure-Pipelines, under pipelines Välj __viktig__. Lägg till en ny artefakt, Välj modell för AzureML-artefakt och tjänst-anslutningen som har skapats i det tidigare steget. Välj modell och version som ska utlösa en distribution. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Aktivera utlösaren modellen på modell-artefakt. Genom att aktivera utlösaren och varje gång den angivna versionen (dvs.) den senaste versionen) för den modellen bara registrera dig på din arbetsyta, en Azure DevOps-releasepipeline utlöses. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

-Exempelprojekt och exempel finns i [MLOps-databasen](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.
Ta bort registrerade modellen genom att använda `model.delete()`.

Mer information finns i referensdokumentationen för [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), och [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Nästa steg
* [Hur du distribuerar en modell med en anpassad dockeravbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)

