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
ms.topic: article
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 459fbbe734577af474fae8de3acf9be732780d6c
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736542"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights för ASP.NET Core

Azure Application Insights ger detaljerad övervakning av webbprogrammet nivån i koden. Du kan enkelt kan övervaka ditt webbprogram för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem.

Den här artikeln guidar dig igenom hur du skapar en ASP.NET Core [Razor sidor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) program i Visual Studio och hur du börja övervaka med Azure Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- NET Core 2.0.0 SDK eller senare.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7.3 eller senare med ASP.NET och web development arbetsbelastningen. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Skapa ett ASP.NET Core-projekt i Visual Studio

1. Högerklicka på och starta **Visual Studio 2017** som administratör.
2. Välj **filen** > **nya** > **projekt** (Ctrl-Skift-N).

   ![Skärmbild av nya projekt-menyn i Visual Studio-fil](./media/app-insights-asp-net-core/001-new-project.png)

3. Expandera **Visual C#** > Välj **.NET Core** > **webbprogram för ASP.NET Core**. Ange en **namn** > **lösningsnamn** > Kontrollera **Skapa ny Git-lagringsplats**.

   ![Skärmbild av Visual Studio filen guiden](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Välj **.Net Core** > **ASP.NET Core 2.0** **webbprogrammet** > **OK**.

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Söka med Application Insights

Som standard i Visual Studio version 2015 Update 2 eller senare med en ASP.NET-Core 2 + baserat projekt som du kan dra nytta av [Programinsikter söka](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio) även innan du uttryckligen lägga till Application Insights i ditt projekt.

Att testa den här funktionen:

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio Express i IIS-ikon](./media/app-insights-asp-net-core/004-iis-express.png)

