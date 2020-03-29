---
title: Distribuera ml-modeller till Azure Functions Apps (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att distribuera en modell till en Azure Functions App.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927447"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Distribuera en maskininlärningsmodell till Azure Functions (förhandsversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar en modell från Azure Machine Learning som en funktionsapp i Azure Functions.

> [!IMPORTANT]
> Även om både Azure Machine Learning och Azure Functions är allmänt tillgängliga, är möjligheten att paketera en modell från Machine Learning-tjänsten för funktioner i förhandsversion.

Med Azure Machine Learning kan du skapa Docker-avbildningar från utbildade maskininlärningsmodeller. Azure Machine Learning har nu förhandsversionsfunktionen för att skapa dessa maskininlärningsmodeller i funktionsappar, som kan [distribueras till Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [Skapa en arbetsyta.](how-to-manage-workspace.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* En tränad maskininlärningsmodell som är registrerad på arbetsytan. Om du inte har någon modell använder du [självstudiekursen Bildklassificering: tågmodell](tutorial-train-models-with-aml.md) för att träna och registrera en.

    > [!IMPORTANT]
    > Kodavsnitten i den här artikeln förutsätter att du har angett följande variabler:
    >
    > * `ws`- Din Azure Machine Learning-arbetsyta.
    > * `model`- Den registrerade modellen som ska distribueras.
    > * `inference_config`- Inferenskonfigurationen för modellen.
    >
    > Mer information om hur du anger dessa variabler finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Innan du distribuerar måste du definiera vad som krävs för att köra modellen som en webbtjänst. I följande lista beskrivs de grundläggande objekt som behövs för en distribution:

* Ett __postskript__. Det här skriptet accepterar begäranden, gör poäng för begäran med hjälp av modellen och returnerar resultaten.

    > [!IMPORTANT]
    > Postskriptet är specifikt för din modell. Den måste förstå formatet på inkommande begäran data, formatet på de data som förväntas av din modell, och formatet på de data som returneras till klienter.
    >
    > Om begärandedata är i ett format som inte kan kan användas av din modell kan skriptet omvandla dem till ett acceptabelt format. Det kan också omvandla svaret innan du återvänder till det till klienten.
    >
    > Som standard när du paketerar för funktioner behandlas indata som text. Om du är intresserad av att konsumera råbyten för indata (till exempel för Blob-utlösare) bör du använda [AMLRequest för att acceptera rådata](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Beroenden**, till exempel hjälpskript eller Python/Conda-paket som krävs för att köra inmatningsskriptet eller modellen

Dessa entiteter är inkapslade i en __inferenskonfiguration__. Inferenskonfigurationen refererar till startskriptet och andra beroenden.

> [!IMPORTANT]
> När du skapar en inferenskonfiguration för användning med Azure Functions måste du använda ett [miljöobjekt.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Observera att om du definierar en anpassad miljö måste du lägga till azureml-standardvärden med version >= 1.0.45 som pipberoende. Det här paketet innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst. I följande exempel visas hur du skapar ett miljöobjekt och använder det med en slutledningskonfiguration:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Mer information om miljöer finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om inferenskonfiguration finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> När du distribuerar till Funktioner behöver du inte skapa en __distributionskonfiguration__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installera förhandsgranskningspaketet för SDK för funktioner

Om du vill skapa paket för Azure Functions måste du installera förhandsgranskningspaketet för SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure Functions använder du [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) eller den specifika paketfunktionen för den utlösare som du är intresserad av att använda. Följande kodavsnitt visar hur du skapar ett nytt paket med en blob-utlösare från modellen och inferenskonfigurationen:

> [!NOTE]
> Kodavsnittet förutsätter att `model` den innehåller en registrerad `inference_config` modell och som innehåller konfigurationen för inferensmiljön. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

När `show_output=True`visas utdata från Docker-byggprocessen. När processen är klar har avbildningen skapats i Azure Container Registry för din arbetsyta. När avbildningen har skapats visas platsen i ditt Azure-behållarregister. Platsen som returneras är `<acrinstance>.azurecr.io/package@sha256:<hash>`i formatet .

> [!NOTE]
> Förpackningar för funktioner stöder för närvarande HTTP-utlösare, Blob-utlösare och servicebussutlösare. Mer information om utlösare finns i [Azure Functions-bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Spara platsinformationen, som den används när avbildningen distribueras.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Använd följande kommando för att hämta inloggningsuppgifterna för Azure Container Registry som innehåller avbildningen. Ersätt `<myacr>` med det värde `package.location`som returnerats tidigare från: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Utdata för det här kommandot liknar följande JSON-dokument:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Spara värdet för __användarnamn__ och ett av __lösenorden__.

1. Om du inte redan har en resursgrupp eller apptjänstplan för att distribuera tjänsten visar följande kommandon hur du skapar båda:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    I det här exemplet används`--sku B1`en _linux-grundläggande_ prisnivå ( ).

    > [!IMPORTANT]
    > Avbildningar som skapats av Azure Machine `--is-linux` Learning använder Linux, så du måste använda parametern.

1. Skapa lagringskontot som ska användas för webbjobblagringen och hämta anslutningssträngen. Ersätt `<webjobStorage>` med det namn du vill använda.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Använd följande kommando om du vill skapa funktionsappen. Ersätt `<app-name>` med det namn du vill använda. Ersätt `<acrinstance>` `<imagename>` och med värdena `package.location` från som returnerats tidigare. Ersätt `<webjobStorage>` med namnet på lagringskontot från föregående steg:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Nu har funktionsappen skapats. Men eftersom du inte har angett anslutningssträngen för blob-utlösaren eller autentiseringsuppgifterna till Azure Container Registry som innehåller avbildningen, är funktionsappen inte aktiv. I nästa steg anger du anslutningssträngen och autentiseringsinformationen för behållarregistret. 

1. Skapa lagringskontot som ska användas för blob-utlösarlagringen och hämta anslutningssträngen. Ersätt `<triggerStorage>` med det namn du vill använda.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Spela in den här anslutningssträngen som ska tillhandahållas till funktionsappen. Vi kommer att använda den senare när vi ber om`<triggerConnectionString>`

1. Skapa behållarna för indata och utdata i lagringskontot. Ersätt `<triggerConnectionString>` med anslutningssträngen som returnerats tidigare:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Om du vill associera utlösaranslutningssträngen med funktionsappen använder du följande kommando. Ersätt `<app-name>` med namnet på funktionsappen. Ersätt `<triggerConnectionString>` med anslutningssträngen som returnerats tidigare:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Du måste hämta taggen som är associerad med den skapade behållaren med följande kommando. Ersätt `<username>` med användarnamnet som returnerats tidigare från behållarregistret:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Spara det returnerade värdet, det `imagetag` kommer att användas som i nästa steg.

1. Om du vill ge funktionsappen de autentiseringsuppgifter som krävs för att komma åt behållarregistret använder du följande kommando. Ersätt `<app-name>` med namnet på funktionsappen. Ersätt `<acrinstance>` `<imagetag>` och med värdena från AZ CLI-anropet i föregående steg. Ersätt `<username>` `<password>` och med ACR inloggningsinformation hämtas tidigare:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Det här kommandot returnerar information som liknar följande JSON-dokument:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Nu börjar funktionsappen läsa in bilden.

> [!IMPORTANT]
> Det kan ta flera minuter innan bilden har lästs in. Du kan övervaka förloppet med Hjälp av Azure Portal.

## <a name="test-the-deployment"></a>Testa distributionen

När bilden har lästs in och appen är tillgänglig gör du så här för att utlösa appen:

1. Skapa en textfil som innehåller de data som score.py filen förväntar sig. Följande exempel skulle fungera med en score.py som förväntar sig en matris med 10 tal:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Formatet på data beror på vad din score.py och modell förväntar sig.

2. Använd följande kommando för att överföra filen till indatabehållaren i utlösarlagringsbloben som skapats tidigare. Ersätt `<file>` med namnet på filen som innehåller data. Ersätt `<triggerConnectionString>` med anslutningssträngen som returnerats tidigare. I det `input` här exemplet är namnet på indatabehållaren som skapats tidigare. Om du har använt ett annat namn ersätter du det här värdet:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Utdata för det här kommandot liknar följande JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Om du vill visa utdata som produceras av funktionen använder du följande kommando för att lista de utdatafiler som genereras. Ersätt `<triggerConnectionString>` med anslutningssträngen som returnerats tidigare. I det `output` här exemplet är namnet på utdatabehållaren som skapats tidigare. Om du har använt ett annat namn ersätter du det här värdet::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Utdata för det här `sample_input_out.json`kommandot liknar .

4. Om du vill hämta filen och kontrollera innehållet använder du följande kommando. Ersätt `<file>` med filnamnet som returnerades av föregående kommando. Ersätt `<triggerConnectionString>` med anslutningssträngen som returnerats tidigare: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    När kommandot är klart öppnar du filen. Den innehåller de data som returneras av modellen.

Mer information om hur du använder blob-utlösare finns i artikeln [Skapa en funktion som utlöses av Azure Blob storage.](/azure/azure-functions/functions-create-storage-blob-triggered-function)

## <a name="next-steps"></a>Nästa steg

* Lär dig att konfigurera din functions-app i [dokumentationen Funktioner.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Läs mer om Blob-lagringsutlösare [Azure Blob storage bindings](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Distribuera din modell till Azure App Service](how-to-deploy-app-service.md).
* [Använda en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [API-referens](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
