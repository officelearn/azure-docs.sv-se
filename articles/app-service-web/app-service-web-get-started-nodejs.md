---
title: Skapa en Node.js-app i en webbapp | Microsoft Docs
description: "Distribuera din första Hello World-app med Node.js i App Service-webbappen på bara några minuter."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/28/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: c32cb52e4bb7bacde20e21820f277b4e86877e74
ms.lasthandoff: 04/25/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>Skapa en Node.js-app i en webbapp

I den här kursen visar vi hur du utvecklar en Node.js-app och distribuerar den till Azure. Vi kommer att köra appen med en Linux-baserad Azure App Service och skapa och konfigurera en ny webbapp i den med hjälp av Azure CLI. Sedan kommer vi att använda git för att distribuera Node.js-appen till Azure.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. Det tar normalt bara 5 minuter att slutföra alla steg.

## <a name="before-you-begin"></a>Innan du börjar

Innan du kör det här exemplet måste du installera följande på den lokala datorn:

1. [Hämta och installera git](https://git-scm.com/)
1. [Hämta och installera Node.js och NPM](https://nodejs.org/)
1. Hämta och installera [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Klona databasen för Hello World-exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

> [!TIP]
> Du kan också [hämta exemplet](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) som en zip-fil och extrahera den.

Ändra till den katalog som innehåller exempelkoden.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt genom att öppna ett terminalfönster och använda `npm start`-skriptet för exemplet för att starta den inbyggda Node.js http-servern.

```bash
npm start
```

Öppna webbläsaren och navigera till exemplet.

```bash
http://localhost:1337
```

Nu kan du se **Hello World**-meddelandet från exempelappen på sidan.

![localhost-hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.

## <a name="log-in-to-azure"></a>Logga in på Azure

Nu ska vi använda Azure CLI 2.0 i ett terminalfönster för att skapa de resurser som behövs för att använda Azure som värd för Node.js-appen. Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

För FTP och lokal Git måste du ha en distributionsanvändare konfigurerad på servern för att autentisera din distribution. Du behöver bara skapa distributionsanvändaren en gång. Anteckna användarnamnet och lösenordet eftersom du kommer att använda dem i ett steg nedan.

> [!NOTE]
> En distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp.
> `username` och `password` är på kontonivå, vilket innebär att de skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration. Dessa autentiseringsuppgifter behöver bara skapas en gång.
>

Använd kommandot [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) för att skapa dina autentiseringsuppgifter på kontonivå.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Skapa en Azure App Service

Skapa en Linux-baserad App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan#create).

> [!NOTE]
> En App Service-plan representerar den samling fysiska resurser som dina appar finns i. Alla program som har tilldelats en App Service-plan delar de resurser som definierats av planen. Det innebär att du kan minska kostnaderna när du har flera appar i en plan.
>
> App Service-planer definierar följande:
> * Region (Europa, norra; USA, östra; Asien, sydöstra)
> * Instansstorlek (liten, medel, stor)
> * Skalningsantal (en, två eller tre instanser osv.)
> * SKU (Kostnadsfri, Delad, Basic, Standard, Premium)
>

I följande exempel skapas en App Service-plan på Linux-arbetare med namnet `quickStartPlan` och prisnivån **Standard**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Nu när en App Service-plan har skapats kan du skapa en webbapp i `quickStartPlan` App Service-planen. Med webbappen får vi ett utrymme för att distribuera vår kod och en URL så att vi kan visa den distribuerade appen. Använd kommandot [az appservice web create](/cli/azure/appservice/web#create) för att skapa webbappen.

I kommandot nedan anger du ditt unika appnamn i platshållaren `<app_name>`. `<app_name>` används som standard-DNS-webbplats för webbappen. Därför måste namnet vara unikt i förhållande till alla appar på Azure. Du kan senare mappa en anpassad DNS-post till webbappen innan du exponerar den för användarna.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

När webbappen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

Gå till webbplatsen för att se webbappen du precis skapade.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

Nu har vi skapat en ny tom webbapp på Azure. Och nu ska vi konfigurera webbappen för användning av Node.js och distribuera vår app till den.

## <a name="configure-to-use-nodejs"></a>Konfiguration för användning av Node.js

Använd kommandot [az appservice web config update](/cli/azure/app-service/web/config#update) för att konfigurera webbappen för användning av Node.js version `6.9.3`.

> [!TIP]
> När node.js-versionen konfigureras på det här sättet används en standardbehållare som tillhandahålls av plattformen. Om du vill använda en egen behållare kan du läsa CLI-referensen för kommandot [az appservice web config container update](/cli/azure/appservice/web/config/container#update).

```azurecli
az appservice web config update --linux-fx-version "NODE|6.9.3" --startup-file process.json --name <app_name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

Du kan distribuera till din webbapp på flera olika sätt, t.ex. FTP, lokal Git samt GitHub, Visual Studio Team Services och Bitbucket.

Använd kommandot [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) för att konfigurera lokal git-åtkomst till webbappen.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Kopiera utdata från terminalen. De kommer att användas i nästa steg.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Lägg till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <paste-previous-command-output-here>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen. Du uppmanas att ange lösenordet du angav tidigare. Det behövs för att skapa distributionsanvändaren.

```azurecli
git push azure master
```

Under distributionen meddelar Azure App Service förloppet till Git.

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Nu körs sidan som visar Hello World-meddelandet som en Azure App Service-webbapp med vår Node.js-kod.

## <a name="updating-and-deploying-the-code"></a>Uppdatera och distribuera koden

Öppna filen `index.js` i Node.js-appen med ett lokalt textredigeringsprogram och gör små ändringar i texten inom anropet till `response.end`:

```nodejs
response.end("Hello Azure!");
```

Spara ändringarna på git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle gå till appen och klickar på knappen för att uppdatera.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till Azure Portal och titta på webbappen du nyss skapade.

Logga in på [https://portal.azure.com](https://portal.azure.com).

Klicka på **Apptjänster** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Nu visas webbappens _blad_ (en portalsida som öppnas vågrätt).

Sidan **Översikt** visas som standard på webbappens blad. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på bladet kan du se olika konfigurationssidor som du kan öppna.

![App Service-blad på Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Flikarna på bladet innehåller många bra funktioner som du kan lägga till i webbappen. I listan nedan kan du se några av möjligheterna:

* Mappa ett anpassat DNS-namn
* Bind ett anpassat SSL-certifikat
* Konfigurera kontinuerlig distribution
* Skala upp
* Lägg till användarautentisering

**Grattis!** Du har distribuerat din första Node.js-app till App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Utforska färdiga [CLI-skript för Web Apps](app-service-cli-samples.md).

