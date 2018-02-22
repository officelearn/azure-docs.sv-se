---
title: Skapa en ASP.NET Core-webbapp i Azure | Microsoft Docs
description: "Lär dig hur du kör webbappar i Azure App Service genom att distribuera standard-ASP.NET Core-webbappen."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: a7f098b6c66109cb5cafbcb19e463daa15a65b59
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Skapa en ASP.NET Core-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core-webbapp i App Service på Linux](./containers/quickstart-dotnetcore.md).
>
> Instruktioner för en ASP.NET Framework-app finns i [Skapa en ASP.NET Framework-webbapp i Azure](app-service-web-get-started-dotnet-framework.md). 
>

Med [Azure Web Apps](app-service-web-overview.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst.  Den här snabbstarten visar hur du distribuerar din första ASP.NET Core-webbapp till Azure Web Apps. När du är klar har du en resursgrupp som består av en App Service-plan och en Azure-webbapp med en distribuerad webbapp.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> med följande arbetsbelastningar:
    - **ASP.NET och webbutveckling**
    - **Azure Development**

    ![ASP.NET och webbutveckling och Azure Development (under webb och moln)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa ett nytt projekt i Visual Studio genom att välja **Arkiv > Nytt > Projekt**. 

I dialogrutan **Nytt projekt** väljer du **Visual C# > Webb > ASP.NET Core-webbtillämpningsprogram**.

Ge appen namnet _myFirstAzureWebApp_, välj **Create new Git repository** (Skapa en ny Git-lagringsplats) och välj sedan **OK**.
   
![Dialogrutan Nytt projekt](./media/app-service-web-get-started-dotnet/new-project.png)

Du kan distribuera alla typer av ASP.NET Core-webbappar till Azure. I den här snabbstarten väljer du mallen **Webbprogram** och ser till att autentiseringen är inställd på **Ingen autentisering**.
      
Välj **OK**.

![Dialogrutan Nytt ASP.NET-projekt](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

När ASP.NET Core-projektet har skapats visas välkomstsidan för ASP.NET Core med ett antal länkar till resurser som hjälper dig att komma igång. 

![Välkomstsida](./media/app-service-web-get-started-dotnet/aspnet-core-welcome-page.png)

På menyn väljer du **Felsöka > Starta utan felsökning** för att köra webbappen lokalt.

![Kör appen lokalt](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)] 

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app.md)] 

![Sida för tom webbapp](media/app-service-web-get-started-html/app-service-web-service-created.png)

## <a name="push-to-azure-from-visual-studio"></a>Gör en push till Azure från Visual Studio

Gå tillbaka till Visual Studio och klicka på **Team Explorer** på **Visa**-menyn. **Team Explorer** visas.

I vyn **Home** (Start) klickar du på **Settings** (Inställningar)  > **Repository Settings** (Inställningar för lagringsplats).

![Startvyn i Team Explorer](./media/app-service-web-get-started-dotnet/team-explorer.png)

I avsnittet **Remotes** (Fjärrplatser) i **Repository Settings** (Inställningar för lagringsplatser) väljer du **Add** (Lägg till). Dialogrutan **Add Remote** (Lägg till fjärrplats) visas.

Ange _Azure_ i fältet **Name** (Namn) och ange URL:en som du sparade i [Skapa en webbapp](#create-a-web-app) i fältet **Fetch** (Hämta). Klicka på **Spara**.

![Startvyn i Team Explorer](./media/app-service-web-get-started-dotnet/team-explorer-set-remote.png)

Den här inställningen motsvarar Git-kommandot `git remote add Azure <URL>`.

Klicka på knappen **Home** (Start) längst upp.

Välj **Settings** (Inställningar)  > **Global Settings** (Globala inställningar). Kontrollera att namnet och e-postadressen har angetts. Välj **Update** (Uppdatera) om det behövs.

Visual Studio har redan checkat in alla filer på Git-lagringsplatsen när projektet skapades. Allt du behöver göra är att push-överföra filerna till Azure.

Klicka på knappen **Home** (Start) längst upp. Välj **Sync** (Synkronisering)  > **Actions** (Åtgärder)  > **Open Command Prompt** (Öppna kommandotolken). 

Ange följande kommando i kommandofönstret och ange distributionslösenordet när du uppmanas att göra det:

```
git push Azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:

```
Counting objects: 4, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 349 bytes | 349.00 KiB/s, done.
Total 4 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '9e20345e9c'.
remote: Generating deployment script.
remote: Project file path: .\myFirstAzureWebApp\myFirstAzureWebApp.csproj
remote: Solution file path: .\myFirstAzureWebApp.sln
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote:   Restoring packages for D:\home\site\repository\myFirstAzureWebApp\myFirstAzureWebApp.csproj...
remote:   Restoring packages for D:\home\site\repository\myFirstAzureWebApp\myFirstAzureWebApp.csproj...
...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Bläddra till appen

Navigera till Azure-webbappens URL i en webbläsare: `http://<app_name>.azurewebsites.net`.

Sidan körs som en Azure App Service-webbapp.

![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Grattis, din ASP.NET Core-webbapp körs live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Öppna _Pages/Index.cshtml_ från **Solution Explorer**.

Leta reda på HTML-taggen `<div id="myCarousel" class="carousel slide" data-ride="carousel" data-interval="6000">` längst upp på sidan och ersätt hela elementet med följande kod:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

I **Solution Explorer** högerklickar du på _Pages/Index.cshtml_ och klickar på **Commit** (Genomför). Ange ett meddelande för dina ändringar och klicka på **Commit All** (Genomför alla).

Push-överför sedan kodändringarna till Azure i kommandotolksfönstret.

```bash
git push Azure master
```

När distributionen är klar navigerar du till `http://<app_name>.azurewebsites.net` igen.

![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-web-app"></a>Hantera Azure-webbappen

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera webbappen.

Klicka på **Apptjänster** på menyn till vänster och välj sedan namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-dotnet/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-sidan på Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
