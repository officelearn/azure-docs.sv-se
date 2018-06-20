---
title: Skapa en Node.js i Azure App Service i Linux | Microsoft Docs
description: Distribuera din första Hello World-app av typen Node.js i Azure App Service i Linux på bara några minuter.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/07/2017
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: eb1c769e034f37d05de63896f65290db79103637
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293912"
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Skapa en Node.js-webbapp i Azure App Service i Linux

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Linux. Om du vill distribuera en app till App Service i _Windows_ kan du läsa [Skapa en Node.js-webbapp i Azure](../app-service-web-get-started-nodejs.md).
>

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här snabbstarten visar hur du distribuerar ett Node.js-program till App Service i Linux med hjälp av [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

Du måste slutföra denna snabbstart i Cloud Shell, men du kan också köra dessa kommandon lokalt med [Azure CLI](/cli/azure/install-azure-cli).

![Exempelapp som körs i Azure](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-web-app-extension-for-cloud-shell"></a>Installera webbprogramtillägget för Cloud Shell

För att slutföra den här snabbstarten måste du lägga till [az-webbprogramtillägget](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az-extension-add). Om tillägget redan är installerat måste du uppdatera det till den senaste versionen. Skriv `az extension update -n webapp` för att uppdatera webbprogramtillägget.

Kör följande kommando om du vill installera webbprogramtillägget:

```bash
az extension add -n webapp
```

När du har installerat tillägget visar Cloud Shell information i följande exempel:

```bash
The installed extension 'webapp' is in preview.
```

## <a name="download-the-sample"></a>Hämta exemplet

Skapa en snabbstartskatalog i Cloud Shell och ändra sedan till den.

```bash
mkdir quickstart

cd quickstart
```

Kör sedan följande kommando för att klona lagringsplatsen för exempelprogrammet till din snabbstartskatalog.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Medan det körs visas information liknande den i följande exempel:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
````

## <a name="create-a-web-app"></a>Skapa en webbapp

Ändra till den katalog som innehåller exempelkoden och kör `az webapp up` kommandot.

Ersätt <app_name> med ett unikt programnamn i följande exempel.

```bash
cd nodejs-docs-hello-world

az webapp up -n <app_name>
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

Kommandot `az webapp up` utför följande åtgärder:

- Skapa en standardresursgrupp.

- Skapa en standard App Service-plan.

- Skapa ett program med det givna namnet.

- [Zip-distribuera](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-zip) filer från den aktuella arbetskatalogen till webbprogrammet.

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren. Ersätt <app name> med namnet på ditt webbprogram.

```bash
http://<app_name>.azurewebsites.net
```

Node.js-exempelkoden körs i en webbapp med inbyggd avbildning.

![Exempelapp som körs i Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Grattis!** Nu har du distribuerat din första Node.js-app till App Service i Linux.

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

I Cloud Shell skriver du `nano index.js` för att öppna nanotextredigerare.

![Nano index.js](media/quickstart-nodejs/nano-indexjs.png)

 Gör en mindre ändring i texten i anropet till `response.end`:

```nodejs
response.end("Hello Azure!");
```

Spara dina ändringar och avsluta nano. Använd kommandot `^O` för att spara och `^X` för att avsluta.

Distribuera nu om programmet. Ersätt `<app_name>` med ditt webbprogram.

```bash
az webapp up -n <app_name>
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klickar på knappen för att uppdatera sidan.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den webbapp som du skapade.

Klicka på **App Services** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Nu visas sidan Översikt för din webbapp. Här kan du slutföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![App Service-sidan på Azure Portal](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen.

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resursgruppen från Cloud Shell. Om du har ändrat regionen uppdaterar du resursgruppens namn `appsvc_rg_Linux_CentralUS` till resursgruppen som är specifik för ditt program.

```azurecli-interactive
az group delete --name appsvc_rg_Linux_CentralUS
```

Det kan några minuter att köra kommandot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Node.js med MongoDB](tutorial-nodejs-mongodb-app.md)
