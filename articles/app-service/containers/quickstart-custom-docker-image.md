---
title: "Kör en anpassad Docker Hub-avbildning i Azure Web App for Containers | Microsoft Docs"
description: "Så här använder du en anpassad Docker-avbildning för Azure Web App for Containers."
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
ms.openlocfilehash: 8e7afd89def170ce756aae9e76daf91d78cc20e0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Kör en anpassad Docker Hub-avbildning i Azure Web App for Containers

App Service tillhandahåller fördefinierade programstackar i Linux med stöd för specifika versioner, till exempel PHP 7.0 och Node.js 4.5. Du kan också använda en anpassad Docker-avbildning för att köra din webbapp på en programstack som inte redan har definierats i Azure. I den här snabbstarten får du lära dig hur man skapar en webbapp och distribuerar [den officiella Nginx Docker-avbildningen](https://hub.docker.com/r/_/nginx/) till den. Du skapar webbappen med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Exempelapp som körs i Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Skapa en webbapp för behållare

Skapa en [webbapp](../app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#create). Glöm inte att ersätta `<app name>` med ett unikt appnamn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

I det föregående kommandot pekar `--deployment-container-image-name` på den offentliga Docker Hub-avbildningen [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

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

Bläddra till följande URL med hjälp av webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

![Exempelapp som körs i Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Grattis!** Nu har du distribuerat en anpassad Docker-avbildning för Web App for Containers.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda anpassad Docker-avbildning](tutorial-custom-docker-image.md)
