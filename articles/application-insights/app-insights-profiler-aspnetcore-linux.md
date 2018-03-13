---
title: Profilen ASP.NET core Azure Linux-webbappar med Application Insights Profiler | Microsoft Docs
description: "Översikt över begrepp och stegvisa självstudier om hur du aktiverar det"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 7f3a0a1a22e14f12b86474dd5b1985029074444e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilen ASP.NET Core Azure Linux-Webbappar med Application Insights Profiler

Den här funktionen är för närvarande under förhandsgranskning

Ta reda på hur mycket tid läggs i varje webbprogrammet live-metoden när du använder [Programinsikter](app-insights-overview.md). Profiler är nu tillgängliga för webbprogram för ASP.NET core finns i Linux på App-tjänster. Den här guiden innehåller stegvisa instruktioner om hur profiler-spårningar kan samlas in för webbprogram för ASP.NET core Linux.

När du har slutfört den här genomgången appen samlar in profiler-spårningar liknar skärmbilden nedan. I det här exemplet anger profileraren spårningen en viss webbegäran är långsam eftersom de flesta arbetstid för väntar. Varm sökvägen i koden som saktas ner appen föregås av ikonen låga. Det här exemplet illustrerar `About` metod i `HomeController` långsam webbprogrammet eftersom det anropar `Thread.Sleep`.

![Profiler-spårningar](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Förutsättningar
Anvisningarna nedan för alla Windows, Linux och Mac utvecklingsmiljöer:

* Installera [.NET core SDK 2.1.2 eller senare](https://www.microsoft.com/net/download/windows/build)
* Installera Git följa anvisningarna i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## <a name="setup-project-locally"></a>Konfigurera projektet lokalt

1. Öppna en kommandotolk på din dator. Anvisningarna nedan fungerar för alla Windows, Linux och Mac utvecklingsmiljöer.

2. Skapa en ASP.NET core MVC-webbapp
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Ändra katalogen i Kommandotolken till rotmappen för projektet

4. Lägg till Nuget-paketet för att samla in profiler-spårningar.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Lägg till en rad med kod för att slumpmässigt fördröjning på några sekunder i HomeController.cs

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. Spara och genomför ändringarna till den lokala databasen

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Skapa Azure App Service som värd för ditt projekt
1. Skapa en App Services Linux-miljö

    ![Skapa Linux Apptjänster](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Skapa distribution av autentiseringsuppgifter. Anteckna lösenordet som du behöver det senare när du distribuerar din app.

    ![Skapa autentiseringsuppgifter för distribution](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Välj alternativ för användning. Konfigurera en lokal Git-lagringsplats i webbapp följa instruktionerna på Azure-portalen. En Git-lagringsplats skapas automatiskt.

    ![Konfigurera Git-lagringsplats](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Fler distributionsalternativ för finns [här](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)

## <a name="deploy-your-project"></a>Distribuera projektet

1. I Kommandotolken, navigerar du till din rotmapp för projektet. Lägg till fjärransluten Git-lagringsplats att peka på App-tjänster:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Använda 'användarnamn' från steg i ”Skapa distribution av autentiseringsuppgifter”.
    * Använd 'app name' från steg i ”Skapa apptjänst”.

2. Distribuera projektet genom att skicka ändringarna till Azure

    ```
    git push azure master
    ```
Du kommer se utdata som liknar följande:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Lägg till Application Insights för att övervaka dina webbprogram
1. [Skapa en Application Insights-resurs](./app-insights-create-new-resource.md)
2. Kopiera iKey av Application Insights-resursen och ange följande inställningar i App-tjänster

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Ange inställningar för app](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Ändra inställningar för appen startas automatiskt platsen. När de nya inställningarna tillämpas startar profileraren omedelbart 2 minuter. sedan körs i två minuter varje timme.

3. Generera en viss trafik till din webbplats. Du kan uppdatera platsen ```About``` för några gånger.

4. Vänta 2-5 minuter så att händelserna som kan sammanställas till Application Insights.

5. Gå till Application Insights prestanda rutan i Azure-portalen. Du ser profiler-spårningar som är tillgängliga i det nedre högra hörnet.

    ![Visa spårningar](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="report-issues-to-project-github-repository"></a>Rapporten problem projektet Github-lagringsplatsen
Om du har några problem eller förslag kan rapportera till vår github-lagringsplats: [ApplicationInsights-Profiler-AspNetCore: problem](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
