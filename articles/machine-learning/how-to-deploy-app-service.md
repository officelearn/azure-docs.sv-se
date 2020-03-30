---
title: Distribuera ml-modeller till Azure App Service (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att distribuera en modell till en Web App i Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282825"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuera en maskininlärningsmodell till Azure App Service (förhandsversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar en modell från Azure Machine Learning som en webbapp i Azure App Service.

> [!IMPORTANT]
> Även om både Azure Machine Learning och Azure App Service är allmänt tillgängliga, är möjligheten att distribuera en modell från Machine Learning-tjänsten till App Service i förhandsversion.

Med Azure Machine Learning kan du skapa Docker-avbildningar från utbildade maskininlärningsmodeller. Den här bilden innehåller en webbtjänst som tar emot data, skickar den till modellen och returnerar sedan svaret. Azure App Service kan användas för att distribuera avbildningen och innehåller följande funktioner:

* Avancerad [autentisering](/azure/app-service/configure-authentication-provider-aad) för förbättrad säkerhet. Autentiseringsmetoder inkluderar både Azure Active Directory och multifaktorautentisering.
* [Automatisk skalning](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) utan att behöva distribuera om.
* [TLS-stöd](/azure/app-service/configure-ssl-certificate-in-code) för säker kommunikation mellan klienter och tjänsten.

Mer information om funktioner som tillhandahålls av Azure App Service finns i [översikten över App-tjänsten](/azure/app-service/overview).

> [!IMPORTANT]
> Om du behöver möjligheten att logga poängdata som används med din distribuerade modell, eller resultatet av bedömning, bör du istället distribuera till Azure Kubernetes Service. Mer information finns i [Samla in data om dina produktionsmodeller](how-to-enable-data-collection.md).

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

    > [!IMPORTANT]
    > Azure Machine Learning SDK tillhandahåller inte ett sätt för webbtjänsten att komma åt dina datalager eller datauppsättningar. Om du behöver den distribuerade modellen för att komma åt data som lagras utanför distributionen, till exempel i ett Azure Storage-konto, måste du utveckla en anpassad kodlösning med hjälp av relevant SDK. Azure Storage [SDK för Python](https://github.com/Azure/azure-storage-python).
    >
    > Ett annat alternativ som kan fungera för ditt scenario är [batchförutsägelser](how-to-use-parallel-run-step.md), som ger åtkomst till datalager när du gör mål.

    Mer information om inmatningsskript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

* **Beroenden**, till exempel hjälpskript eller Python/Conda-paket som krävs för att köra inmatningsskriptet eller modellen

Dessa entiteter är inkapslade i en __inferenskonfiguration__. Inferenskonfigurationen refererar till startskriptet och andra beroenden.

> [!IMPORTANT]
> När du skapar en slutledningskonfiguration för användning med Azure App Service måste du använda ett [miljöobjekt.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Observera att om du definierar en anpassad miljö måste du lägga till azureml-standardvärden med version >= 1.0.45 som pipberoende. Det här paketet innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst. I följande exempel visas hur du skapar ett miljöobjekt och använder det med en slutledningskonfiguration:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> När du distribuerar till Azure App Service behöver du inte skapa en __distributionskonfiguration__.

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure App Service använder du [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). Följande kodavsnitt visar hur du skapar en ny avbildning från modellen och slutledningskonfigurationen:

> [!NOTE]
> Kodavsnittet förutsätter att `model` den innehåller en registrerad `inference_config` modell och som innehåller konfigurationen för inferensmiljön. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

När `show_output=True`visas utdata från Docker-byggprocessen. När processen är klar har avbildningen skapats i Azure Container Registry för din arbetsyta. När avbildningen har skapats visas platsen i ditt Azure-behållarregister. Platsen som returneras är `<acrinstance>.azurecr.io/package:<imagename>`i formatet . Till exempel `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Spara platsinformationen, som den används när avbildningen distribueras.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Använd följande kommando för att hämta inloggningsuppgifterna för Azure Container Registry som innehåller avbildningen. Ersätt `<acrinstance>` med det värde som `package.location`returnerats tidigare från:

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

    I det här __Basic__ exemplet används`--sku B1`en grundläggande prisnivå ( ).

    > [!IMPORTANT]
    > Avbildningar som skapats av Azure Machine `--is-linux` Learning använder Linux, så du måste använda parametern.

1. Använd följande kommando om du vill skapa webbappen. Ersätt `<app-name>` med det namn du vill använda. Ersätt `<acrinstance>` `<imagename>` och med värdena `package.location` från returnerade tidigare:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Det här kommandot returnerar information som liknar följande JSON-dokument:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Nu har webbappen skapats. Men eftersom du inte har angett autentiseringsuppgifterna till Azure Container Registry som innehåller avbildningen är webbappen inte aktiv. I nästa steg anger du autentiseringsinformation för behållarregistret.

1. Om du vill ge webbappen de autentiseringsuppgifter som krävs för att komma åt behållarregistret använder du följande kommando. Ersätt `<app-name>` med det namn du vill använda. Ersätt `<acrinstance>` `<imagename>` och med värdena `package.location` från som returnerats tidigare. Ersätt `<username>` `<password>` och med ACR inloggningsinformation hämtas tidigare:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

Nu börjar webbappen läsa in bilden.

> [!IMPORTANT]
> Det kan ta flera minuter innan bilden har lästs in. Använd följande kommando för att övervaka förloppet:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> När bilden har lästs in och platsen är aktiv `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`visas ett meddelande i loggen som anger .

När avbildningen har distribuerats kan du hitta värdnamnet med hjälp av följande kommando:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Det här kommandot returnerar information `<app-name>.azurewebsites.net`som liknar följande värdnamn - . Använd det här värdet som en del av __basadressen__ för tjänsten.

## <a name="use-the-web-app"></a>Använda webbappen

Webbtjänsten som skickar begäranden till `{baseurl}/score`modellen finns på . Till exempel `https://<app-name>.azurewebsites.net/score`. Följande Python-kod visar hur du skickar data till URL:en och visar svaret:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Nästa steg

* Lär dig att konfigurera din webbapp i [App-tjänsten på Linux-dokumentation.](/azure/app-service/containers/)
* Läs mer om skalning i [Komma igång med Automatisk skalning i Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Använd ett TLS/SSL-certifikat i din Azure App Service](/azure/app-service/configure-ssl-certificate-in-code).
* [Konfigurera apptjänstappen så att den använder Azure Active Directory-inloggning](/azure/app-service/configure-authentication-provider-aad).
* [Använda en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