2. Välj **visa** > **andra fönster** > **Programinsikter söka**.

   ![Skärmbild av diagnostiska verktyg för Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Telemetri för debug-session är tillgänglig för lokala analys. Välj för att aktivera Application Insights fullständigt **telemetri beredskap** i övre Högerklicka eller följer du stegen i nästa avsnitt.

   ![Skärmbild av Visual Studio Application Insights Sök](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Läs mer om att Visual Studio ljus in funktioner som [Application Insights Sök](app-insights-visual-studio.md) och [CodeLens](app-insights-visual-studio-codelens.md) lokalt innan du har lagt till Application Insights i projektet ASP.NET Core ta en titt på förklaring på den [slutet av den här artikeln.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>lägg till Application Insights Telemetry

1. Välj **projekt** > **Lägg till Application Insights Telemetry...** . (Högerklicka **anslutna tjänster** och välj Lägg till ansluten tjänst.)

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Välj **Kom igång**. (Beroende på din version av Visual Studio texten kan variera något. Vissa äldre versioner i stället har en **starta ledigt** knappen.)

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/008-get-started.png)

3. Välj en lämplig **prenumeration** > **resurs** > **registrera**.

## <a name="changes-made-to-your-project"></a>Ändrar Made till ditt projekt

Application Insights är låg belastning. Granska de ändringar som gjorts i projektet genom att lägga till Application Insights telemetri:

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

- Tre filer ändras: (ytterligare kommentarer till Markera ändringar)

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

Automatisera förfrågningar mot appen med syntetiska transaktioner.

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio Express i IIS-ikon](./media/app-insights-asp-net-core/004-iis-express.png)

2. Kopiera URL-adressen från adressfältet i webbläsaren. Det är i formatet http://localhost:{random portnummer}

   ![Skärmbild av adressfältet i webbläsaren url](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Kör följande PowerShell-slinga för att skapa 100 syntetiska transaktioner mot testa appen. Ändra portnumret efter **localhost:** så att den matchar den url som du kopierade i föregående steg.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Öppna Application Insights-portalen

Starta Application Insights för att visa transaktioner och bekräfta att data som samlas in när du har kört PowerShell från föregående avsnitt. 

Visual Studio-menyn väljer du **projekt** > **Programinsikter** > **öppna Application Insights-portalen**

   ![Skärmbild av Application Insights-översikt](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> I skärmbilden ovan **Liveströmning**, **inläsningstid för sidvisning**, och **, misslyckade begäranden** har för närvarande inte samlats in. I nästa avsnitt vägleder genom att lägga till varje. Om du redan samlar **Liveströmning**, och **inläsningstid för sidvisning**, Följ stegen för att endast **, misslyckade begäranden**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Samla in misslyckade begäranden, direktsänd dataström och vyn sida inläsningstid

### <a name="failed-requests"></a>Misslyckade begäranden

Tekniskt **, misslyckade begäranden** samlas in, men inget har genomförts ännu. För att snabbare längs anpassade undantag kan läggas till befintliga projektet för att simulera en verkliga undantag. Om din app fortfarande körs i Visual Studio innan du fortsätter **stoppa felsökning** (SKIFT + F5)

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

Åtkomst till direktsänd dataström med funktionerna i Application Insights med ASP.NET Core update så att den **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet-paket.

I Visual Studio väljer **projekt** > **hantera NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore** > Version **2.2.0** > **uppdatering**.

  ![Skärmbild av NuGet Package Manager](./media/app-insights-asp-net-core/012-nuget-update.png)

Konfigurera flera meddelanden kommer att ske. Läs och Godkänn om du samtycker till ändringarna.

### <a name="page-view-load-time"></a>Vyn sida inläsningstid

1. I Visual Studio navigerar du till **Solution Explorer** > **sidor** > två filer kommer att behöva ändras: _Layout.cshtml_, och  _ViewImports.cshtml_

2. I __ViewImports.cshtml_, lägga till:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. I **_Layout.cshtml** Lägg till rad under innan den ``</head>`` tagg, utan även före eventuella andra skript.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Det gick inte att testa begäranden, vyn sida inläsningstid, direktsänd dataström

Testa och bekräfta att allt fungerar som:

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio Express i IIS-ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Navigera till den **om** att utlösa test-undantag. (Om du kör i felsökningsläge, du måste klicka på **Fortsätt** i Visual Studio för undantaget visas i Application Insights.)

3. Kör skriptet simulerade PowerShell transaktion från tidigare (du kan behöva justera portnumret i skriptet.)

4. Om program insikter översikt inte är fortfarande öppen Visual Studio-menyn väljer **projekt** > **Programinsikter** > **öppna program Insights-portalen**. 

   > [!TIP]
   > Om du inte ser den nya trafiken ännu, kontrollera den **tidsintervallet** och på **uppdatera**.

   ![Skärmbild av översikt](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Välj en direktsänd dataström

   ![Skärmbild av Live mått dataström](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Om din PowerShell skriptet körs fortfarande du bör se live statistik, och om den har stoppats kör skriptet igen med direktsänd dataström öppna.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK jämförelse

Application Insights-produktgruppen har arbetat hårt för att få funktionsparitet mellan den [fullständig .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) och .net Core SDK. 2.2.0 versionen av den [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) för Application Insights i stort sett har stängts funktionen mellanrum.

Information om skillnaderna och kompromisser mellan [.NET och .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Jämförelse av SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live mått**      | **+** |**-** | **+** |
   | **Servern telemetri kanal** | **+** |**-** | **+**|
   |**Anpassningsbar provtagning**| **+** | **-** | **+**|
   | **SQL-Beroendeanrop**     | **+** |**-** | **+**|
   | **Prestandaräknare*** | **+** | **-**| **-**|

_Prestandaräknare_ refererar till i den här kontexten [serversidan prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) som processor, minne och diskanvändning.

## <a name="open-source-sdk"></a>Öppen källkod SDK
[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights Sök fortsatte

För att förstå hur Application Insights Sök fungerar i Visual Studio för ett projekt för ASP.NET Core 2 även när en explicit installation av Application Insights NuGet-paket inte har genomförts ännu. Det kan vara bra att granska Debug-utdata.

Om du söker utdata för ordet _insight_ markeras resultat som liknar följande:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR är två sammansättningar som lästes in: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Och _okonfigurerad_ i varje instans av Application Insights telemetri anger att det här programmet är inte kopplad till en ikey så att de data som genereras när appen körs inte som skickas till Azure och är endast tillgängligt för lokal sökning och analys.

En del av hur du kan göra detta är att NuGet-paketet _Microsoft.AspNetCore.All_ som ett beroende [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Skärmbild av NuGet beroende diagram för Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

Utanför Visual Studio om du har redigerat ett ASP.NET Core-projekt i VSCode eller några andra redigerare skulle dessa sammansättningar laddar automatiskt vid felsökning om du inte uttryckligen lagt till Application Insights i ditt projekt.

I Visual Studio sker dock den här ljus upp lokala Application Insights-funktioner från externa sammansättningar med hjälp av den [IHostingStartup gränssnittet](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) som dynamiskt lägger till Application Insights under felsökning.

Mer information om att utöka en app från en [extern sammansättningsresurs i ASP.NET Core med IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Så här inaktiverar du Application Insights i projekt i Visual Studio .NET Core

Automatisk bakgrund in av Application Insights sökfunktionen kan vara användbara för vissa, kan se debug telemetri genereras när du inte förväntar dig vara förvirrande.

Bara inaktivera telemetri generation är tillräcklig kan du lägga till den här kodblock att konfigurera metoden för din _Startup.cs_ fil:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Kommer fortfarande att läsa in CoreCLR _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ och _Microsoft.ApplicationInsights.AspNetCore.dll_, men de inte göra något.

Om du vill inaktivera helt Application Insights i ditt projekt i Visual Studio .NET Core det bästa sättet är att välja **verktyg** > **alternativ**  >   **Projekt och lösningar** > **webbprojekt** > och markera kryssrutan om du vill inaktivera lokala Programinsikter för webbprojekt i ASP.NET Core. Den här funktionen har lagts till i Visual Studio 15,6.

![Skärmbild av Visual Studio alternativ fönstret webbprojekt skärmen](./media/app-insights-asp-net-core/014-disable.png)

Om du kör en tidigare version av Visual Studio, och du vill ta bort alla sammansättningar som läses in via IHostingStartup kan du antingen lägga till:

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

Problemet med någon av dessa metoder är det inte bara inaktivera Application Insights inaktiverar någonting i Visual Studio som utnyttja IHostingStartup enstaka in-funktioner.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Nästa steg
* [Utforska användare flödar](app-insights-usage-flows.md) att förstå hur användarna navigera i din app.
* [Konfigurera ögonblicksbild samling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) att se status för källkoden och variabler för tillfället ett undantag.
* [Använd API](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md) Kontrollera din app hela tiden från hela världen.
