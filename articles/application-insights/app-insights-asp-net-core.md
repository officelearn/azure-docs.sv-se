---
title: Azure Application Insights för ASP.NET Core | Microsoft Docs
description: Övervaka webbprogram för tillgänglighet, prestanda och användning.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: a0e0d2c3604ea2357f52c3d464933c622ba1810d
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523248"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights för ASP.NET Core

Azure Application Insights erbjuder djupgående övervakning av webbprogrammet kod-nivå. Du kan enkelt övervaka ditt webbprogram för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem.

Den här artikeln vägleder dig genom stegen för hur du skapar en ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) program i Visual Studio. Den också visar hur du startar övervakningen med hjälp av Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- .NET core 2.0.0 SDK eller senare
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7.3 eller senare med arbetsbelastningen ASP.NET och utveckling

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Skapa ett ASP.NET Core-projekt i Visual Studio

1. Högerklicka på **Visual Studio 2017**, och välj sedan **kör som administratör**.
2. Välj **filen** > **nya** > **projekt** (Ctrl + Skift + N).

   ![Skärmbild av Visual Studio-projekt-menyn](./media/app-insights-asp-net-core/001-new-project.png)

3. Utöka **Visual C#**. Välj **.NET Core** > **ASP.NET Core-Webbapp**. Ange ett projektnamn och ett lösningsnamn och välj sedan **Skapa ny Git-lagringsplats**.

   ![Skärmbild av Visual Studio nytt projekt](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Välj **.NET Core** > **ASP.NET Core 2.0** **webbprogram** > **OK**.

    ![Skärmbild av Visual Studio nytt projekt Välj mall](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights-sökning

I Visual Studio 2015 Update 2 eller senare med ett ASP.NET Core 2 + baserat-projekt, kan du dra nytta av [Application Insights Search](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)redan innan du uttryckligen lägga till Application Insights i projektet.

Att testa den här funktionen:

1. Kör din app. Om du vill köra din app, Välj den **IIS Express** ikonen (![Skärmbild av Visual Studio IIS Express ikonen](./media/app-insights-asp-net-core/004-iis-express.png)).

2. Välj **visa** > **andra Windows** > **Application Insights Search**.

   ![Skärmbild av Visual Studio diagnostiska verktyg för val av](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Debug session telemetry är för närvarande endast tillgängligt för lokala analysis. Aktivera Application Insights fullständigt **telemetri beredskap** i det övre högra hörnet eller fullständig stegen i nästa avsnitt.

   ![Skärmbild av Visual Studio Application Insights Sök](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Mer information om hur Visual Studio ljus uppe funktioner vi [Application Insights Search](app-insights-visual-studio.md) och [CodeLens](app-insights-visual-studio-codelens.md) lokalt innan du lägger till Application Insights i projektet ASP.NET Core, se [ Application Insights Search fortsatt](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>lägg till Application Insights Telemetry

1. Välj **projekt** > **Lägg till Application Insights Telemetry**. (Eller högerklickar du på **Connected Services**, och välj sedan **Lägg till Connected Service**.)

    ![Skärmbild av Visual Studio-valmenyn för nya projekt](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Välj **börjar**. (Beroende på din version av Visual Studio kan texten kan variera något. Vissa tidigare versioner har en **starta kostnadsfri** knappen i stället.)

    ![Skärmbild av Application Insights Kom igång-knappen](./media/app-insights-asp-net-core/008-get-started.png)

3. Välj din prenumeration och välj sedan **Resource** > **registrera**.

## <a name="changes-made-to-your-project"></a>Ändringar som gjorts i ditt projekt

Application Insights är låg belastning. Granska de ändringar som gjorts i projektet genom att lägga till Application Insights telemetry:

Välj **visa** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projekt** > **ändringar**

- Ändringar av fyra totalt visas:

  ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- En ny fil skapas:

  -  _ConnectedService.json_

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "8.12.10405.1",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```

- Tre filer ändras (ytterligare kommentarer tillagda för att visa ändringar):

  - _appSettings.JSON_:

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.CS_:

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="synthetic-transactions-with-powershell"></a>Syntetiska transaktioner med PowerShell

Automatisera begäranden till din app med hjälp av syntetiska transaktioner:

1. Om du vill köra din app, Välj den ![Skärmbild av Visual Studio IIS Express-ikonen](./media/app-insights-asp-net-core/004-iis-express.png) ikonen.

2. Kopiera URL: en från webbläsarens adressfält. URL: en är i formatet `http://localhost:<port number>`.

   ![Skärmbild av webbläsaren URL i adressfältet](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Kör följande PowerShell-loop för att skapa 100 syntetiska transaktioner med hjälp av testapp. Ändra portnumret efter `localhost:` så att den matchar den URl som du kopierade i föregående steg. Exempel:

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Öppna Application Insights-portalen

Öppna Application Insights att visa transaktioner och för att bekräfta att data som samlas in när du har kört PowerShell-kommandon i föregående avsnitt. 

I Visual Studio-menyn väljer du **projekt** > **Programinsikter** > **öppna Application Insights-portalen**.

   ![Skärmbild av Application Insights, översikt](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> I den föregående exemplet på skärmbilden, **Live Stream**, **inläsningstid för sidvisning**, och **misslyckade begäranden** inte samlas in. Nästa avsnitt vägleder dig genom stegen för att lägga till var och en av dessa. Om du redan samlar in **Live Stream** och **inläsningstid för sidvisning**, Slutför steg endast för **misslyckade begäranden**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Samla in misslyckade förfrågningar, Live Stream och inläsningstid för Sidvisning

### <a name="failed-requests"></a>Misslyckade begäranden

Tekniskt sett misslyckade förfrågningar samlas in, men inga misslyckade förfrågningar har inträffat ännu. Du kan lägga till en anpassad undantag till befintligt projekt för att simulera en verklig undantag så att processen går snabbare. Om din app körs fortfarande i Visual Studio innan du fortsätter, Välj **stoppa felsökning** (SKIFT + F5).

1. I **Solution Explorer**, expandera **sidor** > **About.cshtml**, och öppna sedan *About.cshtml.cs*.

   ![Skärmbild av Visual Studio Solution Explorer](./media/app-insights-asp-net-core/011-about.png)

2. Lägga till ett undantag under ``Message=``, och sedan spara ändringarna i filen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Direktsänd ström

Uppdatera Microsoft.ApplicationInsights.AspNetCore 2.2.0 NuGet-paket för att komma åt Live Stream-funktionerna i Application Insights med ASP.NET Core.

I Visual Studio väljer **projekt** > **hantera NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore** > (version) **2.2.0** > **uppdatering**.

  ![Skärmbild av NuGet-Pakethanteraren](./media/app-insights-asp-net-core/012-nuget-update.png)

Flera bekräftelsemeddelanden visas. Läs och acceptera om du godkänner ändringarna.

### <a name="page-view-load-time"></a>Inläsningstid för Sidvisning

1. I Visual Studio går du till **Solution Explorer** > **sidor**. Du måste ändra två filer: *Layout.cshtml* och *ViewImports.cshtml*.

2. I *ViewImports.cshtml*, lägga till den här koden:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. I *Layout.cshtml*, Lägg till följande kod innan den ``</head>`` taggen och före eventuella andra skript:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Testa misslyckade förfrågningar, inläsningstid för sidvisning, och Live Stream

Att testa och kontrollera att allt fungerar:

1. Kör din app. Om du vill köra din app, Välj den ![Skärmbild av Visual Studio IIS Express-ikonen](./media/app-insights-asp-net-core/004-iis-express.png) ikonen.

2. Gå till den **om** sidan för att utlösa test-undantag. (Om du är i felsökningsläge i Visual Studio väljer **Fortsätt** för undantaget visas i Application Insights.)

3. Kör simulerade PowerShell transaction-skript som du använde tidigare. (Du kan behöva justera portnumret i skriptet.)

4. Om den **översikt** sidan i program Insights fortfarande inte är öppen i Visual Studio-menyn och välj **projekt** > **Programinsikter**  >  **Öppna Application Insights-portalen**. 

   > [!TIP]
   > Om du inte ser din nya trafik, kontrollerar du värdet för **tidsintervall**, och välj sedan **uppdatera**.

   ![Skärmbild av fönstret för översikt](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Välj **Live Stream**.

   ![Skärmbild av Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Om PowerShell-skript körs, bör du se livemått. Om din PowerShell-skript har avbrutits, kör skriptet igen med öppna Live Stream.)

## <a name="application-insights-sdk-comparison"></a>Application Insights SDK jämförelse

Application Insights-produktgruppen har arbetat hårt för att uppnå funktionsparitet mellan den [fullständiga .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) och .NET Core SDK. 2.2.0 versionen av den [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) för Application Insights i stort sett stänger skillnaderna.

I följande tabell beskriver flera av skillnaderna och avvägningarna mellan [.NET och .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | SDK-jämförelse | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Livemått**      | **+** |**-** | **+** |
   | **Server telemetri kanal** | **+** |**-** | **+**|
   |**Adaptiv sampling**| **+** | **-** | **+**|
   | **SQL-beroendeanrop**     | **+** |**-** | **+**|
   | **Prestandaräknare*** | **+** | **-**| **-**|

Prestandaräknare i den här kontexten avser [serversidan prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) som processor, minne och diskanvändning.

## <a name="open-source-sdk"></a>Öppen källkod-SDK
[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Application Insights Search fortsatt

Det här avsnittet kan hjälpa dig att bättre förstå hur Application Insights Search fungerar i Visual Studio för ett ASP.NET Core 2-projekt. Det fungerar det här sättet även om du inte uttryckligen har installerat Application Insights NuGet-paket ännu. Det kan också vara bra att granska utdata för felsökning.

Om du söker utdata för ordet _insight_, markeras resultat som liknar följande:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Läser in två sammansättningar i utdata, CoreCLR: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Den _okonfigurerad_ referens i varje instans av Application Insights telemetry indikerar att det här programmet är inte kopplad till en ikey. De data som genereras när din app körs skickas inte till Azure. Data är endast tillgängligt för lokal sökning och analys.

Funktionen är möjligt delvis eftersom NuGet-paketet _Microsoft.AspNetCore.All_ tar [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_ ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) som ett beroende.

![Skärmbild av NuGet beroendediagram för Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

Utanför Visual Studio om du redigerade ett ASP.NET Core-projekt i VSCode eller någon annan redigerare skulle dessa sammansättningar läsa in under felsökning om du inte uttryckligen lagt till Application Insights i projektet.

I Visual Studio utförs men den här belysning in av Application Insights-funktioner i lokalt från externa sammansättningar med hjälp av den [IHostingStartup gränssnittet](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). Gränssnittet dynamiskt lägger till Application Insights under felsökning.

Mer information om att utöka en app från en [externa sammansättningen i ASP.NET Core med IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Inaktivera Application Insights i Visual Studio .NET Core-projekt

Även om automatisk light-start av Application Insights söker kan funktioner vara användbart, ser debug telemetri som genereras när du inte hade väntat det kan vara förvirrande.

Om bara inaktivera telemetri generation är tillräcklig kan du lägga till det här kodblocket till den **konfigurera** -metoden för din _Startup.cs_ fil:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR läser fortfarande in _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ och _Microsoft.ApplicationInsights.AspNetCore.dll_, men filerna inte göra något.

Om du vill inaktivera helt Application Insights i Visual Studio .NET Core-projektet, den bästa metoden är att välja **verktyg** > **alternativ**  >   **Produkter och lösningar** > **Web projekt**. Välj den **inaktivera lokala Application Insights för ASP.NET Core-webbprojekt** markerar du kryssrutan. Den här funktionen har lagts till i Visual Studio 15,6.

![Skärmbild av Visual Studio alternativ fönstret Web-projekt](./media/app-insights-asp-net-core/014-disable.png)

Om du kör en tidigare version av Visual Studio och du vill ta bort alla sammansättningar som läses in via *IHostingStartup*, har du två alternativ:

* Lägg till `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` till _Program.cs_:

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* Lägg till ``"ASPNETCORE_preventHostingStartup": "True"`` till _launchSettings.json_ miljövariabler.

Problem med att använda någon av dessa metoder är att de inte inaktivera endast Application Insights. Metoderna som också inaktivera allt i Visual Studio som använde den *IHostingStartup* ljus upp funktioner.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Nästa steg
* [Utforska Användarflöden](app-insights-usage-flows.md) att förstå hur användarna navigerar i din app.
* [Konfigurera insamling av ögonblicksbild](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) att se status för källkoden och variabler för tillfället ett undantagsfel.
* [Använda API: et](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
* Använd [tillgänglighetstester](app-insights-monitor-web-app-availability.md) att kontrollera appens hela tiden från hela världen.
