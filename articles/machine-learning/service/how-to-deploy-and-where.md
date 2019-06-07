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
ms.date: 05/31/2019
ms.custom: seoapril2019
ms.openlocfilehash: 89539509e759da7f041ce0216397b1a9c8ff1f16
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753082"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Lär dig hur du distribuerar din machine learning-modell som en webbtjänst i Azure-molnet, eller IoT Edge-enheter. 

Arbetsflödet är liknande oavsett [där du distribuerar](#target) din modell:

1. Registrera modellen.
1. Förbereda för distribution av (Ange tillgångar, användning, beräkningsmål)
1. Distribuera modellen till beräkningsmål.
1. Testa den distribuerade modellen, även kallat webbtjänsten.

Mer information om begrepp som ingår i arbetsflödet finns i [hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En modell. Om du inte har en tränad modell, du kan använda modellen och beroendefiler som anges i [den här självstudien](https://aka.ms/azml-deploy-cloud).

- Den [Azure CLI-tillägg för Machine Learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk), eller [Azure Machine Learning Visual Studio Code-tillägg](how-to-vscode-tools.md).

## <a id="registermodel"></a> Registrera din modell

Registrera dina maskininlärningsmodeller i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller kan komma från en annan plats. Följande exempel visar hur du registrerar en modell från filen:

### <a name="register-a-model-from-an-experiment-run"></a>Registrera en modell från ett Experiment som kör

+ **Scikit-Läs exempel med hjälp av SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```
+ **Med hjälp av CLI**
  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```


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

+ **Med hjälp av CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

**Uppskattad tidsåtgång**: Cirka 10 sekunder.

Mer information finns i referensdokumentationen för den [Modellklass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

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

Konfigurationen av inferens beskriver hur du konfigurerar modellen för att göra förutsägelser. I följande exempel visar hur du skapar en inferens konfiguration:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

I det här exemplet innehåller konfigurationen följande objekt:

* En katalog som innehåller resurser som behövs för att inferens
* Att den här modellen kräver Python
* Den [post skriptet](#script), som används för att hantera begäranden skickas till den distribuerade tjänsten
* Conda-fil som beskriver Python-paket som behövs för att inferens

Information om InferenceConfig funktioner finns i den [avancerad konfiguration](#advanced-config) avsnittet.

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

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
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

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
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

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **Använder VS Code**

  Du kan också [distribuera till AKS via VS Code-tillägg](how-to-vscode-tools.md#deploy-and-manage-models), men du måste konfigurera AKS-kluster i förväg.

Läs mer om AKS-distributionen och skala automatiskt i den [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) referens.

#### Skapa ett nytt AKS-kluster<a id="create-attach-aks"></a>
**Uppskattad tidsåtgång:** Cirka 5 minuter.

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

**Uppskattad tidsåtgång**: Ungefär 20 minuter.

#### <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

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

<a id="advanced-config"></a>

## <a name="advanced-settings"></a>Avancerade inställningar 

**<a id="customimage"></a> Använda en anpassad källinstallation**

Internt skapar InferenceConfig en Docker-avbildning som innehåller modellen och andra resurser som krävs av tjänsten. Om den inte anges används en standard-basavbildning.

När du skapar en bild som används med inferens konfigurationen måste avbildningen uppfylla följande krav:

* Ubuntu 16.04 eller större.
* Conda 4.5. # eller större.
* Python 3.5. # eller 3.6. #.

Om du vill använda en anpassad avbildning, ange den `base_image` egenskapen för inferens konfigurationen till adressen för avbildningen. I följande exempel visar hur du använder en avbildning från både en offentliga och privata Azure Container Registry:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

Följande bild URI: er för avbildningar som tillhandahålls av Microsoft och kan användas utan att ange ett värde som användaren namn eller lösenord:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Om du vill använda dessa avbildningar, ange den `base_image` till URI: N i listan ovan. Ange `base_image_registry.address` till `mcr.microsoft.com`.

> [!IMPORTANT]
> Microsoft-avbildningar som använder CUDA eller TensorRT måste bara användas på Microsoft Azure-tjänster.

Mer information om att ladda upp egna avbildningar till ett Azure Container Registry finns i [skicka din första avbildning till ett privat Docker-behållarregister](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Om din modell har tränats på beräkning av Azure Machine Learning, med hjälp av __version 1.0.22 eller större__ av SDK för Azure Machine Learning, en avbildning skapas under utbildning. I följande exempel visar hur du använder den här bilden:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.
Ta bort registrerade modellen genom att använda `model.delete()`.

Mer information finns i referensdokumentationen för [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), och [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Nästa steg
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)

