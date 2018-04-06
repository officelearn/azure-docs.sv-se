---
title: Profilen ASP.NET Core Azure Linux-webbappar med Application Insights Profiler | Microsoft Docs
description: En översikt och stegvisa självstudier om hur du använder Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilen ASP.NET Core Azure Linux-webbappar med Application Insights Profiler

Den här funktionen är för närvarande en förhandsversion.

Ta reda på hur mycket tid läggs i varje webbprogrammet live-metoden när du använder [Programinsikter](app-insights-overview.md). Application Insights Profiler är nu tillgänglig för ASP.NET Core webbprogram som finns i Linux på Azure App Service. Den här guiden innehåller stegvisa instruktioner om hur Profiler-spårningar kan samlas in för ASP.NET Core Linux web apps.

När du har slutfört den här genomgången appen kan samla in Profiler-spårningar som spår som visas i bilden. I det här exemplet anger profileraren spårningen att en viss webbegäran går långsamt på grund av tid väntar. Den *varm sökvägen* i koden som saktas appen markeras med en ikon för låga. Den **om** metod i den **HomeController** avsnitt saktas webbprogrammet eftersom metoden anropar den **Thread.Sleep** funktion.

![Profiler-spårningar](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Krav
Följande instruktioner gäller för alla miljöer för utveckling av Windows, Linux och Mac:

* Installera den [.NET Core SDK 2.1.2 eller senare](https://www.microsoft.com/net/download/windows/build).
* Installera Git genom att följa anvisningarna i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurera projektet lokalt

1. Öppna ett kommandotolksfönster på din dator. Följande instruktioner fungerar för alla Windows, Linux och Mac utvecklingsmiljöer.

2. Skapa ett ASP.NET Core MVC-webbprogram:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. Ändra arbetskatalogen till rotmappen för projektet.

4. Lägg till NuGet-paketet för att samla in Profiler-spårningar:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Lägg till en rad med kod i den **HomeController.cs** avsnittet för att slumpmässigt fördröjning på några sekunder:

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

6. Spara och genomför ändringarna till den lokala databasen:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Skapa Linux-webbprogram som värd för ditt projekt

1. Skapa webbprogrammiljö genom att använda Apptjänst på Linux:

    ![Skapa Linux-webbprogram](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Skapa autentiseringsuppgifter för distribution:

    > [!NOTE]
    > Registrera ditt lösenord för senare användning när du distribuerar ditt webbprogram.

    ![Skapa autentiseringsuppgifter för distribution](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Välj distributionsalternativ för. Konfigurera en lokal Git-lagringsplats i webbapp genom att följa anvisningarna på Azure-portalen. En Git-lagringsplats skapas automatiskt.

    ![Konfigurera Git-lagringsplats](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Fler distributionsalternativ, se [i den här artikeln](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Distribuera projektet

1. Bläddra till rotmappen för ditt projekt i Kommandotolk-fönster. Lägg till en fjärransluten Git-lagringsplats att peka mot databasen på Apptjänst:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Använd den **användarnamn** som användes för att skapa autentiseringsuppgifter för distribution.
    * Använd den **appnamn** som användes för att skapa webbprogrammet med hjälp av Apptjänst i Linux.

2. Distribuera projektet genom att skicka ändringarna till Azure:

    ```
    git push azure master
    ```

Du bör se utdata som liknar följande exempel:

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

1. [Skapa en resurs för Application Insights](./app-insights-create-new-resource.md).

2. Kopiera den **iKey** värdet för Application Insights-resursen och ange följande inställningar i web apps:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Konfigurera appinställningar för](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    När app-inställningar ändras, platsen startar om automatiskt. När de nya inställningarna tillämpas körs omedelbart profileraren i två minuter. Profileraren körs i två minuter varje timme.

3. Generera en viss trafik till din webbplats. Du kan skapa trafik genom att uppdatera platsen **om** sidan några gånger.

4. Vänta två till fem minuter för att händelser ska sammanställd till Application Insights.

5. Bläddra till Application Insights **prestanda** rutan i Azure-portalen. Du kan visa Profiler-spårningar längst ned till höger i fönstret.

    ![Visa Profiler-spårningar](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Kända problem

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>Åtgärden Aktivera i rutan profileraren konfigurationen fungerar inte

> [!NOTE]
> Om du är värd för din app med hjälp av Apptjänst i Linux, behöver du inte återaktivera profileraren i den **prestanda** rutan i Application Insights-portalen. Du kan inkludera NuGet-paketet i ditt projekt och ange Application Insights **iKey** värdet i din webbprograminställningarna för att aktivera profileraren.

Om du följer aktivering arbetsflödet för [insikter Profiler för Windows](./app-insights-profiler.md) och välj **aktivera** i den **konfigurera Profiler** rutan ett felmeddelande. Åtgärden Aktivera försöker installera Windows-versionen av agenten Profiler på Linux-miljö.

Vi arbetar på en lösning på problemet.

![Försök inte att återaktivera profileraren i fönstret prestanda](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Nästa steg
Om du använder anpassade behållare som hanteras av Azure App Service, följ instruktionerna i [ aktivera Service Profiler för ett av ASP.NET Core program](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) att aktivera Application Insights Profiler.

Rapportera eventuella problem eller förslag till Application Insights GitHub-lagringsplatsen: [ApplicationInsights-Profiler-AspNetCore: problem med](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
