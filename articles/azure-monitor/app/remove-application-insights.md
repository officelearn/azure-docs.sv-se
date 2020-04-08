---
title: Ta bort programinsikter i Visual Studio - Azure Monitor
description: Ta bort Application Insights SDK för ASP.NET och ASP.NET Core i Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805110"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Så här tar du bort programinsikter i Visual Studio

Den här artikeln visar hur du tar bort ASP.NET och ASP.NET Core Application Insights SDK i Visual Studio.

Om du vill ta bort Application Insights måste du ta bort NuGet-paketen och referenserna från API:et i ditt program. Du kan avinstallera NuGet-paket med hjälp av Package Management Console eller Manage NuGet Solution i Visual Studio. I följande avsnitt visas två sätt att ta bort NuGet-paket och vad som automatiskt lades till i projektet. Var noga med att bekräfta de filer som lagts till och områden med i din egen kod där du gjorde samtal till API tas bort.

## <a name="uninstall-using-the-package-management-console"></a>Avinstallera med konsolen Pakethantering

# <a name="net"></a>[.NET](#tab/net)

1. Om du vill öppna konsolen Pakethantering väljer du Verktyg > NuGet Package Manager > Package Manager Console på den övre menyn.
     
    ![Klicka på Verktyg > NuGet Package Manager > Package Manager Console i den övre menyn](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Om spårningssamling är aktiverad måste du först avinstallera Microsoft.ApplicationInsights.TraceListener. Gå `Uninstall-package Microsoft.ApplicationInsights.TraceListener` sedan steget nedan för att ta bort Microsoft.ApplicationInsights.Web.

1. Ange följande kommando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    När du har angett kommandot avinstalleras Application Insights-paketet och alla dess beroenden från projektet.
    
    ![Ange kommando i konsolen](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Om du vill öppna konsolen Pakethantering väljer du Verktyg > NuGet Package Manager > Package Manager Console på den övre menyn.

    ![Klicka på Verktyg > NuGet Package Manager > Package Manager Console i den övre menyn](./media/remove-application-insights/package-manager.png)

1. Ange följande kommando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    När du har angett kommandot avinstalleras Application Insights-paketet och alla dess beroenden från projektet.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Avinstallera med visual studio NuGet-användargränssnittet

# <a name="net"></a>[.NET](#tab/net)

1. Högerklicka på **Lösning**  *i Lösningsutforskaren* till höger och välj **Hantera NuGet-paket för lösning**.

    Du ser sedan en skärm som låter dig redigera alla NuGet-paket som ingår i projektet.
    
     ![Högerklicka på Lösning i Lösningsutforskaren och välj sedan Hantera NuGet-paket för lösning](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Om spårningssamling är aktiverad måste du först avinstallera Microsoft.ApplicationInsights.TraceListener utan att ta bort markerade beroenden och följ sedan stegen nedan för att avinstallera Microsoft.ApplicationInsights.Web med ta bort beroenden markerade.
    
1. Klicka på paketet "Microsoft.ApplicationInsights.Web".Markera kryssrutan bredvid *Project* till höger för att markera alla projekt.
    
1. Om du vill ta bort alla beroenden när du avinstallerar väljer du knappen **Alternativ** under det avsnitt där du valde projektet.

    Markera kryssrutan bredvid *Ta bort beroenden*under *Avinstallationsalternativ.*

1. Välj **Avinstallera**.
    
    ![Kontrollera ta bort beroenden och avinstallera sedan](./media/remove-application-insights/uninstall-framework.png)

    En dialogruta visar alla beroenden som ska tas bort från programmet.Välj **ok** för att avinstallera.
    
    ![Kontrollera ta bort beroenden och avinstallera sedan](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  När allt har avinstallerats kan du fortfarande se "ApplicationInsights.config" och "AiHandleErrorAttribute.cs" i *Solution Explorer*.Du kan ta bort de två filerna manuellt.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Högerklicka på **Lösning**  *i Lösningsutforskaren* till höger och välj **Hantera NuGet-paket för lösning**.

    Du ser sedan en skärm som låter dig redigera alla NuGet-paket som ingår i projektet.

    ![Högerklicka på Lösning i Lösningsutforskaren och välj sedan Hantera NuGet-paket för lösning](./media/remove-application-insights/manage-nuget-core.png)

1. Klicka på paketet "Microsoft.ApplicationInsights.AspNetCore". Markera kryssrutan bredvid *Project* till höger för att markera alla projekt och välj sedan **Avinstallera**.

    ![Kontrollera ta bort beroenden och avinstallera sedan](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Vad skapas när du lägger till Application Insights

När du lägger till Application Insights i projektet skapas filer och kod läggs till i vissa av dina filer. Enbart avinstallera NuGet Paket kommer inte alltid att kasta filer och kod. Om du vill ta bort Application Insights helt bör du kontrollera och manuellt ta bort den tillagda koden eller filerna tillsammans med alla API-anrop som du har lagt till i projektet.

# <a name="net"></a>[.NET](#tab/net)

När du lägger till Programstatistik telemetri i en Visual Studio ASP.NET projekt, läggs följande filer till:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

Följande koddelar läggs till:

- [Projektets namn].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Om projektet har en Layout.cshtml-fil läggs koden nedan till.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

När du lägger till Programstatistik telemetri i en Visual Studio ASP.NET Core mallprojekt, läggs följande kod:

- [Projektets namn].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Nästa steg

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)