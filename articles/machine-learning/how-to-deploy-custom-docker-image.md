---
title: Distribuera modeller med anpassad Docker-avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en anpassad Docker-basavbildning när du distribuerar dina Azure Machine Learning-modeller. Azure Machine Learning är en standardbasavbildning åt dig, men du kan också använda din egen basavbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: 1f11d6667c22990b3cba2079959bec6f413d5951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296942"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Distribuera en modell med en anpassad Docker-basavbildning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder en anpassad Docker-basavbildning när du distribuerar tränade modeller med Azure Machine Learning.

När du distribuerar en tränad modell till en webbtjänst eller IoT Edge-enhet skapas ett paket som innehåller en webbserver för att hantera inkommande begäranden.

Azure Machine Learning tillhandahåller en standardbasavbildning för Docker så att du inte behöver oroa dig för att skapa en. Du kan också använda Azure Machine __Learning-miljöer__ för att välja en specifik basavbildning eller använda en anpassad som du anger.

En basavbildning används som utgångspunkt när en avbildning skapas för en distribution. Det ger det underliggande operativsystemet och komponenterna. Distributionsprocessen lägger sedan till ytterligare komponenter, till exempel din modell, conda-miljö och andra resurser, i avbildningen innan du distribuerar den.

Vanligtvis skapar du en anpassad basavbildning när du vill använda Docker för att hantera dina beroenden, behålla hårdare kontroll över komponentversioner eller spara tid under distributionen. Du kanske till exempel vill standardisera på en viss version av Python, Conda eller annan komponent. Du kanske också vill installera programvara som krävs av din modell, där installationsprocessen tar lång tid. Installera programvaran när du skapar basavbildningen innebär att du inte behöver installera den för varje distribution.

> [!IMPORTANT]
> När du distribuerar en modell kan du inte åsidosätta kärnkomponenter som webbservern eller IoT Edge-komponenterna. Dessa komponenter ger en känd arbetsmiljö som testas och stöds av Microsoft.

> [!WARNING]
> Microsoft kanske inte kan felsöka problem som orsakas av en anpassad avbildning. Om du stöter på problem kan du bli ombedd att använda standardavbildningen eller någon av bilderna som Microsoft tillhandahåller för att se om problemet är specifikt för din avbildning.

Det här dokumentet är indelat i två avsnitt:

* Skapa en anpassad basavbildning: Ger information till administratörer och DevOps om hur du skapar en anpassad avbildning och konfigurerar autentisering till ett Azure-behållarregister med hjälp av Azure CLI och Machine Learning CLI.
* Distribuera en modell med hjälp av en anpassad basavbildning: Ger information till dataforskare och DevOps / ML-tekniker om hur du använder anpassade avbildningar när du distribuerar en tränad modell från Python SDK eller ML CLI.

## <a name="prerequisites"></a>Krav

