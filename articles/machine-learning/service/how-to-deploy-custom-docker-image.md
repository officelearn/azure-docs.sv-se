---
title: Distribuera modeller med hjälp av en anpassad Docker-avbildning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder en anpassad Docker-avbildning när du distribuerar Azure Machine Learning tjänst modeller. När du distribuerar en utbildad modell skapas en Docker-avbildning som värd för avbildningen, webb servern och andra komponenter som behövs för att köra tjänsten. Medan Azure Machine Learning tjänsten tillhandahåller en standard avbildning åt dig kan du också använda en egen avbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: f41ccef7803366e63247e6862c59ddb983527d26
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990533"
---
# <a name="deploy-a-model-by-using-a-custom-docker-image"></a>Distribuera en modell med hjälp av en anpassad Docker-avbildning

Lär dig hur du använder en anpassad Docker-avbildning när du distribuerar utbildade modeller med Azure Machine Learning-tjänsten.

När du distribuerar en utbildad modell till en webb tjänst eller IoT Edge enhet skapas en Docker-avbildning. Den här avbildningen innehåller modellen, Conda-miljön och till gångar som behövs för att använda modellen. Den innehåller också en webb server som hanterar inkommande begär anden när den distribueras som en webb tjänst och komponenter som behövs för att arbeta med Azure IoT Hub.

Azure Machine Learning tjänsten innehåller en standard Docker-avbildning så att du inte behöver oroa dig för att skapa en. Du kan också använda en anpassad avbildning som du skapar som en _bas avbildning_. En bas avbildning används som start punkt när en avbildning skapas för en distribution. Det tillhandahåller underliggande operativ system och komponenter. Distributions processen lägger sedan till ytterligare komponenter, till exempel din modell, Conda-miljö och andra till gångar, till avbildningen innan du distribuerar den.

Normalt skapar du en anpassad avbildning när du vill kontrol lera komponent versioner eller spara tid under distributionen. Du kanske till exempel vill standardisera en speciell version av python, Conda eller andra komponenter. Du kanske också vill installera program vara som krävs av din modell, där installations processen tar lång tid. Installation av program varan när du skapar bas avbildningen innebär att du inte behöver installera den för varje distribution.

> [!IMPORTANT]
> När du distribuerar en modell kan du inte åsidosätta kärn komponenter som webb server eller IoT Edge-komponenter. Dessa komponenter tillhandahåller en känd arbets miljö som testas och stöds av Microsoft.

> [!WARNING]
> Microsoft kanske inte kan hjälpa till att felsöka problem som orsakas av en anpassad avbildning. Om du stöter på problem kan du uppmanas att använda standard avbildningen eller någon av de avbildningar som Microsoft tillhandahåller för att se om problemet är speciellt för din avbildning.

Det här dokumentet är uppdelat i två delar:

