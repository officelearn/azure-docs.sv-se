---
title: 'Snabb start: köra en Linux ASP.NET Core-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första ASP.NET Core-app till en Linux-behållare i App Service.
keywords: azure app service, web app, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 1eeb5bbd4b10ef660a50f40d6c1300b0ca214561
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82206686"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Skapa en ASP.NET Core-app i App Service på Linux

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Linux. Om du vill distribuera en app till App Service i _Windows_ kan du läsa [Skapa en ASP.NET Core-app i Azure](../app-service-web-get-started-dotnet.md).
>

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här snabbstarten visar hur du skapar en [.NET Core](https://docs.microsoft.com/aspnet/core/)-app med App Service på Linux. Du skapar appen med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) och använder Git för att distribuera .NET Core-koden till appen.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Du kan följa stegen i den här artikeln på en Mac-, Windows- eller Linux-dator.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* <a href="https://git-scm.com/" target="_blank">Installera Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installera den senaste .NET Core 3,1 SDK</a>

## <a name="create-the-app-locally"></a>Skapa appen lokalt

I ett terminalfönster på datorn skapar du en katalog med namnet `hellodotnetcore` och ändrar katalogen till den.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Skapa en ny .NET Core-app.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. 

Återställ NuGet-paketen och kör appen.

```bash
dotnet run
```

Öppna webbläsaren och navigera till appen på `http://localhost:5000`.

Nu kan du se **Hello World**-meddelandet från exempelappen på sidan.

![Testa med webbläsare](media/quickstart-dotnetcore/dotnet-browse-local.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern. Initiera en Git-lagringsplats för .NET Core-projektet.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Bläddra till appen som precis skapades. Ersätt _ &lt;App-Name->_ med namnet på appen.

```bash
https://<app-name>.azurewebsites.net
```

Så här bör din nya app se ut:

![Tom appsida](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

.NET Core-exempelkoden körs i App Service på Linux med en inbyggd avbildning.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Grattis!** Nu har du distribuerat din första .NET Core-app till App Service i Linux.

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

Öppna filen _Startup.cs_ i den lokala katalogen. Göra en mindre ändring i texten i metodanropet `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Spara ändringarna på Git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades i **Bläddra till app** -steget och trycker på Uppdatera.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Hantera din nya Azure-app

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade.

I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/quickstart-dotnetcore/portal-app-service-list.png)

Nu visas översiktssidan för din app. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-sidan på Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)
