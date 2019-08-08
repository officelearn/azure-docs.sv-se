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
ms.openlocfilehash: 84de9d53b19f5aa9b73570aa0d115d204e8b6596
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848223"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuera en maskin inlärnings modell till Azure App Service (för hands version)

Lär dig hur du distribuerar en modell från Azure Machine Learning-tjänsten som en webbapp i Azure App Service.

> [!IMPORTANT]
> Både Azure Machine Learning tjänst och Azure App Service är allmänt tillgängliga, men möjligheten att distribuera en modell från Machine Learnings tjänsten till App Service är i för hands version.

Med Azure Machine Learning tjänsten kan du skapa Docker-avbildningar från tränade maskin inlärnings modeller. Den här avbildningen innehåller en webb tjänst som tar emot data, skickar den till modellen och returnerar svaret. Azure App Service kan användas för att distribuera avbildningen och innehåller följande funktioner:

* [SSL-stöd](/azure/app-service/app-service-web-ssl-cert-load) för säker kommunikation mellan klienter och tjänsten.
* [Skala ut](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) till flera instanser utan att behöva distribuera om.
* [Avancerad autentisering](/azure/app-service/configure-authentication-provider-aad) för förbättrad säkerhet.

Mer information om funktioner som tillhandahålls av Azure App Service finns i [Översikt över App Service](/azure/app-service/overview).

## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .
* En utbildad Machine Learning-modell som registrerats i din arbets yta. Om du inte har någon modell använder du [själv studie kursen om bild klassificering: träna modell](tutorial-train-models-with-aml.md) att träna och registrera en.
* En Docker-avbildning som skapats från modellen. Om du inte har någon avbildning använder du avbildnings [klassificeringen: Distribuera modell](tutorial-deploy-models-with-aml.md) för att skapa en.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Välj arbets ytan Azure Machine Learning från [Azure Portal](https://portal.azure.com). I avsnittet __Översikt__ använder du __register__ länken för att komma åt Azure Container Registry för arbets ytan.

    ![Skärm bild av översikten över arbets ytan](media/how-to-deploy-app-service/workspace-overview.png)

2. Från Azure Container Registry väljer du __databaser__och väljer sedan det avbildnings __namn__ som du vill distribuera. För den version som du vill distribuera väljer du posten __...__ och __distribuerar sedan till Web App__.

    ![Skärm bild av distribution från ACR till en webbapp](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Skapa webbappen genom att ange ett plats namn, en prenumeration, en resurs grupp och välj App Service-plan/plats. Välj slutligen __skapa__.

    ![Skärm bild av dialog rutan ny webbapp](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Använda webbapp

Från [Azure Portal](https://portal.azure.com)väljer du den webbapp som skapades i föregående steg. I avsnittet __Översikt__ kopierar du __URL: en__. Det här värdet är __bas-URL:__ en för tjänsten.

![Skärm bild av översikten över webb programmet](media/how-to-deploy-app-service/web-app-overview.png)

Webb tjänsten som skickar begär anden till modellen finns på `{baseurl}/score`. Till exempel `https://mywebapp.azurewebsites.net/score`. Följande python-kod visar hur du skickar data till URL: en och visar svaret:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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