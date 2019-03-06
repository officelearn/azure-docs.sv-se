---
title: Skapa en Node.js-webbapp – Azure App Service | Microsoft Docs
description: Distribuera din första Node.js-Hello World-app i Azure App Service Web Apps på bara några minuter.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 237f498d1ebe2b402c86f1a4aed66a7ed443ccfa
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653681"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Skapa en Node.js-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en Node.js-webbapp i Azure App Service på Linux](./containers/quickstart-nodejs.md).
>

Med [Azure App Service](overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet.  Den här snabbstarten visar hur du distribuerar en Node.js-app till Azure App Service. Du skapar webbappen med hjälp av [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), men du kan även köra dessa kommandon lokalt med [Azure CLI](/cli/azure/install-azure-cli). Du distribuerar Node.js-exempelkoden till webbappen med hjälp av kommandot [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

![Exempelapp som körs i Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Du kan följa stegen här på en Mac-, Windows- eller Linux-dator. Det tar cirka tre minuter att slutföra stegen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Skapa en snabbstartskatalog i Cloud Shell och ändra sedan till den.

```azurecli-interactive
mkdir quickstart

cd quickstart
```

Kör sedan följande kommando för att klona lagringsplatsen för exempelprogrammet till din snabbstartskatalog.

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Medan det körs visas information liknande den i följande exempel:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> Exempel-index.js anger lyssningsporten till process.env.PORT. Den här miljövariabeln tilldelas av App Service.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

Skapa i Cloud Shell en webbapp i `myAppServicePlan` App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

Ersätt `<app_name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel.

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Ange Node.js-körning

Ange Node-körningen till 10.14.1. Om du vill se alla körningar som stöds ska du köra [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes).

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Bläddra till webbappen som precis skapades. Ersätt `<app_name>` med ett unikt appnamn.

```
http://<app_name>.azurewebsites.net
```

Så här bör din nya webbapp se ut: ![Tom sida för webbapp](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Distribuera en ZIP-fil

I Cloud Shell går du till programmets rotkatalog och skapar en ny ZIP-fil för exempelprojektet.

```azurecli-interactive
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Distribuera ZIP-filen till din webbapp med hjälp av kommandot [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen. Om någon ytterligare, anpassad versionsprocessen har konfigurerats så körs den också. Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```
http://<app_name>.azurewebsites.net
```

Node.js-exempelkoden körs i en Azure App Service-webbapp.

![Exempelapp som körs i Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> I Azure App Service körs appen i IIS med [iisnode](https://github.com/Azure/iisnode). Om du vill att appen ska kunna köras med iisnode finns det en web.config-fil i appens rotkatalog. Filen kan läsas av IIS, och iisnode-relaterade inställningar dokumenteras i [iisnode GitHub-lagringsplatsen](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

**Grattis!** Du har distribuerat din första Node.js-app till App Service.

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

Skriv `code index.js` i Cloud Shell för att öppna Cloud Shell-redigeraren.

![Kod index.js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Gör en mindre ändring i texten i anropet till `response.end`:

```javascript
response.end("Hello Azure!");
```

Spara dina ändringar och avsluta redigeraren. Använd kommandot `^S` för att spara och `^Q` för att avsluta.

Skapa en ZIP-fil och distribuera den med hjälp av kommandot [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Gå tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klicka på knappen för att uppdatera sidan.

![Uppdaterad exempelapp som körs i Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Hantera din nya Azure-app

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den webbapp som du skapade.

I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![App Service-sidan på Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Node.js med MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
