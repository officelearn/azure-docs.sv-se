---
title: Skapa en Python-app i Linux – Azure App Service | Microsoft Docs
description: Distribuera din Hello World-app skriven i Python med Azure App Service i Linux på bara några minuter.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 04f08965d161e35a9ae4423ad5d3cf80cb407b8a
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607780"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Skapa en Python-app i Azure App Service i Linux (förhandsversion)

I den här snabbstarten distribuerar du en enkel Python-app till [App Service i Linux](app-service-linux-intro.md), vilket ger en mycket skalbar och automatiskt uppdaterad Webbvärdtjänst värdtjänst. Du använder Azures kommandoradsgränssnitt (den [Azure CLI](/cli/azure/install-azure-cli)) via interaktiv, webbläsarbaserad Azure Cloud Shell, så att du kan följa stegen använder en Mac, Linux eller Windows-dator.

![Exempelapp som körs i Azure](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:

* <a href="https://www.python.org/downloads/" target="_blank">Installera Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Installera Git</a>
* En Azure-prenumeration. Om du inte har ett redan, skapar du en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="download-the-sample-locally"></a>Ladda ned exemplet lokalt

I terminalfönstret kör du följande kommandon för att klona exempelappen till din lokala dator. Navigera sedan till katalogen som innehåller exempelkoden.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Lagringsplatsen innehåller en *application.py* som informerar App Service om att lagringsplatsen innehåller en Flask-app. Mer information finns i [Containerns startprocess och anpassningar](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. Öppna ett terminalfönster och använd kommandona nedan till att installera de nödvändiga beroendena och starta den inbyggda utvecklingsservern. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Öppna en webbläsare och navigera till exempelappen på `http://localhost:5000/`.

Du ser meddelandet **Hello World!** från exempelappen på sidan.

![Exempelapp som körs lokalt](media/quickstart-python/hello-world-in-browser.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Skapa en snabbstartskatalog i Cloud Shell och ändra sedan till den.

```bash
mkdir quickstart

cd quickstart
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

## <a name="create-a-web-app"></a>Skapa en webbapp

Ändra till den katalog som innehåller exempelkoden och kör `az webapp up` kommandot.

I följande exempel ersätter `<app-name>` med ett unikt appnamn.

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app-name>.azurewebsites.net
```

Python-exempelkoden körs i App Service på Linux med en inbyggd avbildning.

![Exempelapp som körs i Azure](media/quickstart-python/hello-world-in-browser.png)

**Grattis!** Du har distribuerat din första Python-app till App Service i Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Uppdatera lokalt och distribuera om koden

Skriv `code application.py` i Cloud Shell för att öppna Cloud Shell-redigeraren.

![application.py-kod](media/quickstart-python/code-applicationpy.png)

 Gör en mindre ändring i texten i anropet till `return`:

```python
return "Hello Azure!"
```

Spara dina ändringar och avsluta redigeraren. Använd kommandot `^S` för att spara och `^Q` för att avsluta.

Distribuera om appen med den [ `az webapp up` ](/cli/azure/ext/webapp/webapp?view=azure-cli-latest.md#ext-webapp-az-webapp-up) kommando. Ersätt namnet på din app för `<app-name>`, och ange en plats för `<location-name>` (med någon av de värden som visas från den [ `az account list-locations` ](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) kommandot).

```bash
az webapp up -n <app-name> -l <location-name>
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klickar på knappen för att uppdatera sidan.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Hantera din nya Azure-app

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den app som du skapade.

I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/quickstart-python/app-service-list.png)

Du ser din apps översiktssida. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![App Service-sidan på Azure Portal](media/quickstart-python/app-service-detail.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Den inbyggda Python-avbildningen i App Service på Linux är nu i förhandsversion, och du kan anpassa kommandot som används för att starta appen. Du kan även skapa Python-appar för produktion med en anpassad container istället.

> [!div class="nextstepaction"]
> [Självstudier: Python-app med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Python-app](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Självstudier: Köra Python-app i anpassad behållare](tutorial-custom-docker-image.md)
