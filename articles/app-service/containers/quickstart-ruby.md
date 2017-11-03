---
title: "Skapa en Ruby-App och distribuera till App Service på Linux | Microsoft Docs"
description: "Lär dig att skapa Ruby-appar med App Service på Linux."
keywords: "Azure apptjänst, linux, oss, ruby"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: cfowler
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 55ff4dc168ca6f8b2bdbb7c5743515691e8ac92d
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>Skapa en Ruby App i App Service på Linux

[Apptjänst i Linux](app-service-linux-intro.md) ger en mycket skalbar, automatisk uppdatering värdtjänst. Den här snabbstarten visar hur du skapar en grundläggande Ruby på spår programmet du sedan distribuera den till Azure som en Webbapp i Linux.

![Hello world](./media/quickstart-ruby/hello-world-updated.png)

## <a name="prerequisites"></a>Krav

* [Ruby 2.4.1 eller högre](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller).
* [Git](https://git-scm.com/downloads).
* En [aktiv Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando för att klona exempel app lagringsplatsen till den lokala datorn i ett terminalfönster:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Kör programmet lokalt

Köra spår server för att programmet ska fungera. Ändra till den *hello world* directory, och `rails server` kommando startar servern.

```bash
cd hello-world\bin
rails server
```

Använd din webbläsare, navigera till `http://localhost:3000` att testa appen lokalt.

![Hello world](./media/quickstart-ruby/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Ändra app om du vill visa välkomstmeddelande

Ändra programmet så att det visar ett välkomstmeddelande. Först måste du ställa en väg genom att ändra den *~/workspace/ruby-docs-hello-world/config/routes.rb* filen för att inkludera en väg med namnet `hello`.

  ```ruby
  Rails.application.routes.draw do
      #For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
      root 'application#hello'
  end
  ```

Ändra programmets domänkontrollant så att den returnerar meddelandet som HTML till webbläsaren. 

Öppna *~/workspace/hello-world/app/controllers/application_controller.rb* för redigering. Ändra den `ApplicationController` klass ska se ut som följande kodexempel:

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

Appen är nu konfigurerad. Använd din webbläsare, navigera till `http://localhost:3000` bekräfta landningssida rot.

![Hello World konfigurerad](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Skapa en Ruby webbapp på Azure

En resursgrupp måste innehålla de resurser som behövs för ditt webbprogram. Du kan skapa en resursgrupp med det [az gruppen skapa]() kommando.

```azurecli-interactive
az group create --location westeurope --name myResourceGroup
```

Använd den [az programtjänstplan skapa](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) kommando för att skapa en apptjänstplan för ditt webbprogram.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Därefter utfärda den [az webapp skapa](https://docs.microsoft.com/cli/azure/webapp) kommando för att skapa webbprogram som använder den nyligen skapade serviceplanen. Observera att körningen har angetts till `ruby|2.3`. Glöm inte att ersätta `<app name>` med ett unikt appnamn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> \
--runtime "ruby|2.3" --deployment-local-git
```

Utdata från kommandot visar information om den nya webbappen och URL: en för distribution. Det bör likna följande exempel. Kopiera URL-Adressen för senare användning i den här självstudiekursen.

```bash
https://<deployment user name>@<app name>.scm.azurewebsites.net/<app name>.git
```

När webbappen har skapats ett **översikt** är tillgänglig för att visa. Navigera till den. Sidan följande skärmbild visas:

![Välkomstskärmen sida](./media/quickstart-ruby/splash-page.png)


## <a name="deploy-your-application"></a>Distribuera programmet

Kör följande kommandon för att distribuera lokala programmet till Azure-webbplatsen:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Bekräfta att fjärråtkomst distributionsåtgärder rapporterar lyckades. Kommandona producerar utdata som liknar följande:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

När distributionen är klar startar du om ditt webbprogram för att distributionen ska ske med hjälp av den [az webapp omstart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) kommandot som visas här:

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Gå till webbplatsen och kontrollera resultatet.

```bash
http://<app name>.azurewebsites.net
```

![uppdaterade för webbprogram](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> När appen startas försöker bläddra plats resulterar i en HTTP-statuskod `Error 503 Server unavailable`. Det kan ta några minuter att starta om fullständigt.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

[Azure Apptjänst i Linux vanliga frågor och svar](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
