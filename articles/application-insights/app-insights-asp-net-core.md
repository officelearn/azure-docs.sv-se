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
ms.openlocfilehash: 6635906b6aae418fa3754f1152fae3e0d8903ffc
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989776"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights för ASP.NET Core

Azure Application Insights erbjuder djupgående övervakning av webbprogrammet kod-nivå. Du kan enkelt övervaka ditt webbprogram för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem.

Den här artikeln vägleder dig genom hur du skapar en ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) program i Visual Studio och hur du startar övervakning med Azure Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- NET Core 2.0.0 SDK eller senare.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7.3 eller senare med arbetsbelastningen ASP.NET och utveckling. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Skapa ett ASP.NET Core-projekt i Visual Studio

1. Högerklicka på och starta **Visual Studio 2017** som administratör.
2. Välj **filen** > **nya** > **projekt** (Ctrl-Shift-N).

   ![Skärmbild av nytt projekt-menyn i Visual Studio-fil](./media/app-insights-asp-net-core/001-new-project.png)

3. Expandera **Visual C#** > Välj **.NET Core** > **ASP.NET Core-Webbapp**. Ange en **namn** > **lösningsnamn** > Kontrollera **Skapa ny Git-lagringsplats**.

   ![Skärmbild av nytt projekt för Visual Studio-fil](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Välj **.Net Core** > **ASP.NET Core 2.0** **webbprogram** > **OK**.

    ![Skärmbild av Visual Studio nytt projekt val av filmenyn](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Söka med Application Insights

Som standard i Visual Studio version 2015 Update 2 eller högre med en ASP.NET Core 2 + baserat projekt som du kan dra nytta av [söka med Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) till och med innan du uttryckligen lägga till Application Insights i projektet.

Att testa den här funktionen:

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio IIS Express-ikonen](./media/app-insights-asp-net-core/004-iis-express.png)

2. Välj **visa** > **andra Windows** > **söka med Application Insights**.

   ![Skärmbild av diagnostiska verktyg för Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Debug session telemetry är för närvarande tillgängligt för lokala analysis. Fullständigt aktivera Application Insights **telemetri beredskap** uppe till höger eller följer du stegen i nästa avsnitt.

   ![Skärmbild av Visual Studio Application Insights Sök](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Mer information om hur Visual Studio ljus uppe funktioner vi [Application Insights Search](app-insights-visual-studio.md) och [CodeLens](app-insights-visual-studio-codelens.md) lokalt innan du har lagt till Application Insights i projektet ASP.NET Core ta en titt på förklaring på den [slutet av den här artikeln.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>lägg till Application Insights Telemetry

1. Välj **projekt** > **Lägg till Application Insights Telemetry...** . (Eller högerklickar du på **Connected Services** och välj Lägg till Connected Service.)

    ![Skärmbild av Visual Studio nytt projekt val av filmenyn](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Välj **börjar**. (Beroende på din version av Visual Studio texten kan variera något. Vissa äldre versioner måste istället en **starta kostnadsfri** knappen.)

    ![Skärmbild av Visual Studio nytt projekt val av filmenyn](./media/app-insights-asp-net-core/008-get-started.png)

3. Välj en lämplig **prenumeration** > **Resource** > **registrera**.

## <a name="changes-made-to-your-project"></a>Ändras Made till ditt projekt

Application Insights är låg belastning. Granska de ändringar som gjorts i projektet genom att lägga till Application Insights telemetry:

Välj **visa** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projekt** > **ändringar**

- Totalt antal fyra ändringar:

  ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- En ny fil skapas:

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Tre filer ändras: (ytterligare kommentarer tillagda för att visa ändringar)

  _appsettings.json_

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

  _ContosoDotNetCore.csproj_

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

   _Program.CS_

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

Automatisera förfrågningar mot din app med syntetiska transaktioner.

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio IIS Express-ikonen](./media/app-insights-asp-net-core/004-iis-express.png)

2. Kopiera URL: en från webbläsarens adressfält. Det är i formatet `http://localhost:{random port number}`

   ![Skärmbild av webbläsarens adressfält för URL: en](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Kör följande PowerShell-loop för att skapa 100 syntetiska transaktioner mot prov-app. Ändra portnumret efter **localhost:** att matcha den url som du kopierade i föregående steg.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Öppna Application Insights-portalen

Starta Application Insights för att visa transaktioner och bekräfta att data som samlas in när du har kört PowerShell i föregående avsnitt. 

Visual Studio-menyn väljer **projekt** > **Programinsikter** > **öppna Application Insights-portalen**

   ![Skärmbild av Application Insights, översikt](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> I exemplet på skärmbilden ovan **Live Stream**, **inläsningstid för sidvisning**, och **misslyckade begäranden** har för närvarande inte samlats in. Nästa avsnitt beskriver att lägga till var och en. Om du redan kan samla in **Live Stream**, och **inläsningstid för sidvisning**, Följ stegen för att endast **misslyckade begäranden**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Det gick inte att samla in begäranden, Live Stream och inläsningstid för Sidvisning

### <a name="failed-requests"></a>Misslyckade begäranden

Tekniskt sett **misslyckade begäranden** samlas in, men ingen har inträffat ännu. Om du vill påskynda processen längs en anpassade undantag kan läggas till befintligt projekt för att simulera en verklig undantag. Om din app fortfarande körs i Visual Studio innan du fortsätter **stoppa felsökning** (SKIFT + F5)

1. I **Solution Explorer** > Expandera **sidor** > **About.cshtml** > Öppna **About.cshtml.cs**.

   ![Skärmbild av Visual Studio Solution Explorer](./media/app-insights-asp-net-core/011-about.png)

2. Lägga till ett undantag under ``Message=`` och spara ändringarna i filen.

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

Tillgång till Live Stream funktioner för Application Insights med ASP.NET Core-uppdatering till den **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet-paket.

Från Visual Studio, Välj **projekt** > **hantera NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore** > Version **2.2.0** > **uppdatering**.

  ![Skärmbild av NuGet-Pakethanteraren](./media/app-insights-asp-net-core/012-nuget-update.png)

Flera bekräftelsemeddelanden inträffar. Läs och acceptera om du godkänner ändringarna.

### <a name="page-view-load-time"></a>Inläsningstid för Sidvisning

1. I Visual Studio går du till **Solution Explorer** > **sidor** > två filer kommer att behöva ändras: _Layout.cshtml_, och  _ViewImports.cshtml_

2. I __ViewImports.cshtml_, lägga till:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. I **_Layout.cshtml** Lägg till rad nedan innan den ``</head>`` tagg, utan även före eventuella andra skript.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Det gick inte att testa begäranden, inläsningstid för sidvisning, Live Stream

Att testa och kontrollera att allt fungerar:

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio IIS Express-ikonen](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Navigera till den **om** sidan för att utlösa test-undantag. (Om du kör i felsökningsläge, måste du klicka på **Fortsätt** i Visual Studio efter undantag ska visas i Application Insights.)

3. Kör skriptet simulerade PowerShell transaktion från tidigare (du kan behöva justera portnumret i skriptet.)

4. Om program insikter översikt inte är fortfarande öppen, Visual Studio-menyn väljer **projekt** > **Programinsikter** > **öppna program Insights-portalen**. 

   > [!TIP]
   > Om du inte ser din nya trafik ännu, kontrollera den **tidsintervall** och klicka på **uppdatera**.

   ![Skärmbild av översiktsfönstret](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Välj Live Stream

   ![Skärmbild av Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Om din PowerShell som skriptet körs fortfarande du bör se livemått, om den är stoppad kör skriptet igen med Live Stream öppna.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK jämförelse

Application Insights-produktgruppen har arbetat hårt för att uppnå funktionsparitet mellan den [fullständiga .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) och .net Core SDK. 2.2.0 versionen av den [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) för Application Insights i stort sett har stängt skillnaderna.

Vill veta mer om skillnader och rätt balans mellan [.NET och .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | SDK-jämförelse | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live Metrics**      | **+** |**-** | **+** |
   | **Server telemetri kanal** | **+** |**-** | **+**|
   |**Adaptiv Sampling**| **+** | **-** | **+**|
   | **SQL-Beroendeanrop**     | **+** |**-** | **+**|
   | **Prestandaräknare*** | **+** | **-**| **-**|

_Prestandaräknare_ refererar till i den här kontexten [serversidan prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) som processor, minne och diskanvändning.

## <a name="open-source-sdk"></a>Öppen källkod-SDK
[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights search fortsatt

För att bättre förstå hur Application Insights search fungerar i Visual Studio för ett ASP.NET Core 2-projekt även om en explicit installation av Application Insights NuGet-paket inte har utförts än. Det kan vara bra att granska Debug-utdata.

Om du söker utdata för ordet _insight_ den att fokusera på resultat som liknar följande:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR är två sammansättningar som läses in: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Och _okonfigurerad_ i varje instans av Application Insights telemetry indikerar att det här programmet är inte kopplad till en ikey så att de data som genereras när din app körs inte som skickas till Azure och är endast tillgängligt för lokal sökning och analys.

En del av hur du kan göra är att NuGet-paketet _Microsoft.AspNetCore.All_ tar som ett beroende [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Skärmbild av NuGet beroende Graph för Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

Utanför Visual Studio om du redigerade en ASP.NET Core-projektet i VSCode eller några andra redigerare skulle dessa sammansättningar läsa in under felsökning om du inte uttryckligen lagt till Application Insights i projektet.

I Visual Studio sker dock den här belysning in av Application Insights-funktioner i lokalt från externa sammansättningar med hjälp av den [IHostingStartup gränssnittet](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) som dynamiskt lägger till Application Insights under felsökning.

Mer information om hur du förbättrar en app från en [externa sammansättningen i ASP.NET Core med IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Så här inaktiverar du Application Insights i Visual Studio .NET Core-projekt

Automatisk lyser av sökfunktioner för Application Insights kan vara praktiskt att vissa, kan se debug telemetri som genereras när du inte hade väntat den vara förvirrande.

Om du bara inaktivera telemetri generation räcker du kan lägga till den här kodblocket metoden konfigurera i din _Startup.cs_ fil:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

På CoreCLR laddas fortfarande _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ och _Microsoft.ApplicationInsights.AspNetCore.dll_, men de kommer inte göra något.

Om du vill inaktivera helt Application Insights i Visual Studio .NET Core-projektet, den bästa metoden är att välja **verktyg** > **alternativ**  >   **Produkter och lösningar** > **webbprojekt** > och markera kryssrutan om du vill inaktivera den lokala Application Insights för ASP.NET Core web-projekt. Den här funktionen har lagts till i Visual Studio 15,6.

![Skärmbild av Visual Studio alternativ fönstret Web-projekt](./media/app-insights-asp-net-core/014-disable.png)

Om du kör en tidigare version av Visual Studio och du vill ta bort alla sammansättningar som läses in via IHostingStartup som du kan antingen lägga till:

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

att _Program.cs_:

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

Eller också kan du lägga till ``"ASPNETCORE_preventHostingStartup": "True"`` till _launchSettings.json_ miljövariabler.

Problem med att använda någon av dessa metoder är det inte bara inaktivera Application Insights inaktiverar allt i Visual Studio som har att utnyttja IHostingStartup lyser funktioner.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Nästa steg
* [Utforska användare flödar](app-insights-usage-flows.md) att förstå hur användarna navigerar i din app.
* [Konfigurera insamling av ögonblicksbild](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) att se status för källkoden och variabler för tillfället ett undantagsfel.
* [Använda API: et](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md) Kontrollera appens hela tiden från hela världen.
