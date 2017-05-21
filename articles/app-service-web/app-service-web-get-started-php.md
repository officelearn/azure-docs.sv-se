---
title: Skapa en PHP-app i en Azure-webbapp | Microsoft Docs
description: "Distribuera din första Hello World-app med PHP i App Service-webbappen på bara några minuter."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/04/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0541778e07193c4903a90ce0b91db224bdf60342
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-php-application-on-web-app"></a>Skapa en PHP-app i en webbapp

I den här kursen visar vi hur du utvecklar en PHP-app och distribuerar den till Azure. Vi kommer att köra appen med en [Azure App Service-plan](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) och skapa och konfigurera en ny webbapp i den med hjälp av Azure CLI. Sedan kommer vi att använda git för att distribuera PHP-appen till Azure.

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. Det tar normalt bara 5 minuter att slutföra alla steg.

## <a name="prerequisites"></a>Krav

Innan du skapar det här exemplet måste du hämta och installera följande:

* [Git](https://git-scm.com/)
* [PHP](https://php.net)
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Klona databasen för Hello World-exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
```

Ändra till den katalog som innehåller exempelkoden.

```bash
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt genom att öppna ett terminalfönster och använda `php`-kommandoraden för exemplet för att starta den inbyggda PHP-webbservern.

```bash
php -S localhost:8080
```

Öppna webbläsaren och navigera till exemplet.

```bash
http://localhost:8080
```

Nu kan du se **Hello World**-meddelandet från exempelappen på sidan.

![localhost-hello-world-in-browser](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.

## <a name="log-in-to-azure"></a>Logga in på Azure

Nu ska vi använda Azure CLI 2.0 i ett terminalfönster för att skapa de resurser som behövs för att använda Azure som värd för PHP-appen. Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

<!-- ## Configure a Deployment User -->
[!INCLUDE [login-to-azure](../../includes/configure-deployment-user.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service-plan"></a>Skapa en Azure App Service-plan

Skapa en kostnadsfri [App Service-plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) med kommandot [az appservice plan create](/cli/azure/appservice/plan#create).

<!--
 An App Service plan represents the collection of physical resources used to ..
-->
[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

I följande exempel skapas en App Service-plan med namnet `quickStartPlan` och prisnivån **Kostnadsfri**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
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

Nu när en App Service-plan har skapats kan du skapa en [webbapp](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) i `quickStartPlan` App Service-planen. Med webbappen får vi ett utrymme för att distribuera vår kod och en URL så att vi kan visa den distribuerade appen. Använd kommandot [az appservice web create](/cli/azure/appservice/web#create) för att skapa webbappen.

I kommandot nedan anger du ditt unika appnamn i platshållaren `<app_name>`. `<app_name>` används i DNS-standardwebbplatsen för webbappen. Om `<app_name>` inte är unikt får du ett felmeddelande om att webbplatsen med det angivna namnet <appens_namn> redan finns.

<!-- removed per https://github.com/Microsoft/azure-docs-pr/issues/11878
You can later map any custom DNS entry to the web app before you expose it to your users.
-->

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

![app-service-web-service-created](media/app-service-web-get-started-php/app-service-web-service-created.png)

Nu har vi skapat en ny tom webbapp på Azure.

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

Skicka till Azure-fjärrdatabasen för att distribuera appen. Du uppmanas att ange lösenordet du angav tidigare när du skapade distributionsanvändaren. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user), inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Under distributionen meddelar Azure App Service förloppet till Git.

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Nu körs sidan som visar Hello World-meddelandet som en Azure App Service-webbapp med vår PHP-kod.



## <a name="updating-and-deploying-the-code"></a>Uppdatera och distribuera koden

Öppna filen `index.php` i PHP-appen med ett lokalt textredigeringsprogram och gör små ändringar i texten i strängen bredvid `echo`:

```php
echo "Hello Azure!";
```

Spara ändringarna på git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klickar på knappen för att uppdatera.

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till Azure Portal och titta på webbappen du nyss skapade.

Logga in på [https://portal.azure.com](https://portal.azure.com).

Klicka på **Apptjänster** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

Nu visas webbappens _blad_ (en portalsida som öppnas vågrätt).

Sidan **Översikt** visas som standard på webbappens blad. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på bladet kan du se olika konfigurationssidor som du kan öppna.

![App Service-blad på Azure Portal](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

Flikarna på bladet innehåller många bra funktioner som du kan lägga till i webbappen. I listan nedan kan du se några av möjligheterna:

* Mappa ett anpassat DNS-namn
* Bind ett anpassat SSL-certifikat
* Konfigurera kontinuerlig distribution
* Skala upp
* Lägg till användarautentisering

**Grattis!** Du har distribuerat din första PHP-app till App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

> [!div class="nextstepaction"]
> [Utforska exempelskript för Web Apps CLI](app-service-cli-samples.md)


