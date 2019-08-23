---
title: Distribuera ml-modeller till Azure App Service (för hands version)
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder tjänsten Azure Machine Learning för att distribuera en modell till en webbapp i Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897417"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuera en maskin inlärnings modell till Azure App Service (för hands version)

Lär dig hur du distribuerar en modell från Azure Machine Learning-tjänsten som en webbapp i Azure App Service.

> [!IMPORTANT]
> Både Azure Machine Learning tjänst och Azure App Service är allmänt tillgängliga, men möjligheten att distribuera en modell från Machine Learnings tjänsten till App Service är i för hands version.

Med Azure Machine Learning tjänsten kan du skapa Docker-avbildningar från tränade maskin inlärnings modeller. Den här avbildningen innehåller en webb tjänst som tar emot data, skickar den till modellen och returnerar svaret. Azure App Service kan användas för att distribuera avbildningen och innehåller följande funktioner:

* Avancerad [autentisering](/azure/app-service/configure-authentication-provider-aad) för förbättrad säkerhet. Autentiseringsmetoder omfattar både Azure Active Directory och Multi-factor auth.
* [Skala](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) utan att behöva distribuera igen.
* [SSL-stöd](/azure/app-service/app-service-web-ssl-cert-load) för säker kommunikation mellan klienter och tjänsten.

Mer information om funktioner som tillhandahålls av Azure App Service finns i [Översikt över App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Om du behöver kunna logga de bedömnings data som används med din distribuerade modell, eller resultatet av en bedömning, bör du istället distribuera till Azure Kubernetes-tjänsten. Mer information finns i [samla in data på dina produktions modeller](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .
* En utbildad Machine Learning-modell som registrerats i din arbets yta. Om du inte har någon modell använder du [själv studie kursen om bild klassificering: träna modell](tutorial-train-models-with-aml.md) att träna och registrera en.

    > [!IMPORTANT]
    > Kodfragmenten i den här artikeln förutsätter att du har angett följande variabler:
    >
    > * `ws`– Din Azure Machine Learning-arbetsyta.
    > * `model`– Den registrerade modellen som ska distribueras.
    > * `inference_config`– Den här modellens konfigurations konfiguration.
    >
    > Mer information om hur du ställer in dessa variabler finns i [Distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Innan du distribuerar måste du definiera vad som behövs för att köra modellen som en webb tjänst. I följande lista beskrivs de grundläggande objekt som behövs för en-distribution:

* Ett __Entry-skript__. Det här skriptet accepterar begär Anden, visar begäran med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > Start skriptet är bara för din modell. den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    > Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också omvandla svaret innan det returneras till klienten.

    > [!IMPORTANT]
    > Azure Machine Learning SDK tillhandahåller inte något sätt för webb tjänsten att komma åt dina data lager eller data uppsättningar. Om du behöver distribuerad modell för att komma åt data som lagras utanför distributionen, t. ex. i ett Azure Storage konto, måste du utveckla en anpassad kod med hjälp av relevant SDK. Till exempel [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python).
    >
    > Ett annat alternativ som kan fungera för ditt scenario är [batch](how-to-run-batch-predictions.md)-förutsägelser, vilket ger åtkomst till data lager när poäng.

    Mer information om Entry-skript finns i [Distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

* **Beroenden**, till exempel hjälp skript eller python/Conda-paket som krävs för att köra registrerings skriptet eller modellen

Dessa entiteter kapslas in i en konfiguration för en __härledning__. Konfigurations konfigurationen refererar till Start skriptet och andra beroenden.

> [!IMPORTANT]
> När du skapar en konfigurations konfiguration för användning med Azure App Service måste du använda ett [miljö](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) objekt. I följande exempel visas hur du skapar ett miljö objekt och använder det med en konfigurations konfiguration:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om konfiguration av konfiguration finns i [Distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

> [!IMPORTANT]
> När du distribuerar till Azure App Service behöver du inte skapa en distributions __konfiguration__.

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure App Service använder du [modell. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Följande kodfragment visar hur du skapar en ny avbildning från modellen och konfigurationen för konfigurations härledning:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

När `show_output=True`visas utdata från Docker-build-processen. När processen har slutförts har avbildningen skapats i Azure Container Registry för din arbets yta.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Välj arbets ytan Azure Machine Learning från [Azure Portal](https://portal.azure.com). I avsnittet __Översikt__ använder du __register__ länken för att komma åt Azure Container Registry för arbets ytan.

    [![Skärm bild av översikten över arbets ytan](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. Från Azure Container Registry väljer du __databaser__och väljer sedan det avbildnings __namn__ som du vill distribuera. För den version som du vill distribuera väljer du posten __...__ och __distribuerar sedan till Web App__.

    [![Skärm bild av distribution från ACR till en webbapp](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. Skapa webbappen genom att ange ett plats namn, en prenumeration, en resurs grupp och välj App Service-plan/plats. Välj slutligen __skapa__.

    ![Skärm bild av dialog rutan ny webbapp](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Använda webbapp

Från [Azure Portal](https://portal.azure.com)väljer du den webbapp som skapades i föregående steg. I avsnittet __Översikt__ kopierar du __URL: en__. Det här värdet är __bas-URL:__ en för tjänsten.

[![Skärm bild av översikten över webb programmet](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

Webb tjänsten som skickar begär anden till modellen finns på `{baseurl}/score`. Till exempel `https://mywebapp.azurewebsites.net/score`. Följande python-kod visar hur du skickar data till URL: en och visar svaret:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
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

* Mer information om hur du konfigurerar webbappen finns i [app service på Linux](/azure/app-service/containers/) -dokumentationen.
* Mer information om skalning finns i [Kom igång med automatisk skalning i Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Mer information om SSL-stöd finns [i använda ett SSL-certifikat i Azure App Service](/azure/app-service/app-service-web-ssl-cert-load).
* Mer information om autentisering finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](/azure/app-service/configure-authentication-provider-aad).
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)