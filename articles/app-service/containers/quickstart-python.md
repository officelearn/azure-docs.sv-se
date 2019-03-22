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
ms.date: 02/08/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5cca0d866442583c87665b3a1db8c65c66d12f0a
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285385"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Skapa en Python-app i Azure App Service i Linux (förhandsversion)

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här snabbstarten visar hur du distribuerar en Python-app ovanpå den inbyggda Python-avbildningen (förhandsversion) i App Service i Linux med hjälp av [Azure-CLI:t](/cli/azure/install-azure-cli).

Du kan följa stegen i den här artikeln på en Mac-, Windows- eller Linux-dator.

![Exempelapp som körs i Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* <a href="https://www.python.org/downloads/" target="_blank">Installera Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Installera Git</a>

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

Ersätt < appnamn > i följande exempel med ett unikt appnamn.

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

Distribuera nu om programmet. Ersätt `<app-name>` med din app.

```bash
az webapp up -n <app-name>
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
> [Python med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ett anpassat startkommando](how-to-configure-python.md#customize-startup-command)

> [!div class="nextstepaction"]
> [Felsökning](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [Använda anpassade avbildningar](tutorial-custom-docker-image.md)
