---
title: 'Snabb start: skapa en C# ASP.NET Core-app'
description: Lär dig hur du kör Web Apps i Azure App Service genom att distribuera din första ASP.NET Core-app.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b7402dc3f7c1e5c7ff5552b2f454156ef1539711
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212670"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snabb start: skapa en ASP.NET Core webbapp i Azure

::: zone pivot="platform-windows"  

I den här snabb starten får du lära dig hur du skapar och distribuerar din första ASP.NET Core-webbapp till [Azure App Service](overview.md). 

När du är klar har du en Azure-resurs grupp som består av en App Service värd plan och en App Service med ett distribuerat webb program.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- Den här snabb starten distribuerar en app till App Service i Windows. Information om hur du distribuerar till App Service på _Linux_finns [i skapa en .net Core-webbapp i App Service](./quickstart-dotnetcore.md).
- Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

  Om du redan har installerat Visual Studio 2019:

  - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp**  >  **sökning efter uppdateringar**.
  - Lägg till arbets belastningen genom att välja **verktyg**  >  **Hämta verktyg och funktioner**.


## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa en ASP.NET Core webbapp i Visual Studio genom att följa dessa steg:

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**väljer du **ASP.net Core webb program** och bekräftar att **C#** visas på de språk som du väljer. Välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du ditt webb program projekt *MyFirstAzureWebApp*och väljer **skapa**.

   ![Konfigurera ditt webbapp](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Du kan distribuera vilken typ av ASP.NET Core webbapp som helst till Azure, men i den här snabb starten väljer du mallen för **webb program** . Kontrol lera att **autentiseringen** är inställd på **Ingen autentisering**och att inget annat alternativ har valts. Välj sedan **Skapa**.

   ![Skapa en ny ASP.NET Core webbapp](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra din webbapp lokalt.

   ![Webbapp som körs lokalt](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publicera din webbapp

Om du vill publicera din webbapp måste du först skapa och konfigurera en ny App Service som du kan publicera din app till. 

Som en del av att konfigurera App Service skapar du:

- En ny [resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) som innehåller alla Azure-resurser för tjänsten.
- En ny [värd plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) som anger plats, storlek och funktioner för den webb Server grupp som är värd för din app.

Följ de här stegen för att skapa din App Service och publicera din webbapp:

1. I **Solution Explorer**högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**. Om du inte redan har loggat in på ditt Azure-konto från Visual Studio väljer du antingen **Lägg till ett konto** eller **Logga**in. Du kan också skapa ett kostnads fritt Azure-konto.

1. I dialog rutan **Välj ett publicerings mål** väljer du **App Service**, väljer **Skapa ny**och väljer sedan **Skapa profil**.

   ![Välj ett publiceringsmål](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. I dialog rutan **App Service: skapa ny** anger du ett globalt unikt **namn** för din app genom att antingen acceptera standard namnet eller ange ett nytt namn. Giltiga tecken är: `a-z` , `A-Z` , `0-9` , och `-` . Det här **namnet** används som URL-prefix för din webbapp i formatet `http://<app_name>.azurewebsites.net` .

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.

1. I **resurs grupp**väljer du **ny**. I **nytt resurs grupp namn**anger du *MyResourceGroup* och väljer **OK**. 

1. För **värd plan**väljer du **nytt**. 

1. Ange de värden som anges i följande tabell i dialog rutan **värd plan: skapa ny** .

   | Inställning  | Föreslaget värde | Beskrivning |
   | -------- | --------------- | ----------- |
   | **Värdplan**  | *myFirstAzureWebAppPlan* | Namnet på App Service-planen. |
   | **Plats**      | *Europa, västra* | Datacenter som är värd för webbappen. |
   | **Storlek**          | *Kostnadsfri* | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |
   
   ![Skapa ny värd plan](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Lämna **Application Insights** inställningen *ingen*.

1. I dialog rutan **App Service: skapa nytt** väljer du **skapa** för att börja skapa Azure-resurser.

   ![Skapa ny app service](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. När guiden har slutförts väljer du **publicera**.

   ![Publicera webbapp till Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Visual Studio publicerar din ASP.NET Core-webbapp till Azure och startar appen i din standard webbläsare. 

   ![Publicerad ASP.NET-webbapp som körs i Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

**Grattis!** Din ASP.NET Core-webbapp körs Live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Följ dessa steg för att uppdatera och distribuera om din webbapp:

1. Öppna **Solution Explorer** **Pages**  >  **index. cshtml**under ditt projekt i Solution Explorer.

1. Ersätt hela `<div>` taggen med följande kod:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **publicera** Sammanfattning väljer du **publicera**.

   ![Publicera uppdatering av webbapp](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterade ASP.NET-webbappen som körs i Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Om du vill hantera din webbapp går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **app Services**.

![Välj App Services](./media/quickstart-dotnetcore/app-services.png)

På sidan **app Services** väljer du namnet på din webbapp.

![Portalnavigering till Azure-app](./media/quickstart-dotnetcore/select-app-service.png)

**Översikts** sidan för din webbapp innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller fler sidor för att konfigurera din app.

![App Service i Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Visual Studio för att skapa och distribuera en ASP.NET Core-webbapp till Azure App Service.

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Med [App Service i Linux](overview.md#app-service-on-linux) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här snabbstarten visar hur du skapar en [.NET Core](https://docs.microsoft.com/aspnet/core/)-app med App Service på Linux. Du skapar appen med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) och använder Git för att distribuera .NET Core-koden till appen.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Du kan följa stegen i den här artikeln på en Mac-, Windows- eller Linux-dator.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Bläddra till appen som precis skapades. Ersätt _ &lt; app-name->_ med namnet på appen.

```bash
https://<app-name>.azurewebsites.net
```

Så här bör din nya app se ut:

![Tom appsida](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

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

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)

::: zone-end  
