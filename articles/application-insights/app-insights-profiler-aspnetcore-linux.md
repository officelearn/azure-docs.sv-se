---
title: Profilera ASP.NET Core Azure Linux-webbappar med Application Insights Profiler | Microsoft Docs
description: En konceptuell översikt över och stegvisa självstudier om hur du använder Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 012a0ced774335b9528ebc1c970ac1e8e8c929be
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720663"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilera ASP.NET Core Azure Linux-webbappar med Application Insights Profiler

Den här funktionen är för närvarande en förhandsversion.

Ta reda på hur mycket tid på varje metod för ditt webbprogram när du använder [Application Insights](app-insights-overview.md). Application Insights Profiler är nu tillgängligt för ASP.NET Core web apps som finns i Linux på Azure App Service. Den här guiden innehåller stegvisa instruktioner om hur Profiler-spårningar samlas in för ASP.NET Core Linux web apps.

När du har slutfört den här genomgången kommer din app kan samla in Profiler-spårningar som spårningar som visas i bilden. I det här exemplet indikerar Profiler-spårning att en viss webbegäran går långsamt på grund av tid att vänta. Den *heta sökvägen* i koden som saktas appen markeras med en flammförebyggande ikon. Den **om** -metod i den **HomeController** avsnittet saktas webbappen eftersom metoden anropar den **Thread.Sleep** funktion.

![Profiler-spårningar](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Förutsättningar
Följande instruktioner gäller för alla miljöer för utveckling av Windows, Linux och Mac:

* Installera den [.NET Core SDK 2.1.2 eller senare](https://www.microsoft.com/net/download/windows/build).
* Installera Git genom att följa anvisningarna i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurera projektet lokalt

1. Öppna ett kommandotolksfönster på din dator. Följande instruktioner fungerar för alla miljöer för utveckling av Windows, Linux och Mac.

2. Skapa en ASP.NET Core MVC-webbprogram:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. Ändra arbetskatalogen till rotmappen för projektet.

4. Lägg till NuGet-paketet för att samla in Profiler-spårningar:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Lägg till en rad med kod i den **HomeController.cs** avsnitt för att slumpmässigt fördröjning på några sekunder:

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

6. Spara och genomföra ändringarna till den lokala lagringsplatsen:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Skapa Linux-webbapp som värd för ditt projekt

1. Skapa web app-miljö med hjälp av App Service i Linux:

    ![Skapa Linux-webbapp](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Skapa autentiseringsuppgifter för distribution:

    > [!NOTE]
    > Registrera ditt lösenord för senare användning när du distribuerar din webbapp.

    ![Skapa autentiseringsuppgifter för distribution](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Välj distributionsalternativ för. Konfigurera en lokal Git-lagringsplats i webbapp genom att följa anvisningarna på Azure portal. En Git-lagringsplats skapas automatiskt.

    ![Ställ in Git-lagringsplatsen](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Läs fler distributionsalternativ [i den här artikeln](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Distribuera projektet

1. I Kommandotolken, bläddrar du till rotmappen för ditt projekt. Lägg till en fjärransluten Git-lagringsplats så att den pekar till lagringsplatsen på App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Använd den **användarnamn** som användes för att skapa autentiseringsuppgifter för distribution.
    * Använd den **appnamn** som du skapar webbappen med hjälp av App Service i Linux.

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Lägg till Application Insights för att övervaka dina web apps

1. [Skapa en Application Insights-resurs](./app-insights-create-new-resource.md).

2. Kopiera den **iKey** värdet för Application Insights-resursen och konfigurera följande inställningar i web apps:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Konfigurera appinställningar](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    När appinställningar ändras, platsen startar om automatiskt. När de nya inställningarna tillämpas körs omedelbart Profiler för två minuter. Profiler körs sedan för varje timme med två minuter.

3. Generera en del trafik till din webbplats. Du kan generera trafik genom att uppdatera webbplatsen **om** sidan några gånger.

4. Vänta två till fem minuter innan händelser till sammanställda till Application Insights.

5. Bläddra till Application Insights **prestanda** fönstret i Azure-portalen. Du kan visa Profiler-spårningar längst ned till höger i fönstret.

    ![Visa Profiler-spårningar](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Kända problem

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>Aktivera åtgärden i fönstret Profiler konfiguration fungerar inte

> [!NOTE]
> Om du vara värd för din app med App Service på Linux, behöver du inte aktivera Profiler i den **prestanda** fönstret i Application Insights-portalen. Du kan inkludera i NuGet-paketet i ditt projekt och ställa in Application Insights **iKey** värdet i inställningarna för din web app att aktivera Profiler.

Om du följer aktivering av arbetsflödet för [Application Insights Profiler för Windows](./app-insights-profiler.md) och välj **aktivera** i den **konfigurera Profiler** fönstret du får ett felmeddelande. Åtgärden Aktivera försöker installera Windows-versionen av Profiler-agent på Linux-miljön.

Vi arbetar på en lösning på problemet.

![Försök inte att återaktivera Profiler i fönstret prestanda](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Nästa steg
Om du använder anpassade behållare som hanteras av Azure App Service, följer du anvisningarna i [ aktivera tjänsten Profiler för ett ASP.NET Core-program i behållare](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) att aktivera Application Insights Profiler.

Rapportera problem eller förslag till Application Insights GitHub-lagringsplatsen: [ApplicationInsights-Profiler-AspNetCore: problem med](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
