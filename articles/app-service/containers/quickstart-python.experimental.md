---
title: 'Snabb start: skapa en Linux python-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första python-app till en Linux-behållare i App Service.
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: seodec18
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 7905b086ac390243f2fe02697f68c9b1c05ca28e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688985"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Skapa en python-app i Azure App Service på Linux

I den här snabb starten distribuerar du en enkel python-app till [app service på Linux](app-service-linux-intro.md), som ger en mycket skalbar och automatisk korrigering av webb värd tjänst. Du kan använda Azures kommando rads gränssnitt ( [Azure CLI](/cli/azure/install-azure-cli)) via interaktiva, webbläsarbaserat Azure Cloud Shell, så du kan följa stegen i använda en Mac-, Linux-eller Windows-dator.

![Exempelapp som körs i Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Skapa en snabbstartskatalog i Cloud Shell och ändra sedan till den.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Kör sedan följande kommando för att klona lagringsplatsen för exempelprogrammet till din snabbstartskatalog.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Medan det körs visas information liknande den i följande exempel:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Skapa ett webbprogram

Ändra till den katalog som innehåller exempelkoden och kör `az webapp up` kommandot.

Ersätt *\<app_name>* med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel.

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

Kommandot `az webapp up` utför följande åtgärder:

- Skapa en standardresursgrupp.

- Skapa en standard App Service-plan.

- Skapa ett program med det givna namnet.

- [Zip-distribuera](https://docs.microsoft.com/azure/app-service/deploy-zip) filer från den aktuella arbetskatalogen till appen.

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Python-exempelkoden körs i App Service på Linux med en inbyggd avbildning.

![Exempelapp som körs i Azure](media/quickstart-python/hello-world-in-browser.png)

**Grattis!** Du har distribuerat din första Python-app till App Service i Linux.

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

Skriv `code application.py` i Cloud Shell för att öppna Cloud Shell-redigeraren.

![application.py-kod](media/quickstart-python/code-applicationpy.png)

 Gör en mindre ändring i texten i anropet till `return`:

```python
return "Hello Azure!"
```

Spara dina ändringar och avsluta redigeraren. Använd kommandot `^S` för att spara och `^Q` för att avsluta.

Distribuera nu om programmet. Ersätt `<app_name>` med din app.

```bash
az webapp up -n <app_name>
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klickar på knappen för att uppdatera sidan.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Hantera din nya Azure-app

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade.

I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/quickstart-python/app-service-list.png)

Du ser din apps översiktssida. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![App Service-sidan på Azure Portal](media/quickstart-python/app-service-detail.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python-app med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera python-app](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Självstudie: Distribuera från privat container-lagringsplats](tutorial-custom-docker-image.md)
