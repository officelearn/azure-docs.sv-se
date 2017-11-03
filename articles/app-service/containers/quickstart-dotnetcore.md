---
title: "Skapa en .NET Core-webbapp och distribuera till App Service på Linux | Microsoft Docs"
description: "Distribuera din första .NET Core Hello World-app till App Service på Linux i minuter."
keywords: "Azure apptjänst, webbprogram, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2a8000cadd6f6d7204e1790df62443a7ac7598c9
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>Skapa en .NET Core-webbapp i App Service på Linux

[Apptjänst i Linux](app-service-linux-intro.md) ger en mycket skalbar, automatisk uppdatering värdtjänst med Linux-operativsystem. Den här snabbstarten visar hur du skapar en [.NET Core](https://docs.microsoft.com/aspnet/core/) appen på Apptjänst i Linux. Du skapar en web app med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), och du använder Git för att distribuera .NET Core-kod till webbappen.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera .NET Core SDK](https://www.microsoft.com/net/download/core)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-app-locally"></a>Skapa appen lokalt

I ett terminalfönster på din dator skapar du en katalog med namnet `hellodotnetcore` och ändra katalogen till den.

```bash
md hellodotnetcore
cd hellodotnetcore
```

Skapa ett nytt webbprogram för .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Återställa NuGet-paketen och kör appen.

```bash
dotnet restore
dotnet run
```

Öppna en webbläsare och gå till app på `http://localhost:5000`.

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

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-with-built-in-image"></a>Skapa en webbapp med inbyggda avbildning

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)]

Bläddra till den nya webbappen. Ersätt  _&lt;appnamn >_ med ett unikt appnamn.

```bash
http://<app name>.azurewebsites.net
```

![Sida för tom webbapp](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Node.js-exempelkod körs i en webbapp med inbyggda avbildning.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Grattis!** Du har distribuerat din första Node.js-app till App Service på Linux.

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

I den lokala katalogen öppnar den _Startup.cs_ fil. Göra en mindre ändring i texten i metodanropet `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Spara ändringarna på Git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klickar på knappen för att uppdatera.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den webbapp som du skapade.

Klicka på **Apptjänster** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/quickstart-dotnetcore/portal-app-service-list.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-sidan på Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en .NET Core och SQL Database-webbapp i Azure App Service på Linux](tutorial-dotnetcore-sqldb-app.md)