* En arbetsgrupp för Azure Machine Learning. Mer information finns i artikeln [Skapa en arbetsyta.](how-to-manage-workspace.md)
* [Sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)för Azure Machine Learning . 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Ett [Azure-behållarregister](/azure/container-registry) eller annat Docker-register som är tillgängligt på internet.
* Stegen i det här dokumentet förutsätter att du är bekant med att skapa och använda ett __inferenskonfigurationsobjekt__ som en del av modelldistributionen. Mer information finns i avsnittet "förbered distribution" i [Var du ska distribuera och hur](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Skapa en anpassad basavbildning

Informationen i det här avsnittet förutsätter att du använder ett Azure Container-registret för att lagra Docker-avbildningar. Använd följande checklista när du planerar att skapa anpassade avbildningar för Azure Machine Learning:

* Kommer du att använda Azure Container Registry som skapats för Azure Machine Learning arbetsyta, eller en fristående Azure Container Registry?

    När du använder avbildningar som lagras i __behållarregistret för arbetsytan__behöver du inte autentisera till registret. Autentisering hanteras av arbetsytan.

    > [!WARNING]
    > Azure Container-registret för din arbetsyta __skapas första gången du tränar eller distribuerar en modell__ med arbetsytan. Om du har skapat en ny arbetsyta, men inte tränats eller skapat en modell, finns det inget Azure Container-register för arbetsytan.

    Information om hur du hämtar namnet på Azure Container Registry för arbetsytan finns i avsnittet [Hämta behållarregisternamn](#getname) i den här artikeln.

    När du använder avbildningar som lagras i ett __fristående behållarregister__måste du konfigurera ett huvudnamn för tjänsten som har åtminstone läsbehörighet. Du anger sedan tjänstens huvud-ID (användarnamn) och lösenord till alla som använder bilder från registret. Undantaget är om du gör behållarregistret tillgängligt för allmänheten.

    Information om hur du skapar ett privat Azure-behållarregister finns i [Skapa ett privat behållarregister](/azure/container-registry/container-registry-get-started-azure-cli).

    Information om hur du använder tjänsthuvudnamn med Azure Container Registry finns i [Azure Container Registry authentication with service principals](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry and image information: Ange avbildningsnamnet till alla som behöver använda den. En avbildning med `myimage`namnet , lagrad `myregistry`i ett `myregistry.azurecr.io/myimage` register med namnet, refereras till som när avbildningen för modelldistribution

* Avbildningskrav: Azure Machine Learning stöder endast Docker-avbildningar som tillhandahåller följande programvara:

    * Ubuntu 16,04 eller mer.
    * Conda 4.5.# eller mer.
    * Python 3.5.# eller 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Hämta information om behållarregister

I det här avsnittet kan du läsa om hur du hämtar namnet på Azure Container Registry för din Azure Machine Learning-arbetsyta.

> [!WARNING]
> Azure Container-registret för din arbetsyta __skapas första gången du tränar eller distribuerar en modell__ med arbetsytan. Om du har skapat en ny arbetsyta, men inte tränats eller skapat en modell, finns det inget Azure Container-register för arbetsytan.

Om du redan har tränat eller distribuerat modeller med Azure Machine Learning skapades ett behållarregister för din arbetsyta. Så här hittar du namnet på det här behållarregistret:

1. Öppna ett nytt skal eller en ny kommandotolk och använd följande kommando för att autentisera din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Följ anvisningarna för att autentisera prenumerationen.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

2. Använd följande kommando för att lista behållarregistret för arbetsytan. Ersätt `<myworkspace>` med ditt Azure Machine Learning-arbetsytenamn. Ersätt `<resourcegroup>` med den Azure-resursgrupp som innehåller arbetsytan:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Den information som returneras liknar följande text:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Värdet `<registry_name>` är namnet på Azure Container Registry för din arbetsyta.

### <a name="build-a-custom-base-image"></a>Skapa en anpassad basavbildning

Stegen i det här avsnittet går igenom att skapa en anpassad Docker-avbildning i ditt Azure-behållarregister.

1. Skapa en ny `Dockerfile`textfil med namnet och använd följande text som innehåll:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Från ett skal eller kommandotolken använder du följande för att autentisera till Azure Container Registry. Ersätt `<registry_name>` med namnet på det behållarregister som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Om du vill ladda upp Dockerfile och skapa den använder du följande kommando. Ersätt `<registry_name>` med namnet på det behållarregister som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > I det här exemplet `:v1` används en tagg av bilden. Om ingen tagg anges används `:latest` en tagg med en tagg.

    Under byggprocessen strömmas information tillbaka till kommandoraden. Om bygget lyckas visas ett meddelande som liknar följande text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Mer information om hur du skapar avbildningar med ett Azure-behållarregister finns i [Skapa och köra en behållaravbildning med Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Mer information om hur du överför befintliga avbildningar till ett Azure-behållarregister finns i [Skicka din första avbildning till ett privat Docker-behållarregister](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-base-image"></a>Använda en anpassad basbild

Om du vill använda en anpassad avbildning behöver du följande information:

* __Bildnamnet__. Till exempel `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` är sökvägen till en grundläggande Docker Image som tillhandahålls av Microsoft.

    > [!IMPORTANT]
    > För anpassade bilder som du har skapat måste du inkludera alla taggar som har använts med bilden. Om bilden till exempel har skapats med `:v1`en viss tagg, till exempel . Om du inte använde en viss tagg när `:latest` du skapade bilden användes en tagg.

* Om bilden finns i en __privat databas__behöver du följande information:

    * __Registeradressen__. Till exempel `myregistry.azureecr.io`.
    * Ett __användarnamn__ och __lösenord__ för tjänsten som har läsbehörighet till registret.

    Om du inte har den här informationen kontaktar du administratören för Azure Container Registry som innehåller avbildningen.

### <a name="publicly-available-base-images"></a>Offentligt tillgängliga basbilder

Microsoft tillhandahåller flera dockeravbildningar i en databas som är tillgänglig för allmänheten, som kan användas med stegen i det här avsnittet:

| Bild | Beskrivning |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Grundläggande avbildning för Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Innehåller ONNX Runtime för CPU-inferencing |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Innehåller ONNX Runtime och CUDA för GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Innehåller ONNX Runtime och TensorRT för GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Innehåller ONNX Runtime och OpenVINO för Intel<sup> </sup> Vision Accelerator Design baserad på Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Innehåller ONNX Runtime och OpenVINO för Intel<sup> </sup> Movidius<sup>TM</sup> USB-minnen |

Mer information om ONNX Runtime-basbilderna finns i [avsnittet ONNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) i GitHub-repo.

> [!TIP]
> Eftersom dessa bilder är allmänt tillgängliga behöver du inte ange någon adress, ett användarnamn eller lösenord när du använder dem.

Mer information finns i [Azure Machine Learning-behållare](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Om din modell tränas på Azure Machine Learning Compute__med version __1.0.22 eller mer__ av Azure Machine Learning SDK skapas en avbildning under träningen. Om du vill ta reda `run.properties["AzureML.DerivedImageName"]`på namnet på den här bilden använder du . I följande exempel visas hur du använder den här bilden:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Använda en avbildning med Azure Machine Learning SDK

Om du vill använda en avbildning som lagras i **Azure Container Registry för din arbetsyta**eller ett **behållarregister som är allmänt tillgängligt**anger du följande [miljöattribut:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Ställ in på registret och sökvägen till avbildningen.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Om du vill använda en avbildning från ett __privat behållarregister__ som inte finns på arbetsytan måste du använda `docker.base_image_registry` för att ange databasens adress och ett användarnamn och lösenord:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Du måste lägga till azureml-standardvärden med version >= 1.0.45 som pipberoende. Det här paketet innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst. Du måste också ställa in inferencing_stack_version egenskap på miljön till "senaste", kommer detta att installera specifika apt-paket som behövs av webbtjänsten. 

När du har definierat miljön använder du den med ett [InferenceConfig-objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) för att definiera den inferensmiljö där modellen och webbtjänsten ska köras.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Nu kan du fortsätta med distributionen. Följande kodavsnitt skulle till exempel distribuera en webbtjänst lokalt med hjälp av inferenskonfigurationen och den anpassade avbildningen:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information om distribution finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Mer information om hur du anpassar pythonmiljön finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Använda en bild med Machine Learning CLI

> [!IMPORTANT]
> För närvarande kan Machine Learning CLI använda avbildningar från Azure Container Registry för din arbetsyta eller offentligt tillgängliga databaser. Den kan inte använda bilder från fristående privata register.

Innan du distribuerar en modell med Machine Learning CLI skapar du en [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) som använder den anpassade avbildningen. Skapa sedan en inferenskonfigurationsfil som refererar till miljön. Du kan också definiera miljön direkt i inferenskonfigurationsfilen. Följande JSON-dokument visar hur du refererar till en avbildning i ett offentligt behållarregister. I det här exemplet definieras miljön infogad:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Den här filen `az ml model deploy` används med kommandot. Parametern `--ic` används för att ange inferenskonfigurationsfilen.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Mer information om hur du distribuerar en modell med ml CLI finns i avsnittet "modellregistrering, profilering och distribution" i [CLI-tillägget för Azure Machine](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) Learning-artikeln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Var du kan distribuera och hur](how-to-deploy-and-where.md).
* Lär dig hur du [tränar och distribuerar maskininlärningsmodeller med Hjälp av Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
