---
title: Skapa en Python-webbapp i Azure | Microsoft Docs
description: "Distribuera din första Hello World-app med Python i Azure App Service Web Apps på bara några minuter."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 754c381cb242e0bdf7c56bd2a763d46acc80fbda
ms.contentlocale: sv-se
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-python-web-app-in-azure"></a>Skapa en Python-webbapp i Azure

Med [Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst.  Den här snabbstarten visar hur du utvecklar en Python-app och distribuerar den till Azure Web Apps. Du skapar webbappen med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) och använder Git för att distribuera Python-exempelkoden till webbappen.

![Exempelapp som körs i Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. Det tar cirka fem minuter att slutföra självstudiekursen när de nödvändiga komponenterna har installerats.
## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera Python](https://www.python.org/downloads/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando i ett terminalfönster för att klona databasen för exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Du använder det här terminalfönstret för att köra alla kommandon i den här snabbstarten.

Ändra till den katalog som innehåller exempelkoden.

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Installera de paket som behövs med hjälp av `pip`.

```bash
pip install -r requirements.txt
```

Kör programmet lokalt genom att öppna ett terminalfönster och använda kommandot `Python` för att starta den inbyggda Python-webbservern.

```bash
python main.py
```

Öppna en webbläsare och navigera till exempelappen på http://localhost:5000.

Nu kan du se **Hello World**-meddelandet från exempelappen på sidan.

![Exempelapp som körs lokalt](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)] 

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)] 

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app.md)] 

![Sida för tom webbapp](media/app-service-web-get-started-python/app-service-web-service-created.png)

Nu har du skapat en ny tom webbapp på Azure.

## <a name="configure-to-use-python"></a>Konfiguration för användning av Python

Använd kommandot [az webapp config set](/cli/azure/webapp/config#set) och konfigurera webbappen för att använda Python version `3.4`.

```azurecli-interactive
az webapp config set --python-version 3.4 --name <app_name> --resource-group myResourceGroup
```


När du anger Python-versionen på det här sättet används en standardbehållare som tillhandahålls av plattformen. Om du vill använda en egen behållare kan läsa CLI-referensen för kommandot [az webapp config container set](/cli/azure/webapp/config/container#set).

[!INCLUDE [Configure local git](../../includes/app-service-web-configure-local-git.md)] 

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Python-exempelkoden körs i en Azure App Service-webbapp.

![Exempelapp som körs i Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

**Grattis!** Du har distribuerat din första Python-app till App Service.

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

Öppna filen `main.py` i Python-appen med ett lokalt textredigeringsprogram och gör små ändringar i texten i strängen bredvid `return`-instruktionen:

```python
return 'Hello, Azure!'
```

Spara ändringarna på Git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle [söka efter appen](#browse-to-the-app) och klickar på knappen för att uppdatera sidan.

![Uppdaterad exempelapp som körs i Azure](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den webbapp som du skapade.

Klicka på **Apptjänster** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-blad på Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Python med PostgreSQL](app-service-web-tutorial-docker-python-postgresql-app.md)

