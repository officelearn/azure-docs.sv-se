---
title: Profil ASP.NET Azure Linux-webbappar för kärna med Application Insights Profiler | Microsoft-dokument
description: En begreppsmässig översikt och steg-för-steg-självstudie om hur du använder Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d845e245a242a88d16a2597f0144a0ae4a727cb0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640977"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET Azure Linux-webbappar för grundläggande kärna med Profiler för Application Insights

Den här funktionen är för närvarande en förhandsversion.

Ta reda på hur mycket tid som spenderas i varje metod i ditt live-webbprogram när du använder [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler är nu tillgänglig för ASP.NET Core-webbappar som finns i Linux på Azure App Service. Den här guiden innehåller steg-för-steg-instruktioner om hur Profiler-spårningarna kan samlas in för ASP.NET Core Linux-webbappar.

När du har slutfört den här genomgången kan appen samla in Profiler-spårningar som de spår som visas i bilden. I det här exemplet anger spårningen Profiler att en viss webbbegäran är långsam på grund av väntetiden. Den *heta sökvägen* i koden som saktar ner appen markeras med en flamikon. **Metoden Om** i avsnittet **HomeController** saktar ned webbappen eftersom metoden anropar funktionen **Thread.Sleep.**

![Profilerspår](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Krav
Följande instruktioner gäller för alla utvecklingsmiljöer för Windows, Linux och Mac:

* Installera [.NET Core SDK 2.1.2 eller senare](https://dotnet.microsoft.com/download/archives).
* Installera Git genom att följa instruktionerna på [Komma igång - Installera Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Ställ in projektet lokalt

1. Öppna ett kommandotolksfönster på datorn. Följande instruktioner fungerar för alla utvecklingsmiljöer för Windows, Linux och Mac.

1. Skapa ett ASP.NET Core MVC-webbprogram:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Ändra arbetskatalogen till rotmappen för projektet.

1. Lägg till NuGet-paketet för att samla in Profiler-spårningarna:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Aktivera programinsikter i Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Aktivera Profiler i Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Lägg till en kodrad i **avsnittet HomeController.cs** för att slumpmässigt fördröja några sekunder:

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

1. Spara och genomför ändringarna i den lokala databasen:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Skapa Linux-webbappen som värd för ditt projekt

1. Skapa webbappmiljön med hjälp av App Service på Linux:

    ![Skapa Linux-webbappen](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Skapa distributionsuppgifterna:

    > [!NOTE]
    > Registrera ditt lösenord som du kan använda senare när du distribuerar webbappen.

    ![Skapa distributionsautentiseringsuppgifter](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Välj distributionsalternativ. Konfigurera en lokal Git-databas i webbappen genom att följa instruktionerna på Azure-portalen. En Git-databas skapas automatiskt.

    ![Konfigurera Git-databasen](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Fler distributionsalternativ finns i [den här artikeln](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Distribuera projektet

1. Bläddra till rotmappen för projektet i kommandotolksfönstret. Lägg till en Git-fjärrdatabas för att peka på databasen i App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Använd **användarnamnet** som du använde för att skapa distributionsautentiseringsuppgifterna.
    * Använd **appnamnet** som du använde för att skapa webbappen med hjälp av App Service på Linux.

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Lägga till programstatistik för att övervaka dina webbappar

1. [Skapa en application insights-resurs](./../../azure-monitor/app/create-new-resource.md ).

2. Kopiera **iKey-värdet** för application insights-resursen och ange följande inställningar i webbapparna:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    När appinställningarna ändras startas webbplatsen automatiskt om. När de nya inställningarna har tillämpats körs Profiler omedelbart i två minuter. Profiler körs sedan i två minuter varje timme.

3. Generera lite trafik till din webbplats. Du kan generera trafik genom att uppdatera webbplatsen **Om** sidan några gånger.

4. Vänta två till fem minuter innan händelserna kan aggregeras till Application Insights.

5. Bläddra till **fönstret** Prestanda för programstatistik i Azure-portalen. Du kan visa Profiler-spårningarna längst ned till höger i fönstret.

    ![Visa profilerspårningar](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Nästa steg
Om du använder anpassade behållare som finns hos Azure App Service följer du instruktionerna i [Aktivera serviceprofiler för ett ASP.NET Core-program](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) för att aktivera Application Insights Profiler.

Rapportera eventuella problem eller förslag till Application Insights GitHub-databasen: [ApplicationInsights-Profiler-AspNetCore: Problem](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