* Skapa en anpassad avbildning: Innehåller information till administratörer och DevOps om hur du skapar en anpassad avbildning och konfigurerar autentisering till en Azure Container Registry med hjälp av Azure CLI och Machine Learning CLI.
* Använd en anpassad avbildning: Innehåller information till data vetenskaps-och DevOps/MLOps med anpassade avbildningar när du distribuerar en utbildad modell från python SDK eller ML CLI.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning tjänst arbets grupp. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* Den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* En [Azure Container Registry](/azure/container-registry) eller ett annat Docker-register som är tillgängligt på Internet.
* Stegen i det här dokumentet förutsätter att du är van vid att skapa och använda ett konfigurations objekt för konfigurations objekt som en del av modell distributionen. Mer information finns i avsnittet "förbereda distribution" i [var du ska distribuera och hur](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Skapa en anpassad avbildning

Informationen i det här avsnittet förutsätter att du använder en Azure Container Registry för att lagra Docker-avbildningar. Använd följande check lista när du planerar att skapa anpassade avbildningar för Azure Machine Learning-tjänsten:

* Kommer du att använda Azure Container Registry som skapats för arbets ytan Azure Machine Learnings tjänst eller en fristående Azure Container Registry?

    När du använder avbildningar som lagras i behållar __registret för arbets ytan__behöver du inte autentisera till registret. Autentiseringen hanteras av arbets ytan.

    > [!WARNING]
    > Azure Container Rzegistry för din arbets yta __skapas första gången du tränar eller distribuerar en modell__ med hjälp av arbets ytan. Om du har skapat en ny arbets yta, men inte tränat eller skapat någon modell, så finns det inga Azure Container Registry för arbets ytan.

    Information om hur du hämtar namnet på Azure Container Registry för din arbets yta finns i avsnittet [Hämta container Registry-namn](#getname) i den här artikeln.

    När du använder avbildningar som lagras i ett __fristående behållar register__måste du konfigurera ett huvud namn för tjänsten som har minst Läs behörighet. Du anger sedan tjänstens huvud namns-ID (username) och lösen ord för alla som använder avbildningar från registret. Undantaget är om du gör att behållar registret är offentligt tillgängligt.

    Information om hur du skapar en privat Azure Container Registry finns i [skapa ett privat container Registry](/azure/container-registry/container-registry-get-started-azure-cli).

    Information om hur du använder tjänstens huvud namn med Azure Container Registry finns [Azure Container Registry autentisering med tjänstens huvud namn](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry-och avbildnings information: Ange avbildnings namnet för alla som behöver använda det. En bild som heter `myimage`, lagrad i ett register med namnet `myregistry`, refereras till som `myregistry.azurecr.io/myimage` när du använder avbildningen för modell distribution

* Avbildnings krav: Azure Machine Learnings tjänsten har endast stöd för Docker-avbildningar som tillhandahåller följande program vara:

    * Ubuntu 16,04 eller senare.
    * Conda 4.5. # eller senare.
    * Python 3.5. # eller 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Hämta information om container registret

I det här avsnittet får du lära dig hur du hämtar namnet på Azure Container Registry för din Azure Machine Learning service-arbetsyta.

> [!WARNING]
> Azure Container Registry för din arbets yta __skapas första gången du tränar eller distribuerar en modell__ med hjälp av arbets ytan. Om du har skapat en ny arbets yta, men inte tränat eller skapat någon modell, så finns det inga Azure Container Registry för arbets ytan.

Om du redan har tränat eller distribuerat modeller med hjälp av tjänsten Azure Machine Learning, har ett behållar register skapats för din arbets yta. Använd följande steg för att hitta namnet på det här behållar registret:

1. Öppna ett nytt gränssnitt eller kommando tolk och Använd följande kommando för att autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Följ anvisningarna för att autentisera till prenumerationen.

2. Använd följande kommando för att Visa behållar registret för arbets ytan. Ersätt `<myworkspace>` med namnet på din Azure Machine Learning service-arbetsyta. Ersätt `<resourcegroup>` med Azure-resurs gruppen som innehåller din arbets yta:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

    Den information som returneras liknar följande text:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` Värdet är namnet på Azure Container Registry för din arbets yta.

### <a name="build-a-custom-image"></a>Bygg en anpassad avbildning

Stegen i det här avsnittet beskriver hur du skapar en anpassad Docker-avbildning i din Azure Container Registry.

1. Skapa en ny textfil med namnet `Dockerfile`och Använd följande text som innehåll:

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

2. Använd följande för att autentisera till Azure Container Registry från ett gränssnitt eller kommando tolken. `<registry_name>` Ersätt med namnet på det behållar register som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Om du vill ladda upp Dockerfile och bygga den använder du följande kommando. Ersätt `<registry_name>` med namnet på det behållar register som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Under skapande processen strömmas information tillbaka till kommando raden. Om versionen lyckas visas ett meddelande som liknar följande text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Mer information om hur du skapar avbildningar med en Azure Container Registry finns i [skapa och köra en behållar avbildning med hjälp av Azure Container Registry uppgifter](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Mer information om hur du överför befintliga avbildningar till en Azure Container Registry finns i [skicka din första avbildning till ett privat Docker-behållarobjekt](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Använda en anpassad avbildning

Om du vill använda en anpassad avbildning behöver du följande information:

* __Avbildningens namn__. Till exempel är `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` sökvägen till en grundläggande Docker-avbildning från Microsoft.
* Om avbildningen finns i ett __privat lager__behöver du följande information:

    * Register __adressen__. Till exempel `myregistry.azureecr.io`.
    * Ett __användar namn__ och __lösen ord__ för tjänstens huvud namn som har Läs behörighet till registret.

    Om du inte har den här informationen kan du prata med administratören för den Azure Container Registry som innehåller din avbildning.

### <a name="publicly-available-images"></a>Offentligt tillgängliga bilder

Microsoft tillhandahåller flera Docker-avbildningar på en offentligt tillgänglig lagrings plats som kan användas med stegen i det här avsnittet:

| Image | Beskrivning |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Grundläggande avbildning för Azure Machine Learning tjänst |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Innehåller ONNX-körningsmiljön. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Innehåller ONNX Runtime-och CUDA-komponenterna. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Innehåller ONNX Runtime och TensorRT. |

> [!TIP]
> Eftersom dessa bilder är offentligt tillgängliga behöver du inte ange någon adress, användar namn eller lösen ord när du använder dem.

> [!IMPORTANT]
> Microsoft-avbildningar som använder CUDA eller TensorRT får endast användas på Microsoft Azure-tjänster.

> [!TIP]
>__Om din modell tränas på Azure Machine Learning Compute__, med __version 1.0.22 eller senare__ av Azure Machine Learning SDK, skapas en avbildning under utbildningen. Använd `run.properties["AzureML.DerivedImageName"]`om du vill identifiera namnet på den här avbildningen. Följande exempel visar hur du använder den här avbildningen:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Använd en avbildning med Azure Machine Learning SDK

Om du vill använda en anpassad avbildning anger `base_image` du egenskapen för [konfigurations objektets konfigurations objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) till adressen för avbildningen:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Det här formatet fungerar för både avbildningar som lagras i Azure Container Registry för din arbets yta och dina behållar register som är tillgängliga för allmänheten. I följande kod används till exempel en standard avbildning från Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Om du vill använda en avbildning från ett __privat behållar register__ som inte finns i din arbets yta, måste du ange adressen till lagrings platsen och ett användar namn och lösen ord:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Använd en avbildning med Machine Learning CLI

> [!IMPORTANT]
> För närvarande kan Machine Learning CLI använda bilder från Azure Container Registry för din arbets yta eller offentligt tillgängliga databaser. Det går inte att använda bilder från fristående privata register.

När du distribuerar en modell med hjälp av Machine Learning CLI, anger du en konfigurations fil för konfiguration som refererar till den anpassade avbildningen. Följande JSON-dokument visar hur du refererar till en avbildning i ett offentligt behållar register:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Den här filen används med `az ml model deploy` kommandot. `--ic` Parametern används för att ange konfigurations filen för konfigurations filen.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Mer information om hur du distribuerar en modell med hjälp av ML CLI finns i avsnittet "modell registrering, profilering och distribution" i CLI- [tillägget för Azure Machine Learning service](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artikeln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [var du distribuerar och hur](how-to-deploy-and-where.md).
* Lär dig hur du [tränar och distribuerar maskin inlärnings modeller med Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)-pipeliner.
