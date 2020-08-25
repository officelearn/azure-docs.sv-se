---
title: Hur och var modeller ska distribueras
titleSuffix: Azure Machine Learning
description: Lär dig hur och var du kan distribuera Azure Machine Learning-modeller, inklusive Azure Container Instances, Azure Kubernetes service, Azure IoT Edge och fält-programmerbara grind mat ris.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d7502414f6476cafcc85bbefd28a4ec463f62099
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751696"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuera modeller med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar din Machine Learning-modell som en webb tjänst i Azure-molnet eller Azure IoT Edge enheter.

Arbetsflödet är ungefär likadant var du än distribuerar din modell:

1. Registrera modellen.
1. Förbered en konfiguration för en härledning
1. Förbereda ett Entry-skript (om du inte använder [distribution utan kod](how-to-deploy-no-code-deployment.md))
1. Distribuera modellen till beräkningsmålet.
1. Testa den distribuerade modellen, även kallad en webb tjänst.

Mer information om de begrepp som ingår i distributions arbets flödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).
- [Tillägget för Azure Command Line Interface (CLI) för tjänsten Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).
- [Azure Machine Learning Software Development Kit (SDK) för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Följ anvisningarna i Azure CLI-dokumentationen för att [ställa in din prenumerations kontext](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Gör sedan följande:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

för att se de arbets ytor som du har åtkomst till.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Mer information om hur du använder SDK för att ansluta till en arbets yta finns i dokumentationen [för Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .


---


## <a name="register-your-model"></a><a id="registermodel"></a> Registrera din modell

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en moln plats (från en utbildnings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. Den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [hitta modell filer i ditt Entry-skript](how-to-deploy-advanced-entry-script.md#load-registered-models).

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller från någon annan stans. När du registrerar en modell kan du välja att ange metadata om modellen. De `tags` `properties` ord listor som du använder för en modell registrering kan sedan användas för att filtrera modeller.

Följande exempel visar hur du registrerar en modell.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-utbildning

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du sökvägen till en mapp som innehåller filerna.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Om du vill inkludera flera filer i modell registreringen anger `-p` du sökvägen till en mapp som innehåller filerna.

Mer information finns i `az ml model register` [referens dokumentationen](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en moln plats (från en utbildnings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. Den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [hitta modell filer i ditt Entry-skript](how-to-deploy-advanced-entry-script.md#load-registered-models).

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller från någon annan stans. När du registrerar en modell kan du välja att ange metadata om modellen. De `tags` `properties` ord listor som du använder för en modell registrering kan sedan användas för att filtrera modeller.

Följande exempel visar hur du registrerar en modell.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-utbildning

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

Mer information finns i dokumentationen för [modell klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Mer information om hur du arbetar med modeller som har tränats utanför Azure Machine Learning finns i [distribuera en befintlig modell](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definiera ett post skript

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definiera en konfiguration för en härledning

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet anger konfigurationen följande inställningar:

* Som modellen kräver python
* [Entry-skriptet](#define-an-entry-script), som används för att hantera webb förfrågningar som skickas till den distribuerade tjänsten
* Den Conda-fil som beskriver de python-paket som behövs för att kunna utvägar

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

# <a name="python"></a>[Python](#tab/python)

En konfiguration för en härledning beskriver hur du konfigurerar webb tjänsten som innehåller din modell. Den används senare när du distribuerar modellen.

Konfiguration av konfigurations miljön använder Azure Machine Learning miljöer för att definiera de program beroenden som behövs för distributionen. Med miljöer kan du skapa, hantera och återanvända program beroenden som krävs för utbildning och distribution. Du kan skapa en miljö från anpassade beroende filer eller använda någon av de granskade Azure Machine Learning miljöerna. Följande YAML är ett exempel på en fil för Conda-beroenden. Observera att du måste ange azureml-defaults med version >= 1.0.45 som ett pip-beroende, eftersom det innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst. Om du vill använda automatisk schema generering måste ditt Entry-skript också importera `inference-schema` paketen.

```YAML

name: project_environment
dependencies:
- python=3.6.2
- scikit-learn=0.22.1
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

En grundlig diskussion om hur du använder och anpassar python-miljöer med Azure Machine Learning finns [i skapa & använda program varu miljöer i Azure Machine Learning](how-to-use-environments.md).

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).


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

---

## <a name="choose-a-compute-target"></a>Välj ett beräknings mål

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definiera en distributions konfiguration

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Vilka alternativ som är tillgängliga för en distributions konfiguration beror på vilket beräknings mål du väljer.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i dokumentationen för [AZ ml-modellen Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="python"></a>[Python](#tab/python)

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

---



## <a name="deploy-your-model"></a>Distribuera din modell

Nu är du redo att distribuera din modell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Använda en registrerad modell

Om du har registrerat din modell i Azure Machine Learning arbets ytan ersätter du "modell: 1" med namnet på din modell och dess versions nummer.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Använda en lokal modell

Om du föredrar att inte registrera din modell kan du skicka parametern "sourceDirectory" i din inferenceconfig.jspå för att ange en lokal katalog som din modell ska användas från.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

Exemplet nedan visar en lokal distribution. Syntaxen varierar beroende på vilket beräknings mål som du valde i föregående steg.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information finns i dokumentationen för [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)och [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

---

### <a name="understanding-service-state"></a>Förstå tjänst tillstånd

Under modell distributionen kan tjänst tillstånds ändringen visas när den distribueras fullständigt.

I följande tabell beskrivs de olika tjänst tillstånden:

| Webservice-tillstånd | Beskrivning | Slutligt tillstånd?
| ----- | ----- | ----- |
| Övergår | Tjänsten håller på att distribueras. | Inga |
| Ohälsosamt | Tjänsten har distribuerats men är för närvarande inte tillgänglig.  | Inga |
| Unschedulable | Det går inte att distribuera tjänsten för tillfället på grund av bristande resurser. | Inga |
| Misslyckad | Det gick inte att distribuera tjänsten på grund av ett fel eller en krasch. | Ja |
| Felfri | Tjänsten är felfri och slut punkten är tillgänglig. | Ja |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batch-härledning
Azure Machine Learning beräknings mål skapas och hanteras av Azure Machine Learning. De kan användas för batch förutsägelse från Azure Machine Learning pipeliner.

En genom gång av batch-härledning med Azure Machine Learning Compute finns i [så här kör du batch-förutsägelser](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge-härledning
Stöd för att distribuera till Edge är i för hands version. Mer information finns i [distribuera Azure Machine Learning som en IoT Edge modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="delete-resources"></a>Ta bort resurser

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Använd om du vill ta bort en distribuerad WebService `az ml service <name of webservice>` .

Om du vill ta bort en registrerad modell från din arbets yta använder du `az ml model delete <model id>`

Läs mer om att [ta bort en WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) och [ta bort en modell](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Använd om du vill ta bort en distribuerad webb tjänst `service.delete()` .
Använd om du vill ta bort en registrerad modell `model.delete()` .

Mer information finns i dokumentationen för [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) och [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

---


## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)

