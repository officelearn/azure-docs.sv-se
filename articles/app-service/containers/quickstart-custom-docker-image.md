---
title: "Kör en anpassad avbildning Docker-hubb i Azure Web App för behållare | Microsoft Docs"
description: "Hur du använder en anpassad Docker-avbildning för Azure Web App för behållare."
keywords: "Azure apptjänst, webbprogram, linux, docker, behållare"
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
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Kör en anpassad avbildning Docker-hubb i Azure Web App för behållare

Apptjänst innehåller fördefinierade program stackar på Linux med stöd för specifika versioner, till exempel PHP 7.0 och Node.js 4.5. Du kan också använda en anpassad Docker-avbildning för att köra ditt webbprogram på en programstack som inte redan har definierats i Azure. Den här snabbstarten visar hur du skapar en webbapp och distribuera den [officiella Nginx Docker-avbildningen](https://hub.docker.com/r/_/nginx/) till den. Du skapar en web app med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Exempelapp som körs i Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Skapa ett webbprogram för behållaren

Skapa en [webbapp](../app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#create). Glöm inte att ersätta `<app name>` med ett unikt appnamn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

I det föregående kommandot `--deployment-container-image-name` pekar på den offentliga Docker hubb bilden [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

När webbappen har skapats visas Azure CLI utdata liknar följande exempel:

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

Bläddra till följande URL: en med hjälp av webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

![Exempelapp som körs i Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Grattis!** Du har distribuerat en anpassad Docker-avbildning till webbprogram för behållare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd en anpassad Docker-bild](tutorial-custom-docker-image.md)
