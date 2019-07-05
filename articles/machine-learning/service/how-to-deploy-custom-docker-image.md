---
title: Hur du distribuerar en modell med en anpassad dockeravbildning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder en anpassad Docker-avbildning när du distribuerar dina modeller för Azure Machine Learning-tjänsten. När du distribuerar en tränad modell, skapas en Docker-avbildning till värd avbildningen, webbserver och andra komponenter som behövs för att köra tjänsten. Azure Machine Learning-tjänsten tillhandahåller en standardavbildning för dig, kan du också använda en egen avbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 29fdb200075a5b5843944a7a890cc2f8ad61f1ee
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543860"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Distribuera en modell med en anpassad dockeravbildning

Lär dig hur du använder en anpassad Docker-avbildning när du distribuerar tränade modeller med Azure Machine Learning-tjänsten.

När du distribuerar en tränad modell till en webbtjänst eller IoT Edge-enhet, skapas en Docker-avbildning. Den här avbildningen innehåller modellen, conda-miljö och tillgångar som krävs för att använda modellen. Den innehåller också en webbserver för att hantera inkommande begäranden när de distribueras som en webbtjänst och komponenter som behövs för att arbeta med Azure IoT Hub.

Azure Machine Learning-tjänsten ger en standard Docker-avbildning så att du inte behöver bekymra dig om hur du skapar en. Du kan också använda en anpassad avbildning som du skapar som en _basavbildningen_. En basavbildning används som startpunkt när en avbildning skapas för en distribution. Det ger det underliggande operativsystemet och komponenter. Distributionsprocessen lägger sedan till ytterligare komponenter, till exempel din modell, conda-miljö och andra tillgångar på avbildningen innan du distribuerar den.

Normalt skapar du en anpassad avbildning när du vill styra komponenten versioner eller spara tid under distributionen. Du kanske exempelvis vill standardisera på en specifik version av Python, Conda eller andra komponent. Du kanske också vill installera programvara som krävs av din modell, där installationen tar lång tid. Installera programvaran när du skapar basavbildningen innebär att du inte behöver installera det för varje distribution.

> [!IMPORTANT]
> När du distribuerar en modell kan du åsidosätta kärnkomponenter som webbservern eller IoT Edge-komponenter. Dessa komponenter upprättar en känd arbetsmiljö som testats och stöds av Microsoft.

> [!WARNING]
> Microsoft kan inte för att felsöka problem som orsakas av en anpassad avbildning. Om du får problem kan du bli ombedd att använda standardavbildningen eller en av avbildningarna som Microsoft tillhandahåller för att se om problemet är specifikt för din avbildning.

Det här dokumentet är uppdelad i två delar:

* Skapa en anpassad avbildning: Innehåller information för administratörer och DevOps för att skapa en anpassad avbildning och konfigurera autentisering för ett Azure Container Registry med Azure CLI och Machine Learning CLI.
* Använd en anpassad avbildning: Innehåller information till Dataforskare och DevOps/MLOps om hur du använder anpassade avbildningar när du distribuerar en tränad modell från Python SDK eller ML CLI.

## <a name="prerequisites"></a>Förutsättningar

