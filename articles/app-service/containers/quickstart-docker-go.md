---
title: Skapa Docker-/Go-app i Linux – Azure App Service
description: Så här distribuerar du en Docker-avbildning som kör ett Go-program till Web App for Containers.
keywords: azure app service, web app, go, docker, container
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ec2b974e008ea4c7e266f5ae0d46cd67d2133e54
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854005"
---
# <a name="run-a-custom-linux-container-in-azure-app-service"></a>Köra en anpassad Linux-behållare i Azure App Service

[App Service Linux](app-service-linux-intro.md) tillhandahåller fördefinierade programstackar i Linux med stöd för språk som .NET, PHP, Node.js med flera. Du kan också använda en anpassad Docker-avbildning för att köra din webbapp på en programstack som inte redan har definierats i Azure. I den här snabbstarten får du lära dig hur man skapar en webbapp och distribuerar en Go-avbildning från Docker Hub. Du skapar webbappen med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Exempelapp som körs i Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en [webbapp](../overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Glöm inte att ersätta `<app name>` med ett globalt unikt app-namn (giltiga tecken `a-z`är `0-9`, och `-`).

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

I det föregående kommandot pekar `--deployment-container-image-name` på den offentliga Docker Hub-avbildningen [microsoftazure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Bläddra till appen

```bash
http://<app_name>.azurewebsites.net/hello
```

![Exempelapp som körs i Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Grattis!** Du har distribuerat en anpassad Docker-avbildning som kör ett Go-program i Web App for Containers.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: Distribuera från privat container-lagringsplats](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Konfigurera en anpassad behållare](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Självstudier: WordPress-app med flera behållare](tutorial-multi-container-app.md)