* Arbetsgrupp för en Azure Machine Learning-tjänsten. Mer information finns i den [skapa en arbetsyta](setup-create-workspace.md) artikeln.
* Azure Machine Learning SDK. Mer information finns i avsnittet SDK för Python i den [skapa en arbetsyta](setup-create-workspace.md#sdk) artikeln.
* Den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Den [CLI-tillägg för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* En [Azure Container Registry](/azure/container-registry) eller andra Docker-register som är tillgänglig på internet.
* I det här dokumentet förutsätter vi att du är bekant med att skapa och använda en __inferens configuration__ objektet som en del av modelldistribution. Mer information finns i avsnittet ”Förbered att distribuera” i [var du vill distribuera och hur](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Skapa en anpassad avbildning

Informationen i det här avsnittet förutsätter att du använder ett Azure Container Registry för att lagra Docker-avbildningar. Använd följande checklista när du planerar för att skapa anpassade avbildningar för Azure Machine Learning-tjänsten:

* Ska du använda Azure Container Registry skapat för Azure Machine Learning-tjänsten arbetsyta eller en fristående Azure Container Registry?

    När med hjälp av avbildningar lagras i den __behållarregister för arbetsytan__, du behöver inte autentisera till registret. Autentisering utförs av arbetsytan.

    > [!WARNING]
    > Azure Container Rzegistry för arbetsytan är __skapas första gången du träna eller distribuera en modell__ använda arbetsytan. Om du inte har skapat en ny arbetsyta, men tränats eller skapat en modell, finns inga Azure Container Registry för arbetsytan.

    Information om hur du hämtar namnet på Azure Container Registry för din arbetsyta finns i den [Get behållarregisternamnet](#getname) i den här artikeln.

    När med hjälp av avbildningar lagras i en __fristående behållarregister__, behöver du konfigurera ett huvudnamn för tjänsten som har minst skrivskyddad åtkomst. Du sedan ange ID för tjänstens huvudnamn (användarnamn) och lösenord för alla som använder avbildningar från registret. Undantaget är om du gör behållarregistret offentligt tillgängliga.

    Information om hur du skapar en privat Azure Container Registry finns i [skapa ett privat behållarregister](/azure/container-registry/container-registry-get-started-azure-cli).

    Information om hur du använder tjänstens huvudnamn med Azure Container Registry finns i [Azure Container Registry-autentisering med tjänstens huvudnamn](/azure/container-registry/container-registry-auth-service-principal).

* Information om Azure Container Registry och avbildning: Ange avbildningens namn för alla som behöver använda den. Till exempel en avbildning med namnet `myimage`, lagras i ett register med namnet `myregistry`, refereras som `myregistry.azurecr.io/myimage` när du använder avbildningen för distribution av modeller

* Avbildningskrav: Azure Machine Learning-tjänsten har endast stöd för Docker-avbildningar som tillhandahåller följande programvara:

    * Ubuntu 16.04 eller större.
    * Conda 4.5. # eller större.
    * Python 3.5. # eller 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Hämta information om container registry

I det här avsnittet lär du dig hur du hämtar namnet på Azure Container Registry för tjänsten Azure Machine Learning-arbetsytan.

> [!WARNING]
> Azure Container Registry för arbetsytan är __skapas första gången du träna eller distribuera en modell__ använda arbetsytan. Om du inte har skapat en ny arbetsyta, men tränats eller skapat en modell, finns inga Azure Container Registry för arbetsytan.

Om du har redan tränats eller distribuerat modeller med hjälp av Azure Machine Learning-tjänsten, har ett behållarregister skapats för din arbetsyta. Använd följande steg för att hitta namnet på det här behållarregistret:

1. Öppna ett nytt gränssnitt eller Kommandotolken och Använd följande kommando för att autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Följ anvisningarna för att autentisera till prenumerationen.

2. Använd följande kommando för att lista behållarregistret för arbetsytan. Ersätt `<myworkspace>` med Azure Machine Learning-arbetsyta tjänstnamnet. Ersätt `<resourcegroup>` med Azure-resursgrupp som innehåller din arbetsyta:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    Den information som returneras liknar följande text:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Den `<registry_name>` värdet är namnet på Azure Container Registry för din arbetsyta.

### <a name="build-a-custom-image"></a>Skapa en anpassad avbildning

Stegen i den här genomgången för avsnittet Skapa en anpassad dockeravbildning i Azure Container Registry.

1. Skapa en ny textfil med namnet `Dockerfile`, och Använd följande text som innehållet:

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Från en shell eller Kommandotolken kan du använda följande för att autentisera till Azure Container Registry. Ersätt den `<registry_name>` med namnet på behållarregistret du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Om du vill ladda upp Dockerfile och bygg den, använder du följande kommando. Ersätt `<registry_name>` med namnet på behållarregistret du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    När du skapar strömmas information tillbaka till kommandoraden. Om versionen har slutförts visas ett meddelande som liknar följande text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Mer information om hur du bygger avbildningar med ett Azure Container Registry finns i [skapa och köra en behållaravbildning med hjälp av Azure Container Registry uppgifter](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Mer information om att ladda upp befintlig avbildning till ett Azure Container Registry finns i [skicka din första avbildning till ett privat Docker-behållarregister](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Använda en anpassad avbildning

Om du vill använda en anpassad avbildning, behöver du följande information:

* Den __avbildningsnamn__. Till exempel `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` är sökvägen till en grundläggande Docker-avbildning som tillhandahålls av Microsoft.
* Om avbildningen i en __privata lagringsplatsen__, behöver du följande information:

    * Registret __adress__. Till exempel `myregistry.azureecr.io`.
    * Ett huvudnamn för tjänsten __användarnamn__ och __lösenord__ som har läsbehörighet till registret.

    Om du inte har den här informationen kan du tala med administratören för Azure Container Registry som innehåller din avbildning.

### <a name="publicly-available-images"></a>Offentligt tillgängliga avbildningar

Microsoft tillhandahåller flera docker-avbildningar på en offentligt tillgänglig lagringsplats, som kan användas med stegen i det här avsnittet:

| Image | Beskrivning |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Basic-avbildningen för Azure Machine Learning-tjänsten |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Innehåller ONNX-runtime. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Innehåller ONNX-runtime och CUDA-komponenter. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Innehåller ONNX-runtime och TensorRT. |

> [!TIP]
> Eftersom dessa avbildningar är allmänt tillgängliga, behöver du inte ange en adress, användarnamn eller lösenord när du använder dem.

> [!IMPORTANT]
> Microsoft-avbildningar som använder CUDA eller TensorRT måste bara användas på Microsoft Azure-tjänster.

> [!TIP]
>__Om din modell har tränats på beräkning av Azure Machine Learning__med hjälp av __version 1.0.22 eller större__ av SDK för Azure Machine Learning, en avbildning skapas under utbildning. För att identifiera namnet på den här bilden, använda `run.properties["AzureML.DerivedImageName"]`. I följande exempel visar hur du använder den här bilden:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Använda en avbildning med Azure Machine Learning-SDK

Om du vill använda en anpassad avbildning, ange den `base_image` egenskapen för den [inferens konfigurationsobjektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -adressen för avbildningen:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Det här formatet fungerar för båda bilderna som lagras i Azure Container Registry för din arbetsyta och container register som är tillgängliga för allmänheten. Följande kod använder exempelvis en standardavbildning som tillhandahålls av Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Att använda en avbildning från en __privat behållarregister__ som inte är i din arbetsyta, måste du ange adressen till databasen och ett användarnamn och lösenord:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Använda en avbildning med Machine Learning CLI

> [!IMPORTANT]
> Machine Learning CLI kan för närvarande använder avbildningar från Azure Container Registry för din arbetsyta eller en offentligt tillgänglig databaser. Det kan inte använda avbildningar från fristående privata register.

När du distribuerar en modell med Machine Learning CLI kan ange du en konfigurationsfil för inferens som refererar till den anpassade avbildningen. Följande JSON-dokumentet visar hur du refererar till en bild i en offentlig container registry:

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

Den här filen används med den `az ml model deploy` kommando. Den `--ic` används för att ange inferens konfigurationsfil.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Mer information om hur du distribuerar en modell med hjälp av ML CLI finns i avsnittet ”modellen profilering och distribution” för den [CLI-tillägg för Azure Machine Learning-tjänsten](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artikeln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [var du vill distribuera och hur](how-to-deploy-and-where.md).
* Lär dig hur du [träna och distribuera machine learning-modeller med hjälp av Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
