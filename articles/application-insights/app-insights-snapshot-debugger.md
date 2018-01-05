---
title: "Azure Application Insights ögonblicksbild felsökare för .NET-appar | Microsoft Docs"
description: "Felsöka ögonblicksbilder automatiskt samlas in när undantag utlöstes i produktion .NET appar"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: f1efbfc1f85f4c2fa404742e2d71344b3426c94d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Felsöka ögonblicksbilder på undantag i .NET-appar

När ett undantag inträffar kan du automatiskt samla in en debug ögonblicksbild webbtillämpningen live. Ögonblicksbilden visar tillståndet för källkoden och variabler för tillfället undantagsfel uppstod. Felsökaren ögonblicksbild (förhandsgranskning) i [Azure Application Insights](app-insights-overview.md) övervakar undantagstelemetri från ditt webbprogram. Den samlar in ögonblicksbilder på de översta utlösande undantag så att du har den information du behöver att diagnostisera problem i produktion. Inkludera den [ögonblicksbild insamlaren NuGet-paketet](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) i ditt program och du kan också konfigurera samlingen parametrar i [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ögonblicksbilder visas på [undantag](app-insights-asp-net-exceptions.md) i Application Insights-portalen.

Du kan visa debug ögonblicksbilder i portalen för att se anropet stacken och inspektera variabler vid varje anropsstacken. För att få en mer kraftfulla felsökning upplevelse med källkoden, öppna ögonblicksbilder med Visual Studio 2017 Enterprise av [hämtar ögonblicksbild felsökare tillägget för Visual Studio](https://aka.ms/snapshotdebugger). I Visual Studio kan du också [ange Snappoints till interaktivt ta ögonblicksbilder](https://aka.ms/snappoint) utan att vänta på ett undantag.

Ögonblicksbild samlingen är tillgänglig för:
* .NET framework och ASP.NET-program med .NET Framework 4.5 eller senare.
* .NET core 2.0 och ASP.NET Core 2.0 program som körs på Windows.

Följande miljöer stöds:
* Azure Apptjänst.
* Molntjänsten Azure kör OS-familjen 4 eller senare.
* Azure Service Fabric-tjänster som körs på Windows Server 2012 R2 eller senare.
* Azure virtuella datorer som kör Windows Server 2012 R2 eller senare.
* Lokala virtuella eller fysiska datorer som kör Windows Server 2012 R2 eller senare.

> [!NOTE]
> Klientprogram (till exempel WPF, Windows Forms eller UWP) stöds inte.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurera ögonblicksbild samling för ASP.NET-program

1. [Aktivera Application Insights i ditt webbprogram](app-insights-asp-net.md), om du inte gjort det ännu.

2. Inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app. 

3. Granska standardalternativen som paketet lagts till i [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Ögonblicksbilder samlas endast för undantag som har rapporterats till Application Insights. I vissa fall (till exempel äldre versioner av .NET-plattformen) kan du behöva [konfigurera undantag samling](app-insights-asp-net-exceptions.md#exceptions) att se undantag med ögonblicksbilder i portalen.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurera ögonblicksbild samlingen för ASP.NET Core 2.0-program

1. [Aktivera Application Insights i ditt webbprogram för ASP.NET Core](app-insights-asp-net-core.md), om du inte gjort det ännu.

> [!NOTE]
> Vara säker på att tillämpningsprogrammet refererar till version 2.1.1 eller nyare Microsoft.ApplicationInsights.AspNetCore paketets.

2. Inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ändra programmets `Startup` klassen för att lägga till och konfigurera ögonblicksbild insamlarens telemetri processor.

   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Konfigurera ögonblicksbild insamlaren genom att lägga till ett avsnitt som SnapshotCollectorConfiguration appsettings.json. Exempel:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurera ögonblicksbild samling för andra .NET-program

1. Om programmet inte är redan instrumenterats med Application Insights, Kom igång genom att [aktivera Application Insights och ange nyckeln instrumentation](app-insights-windows-desktop.md).

2. Lägg till den [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ögonblicksbilder samlas endast för undantag som har rapporterats till Application Insights. Du kan behöva ändra koden för att rapportera dem. Koden för undantagshantering beror på strukturen för ditt program, men ett exempel är lägre än:
    ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```
    
## <a name="grant-permissions"></a>Bevilja behörigheter

Ägarna av Azure-prenumeration kan inspektera ögonblicksbilder. Andra användare måste beviljas behörighet genom en ägare.

Om du vill ge behörighet, tilldela den `Application Insights Snapshot Debugger` rollen för användare som ska granska ögonblicksbilder. Den här rollen kan tilldelas enskilda användare eller grupper av prenumeration ägare för målet Application Insights-resurs eller dess resursgrupp eller prenumeration.

1. Öppna bladet Access Control (IAM).
1. Klicka på + Lägg till.
1. Välj Application Insights ögonblicksbild felsökare från listrutan roller.
1. Sök efter och ange ett namn för användaren att lägga till.
1. Klicka på Spara för att lägga till användaren i rollen.


> [!IMPORTANT]
> Ögonblicksbilder kan innehålla personliga och annan känslig information i variabeln och parametervärden.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Felsöka ögonblicksbilder i Application Insights-portalen

Om en ögonblicksbild som är tillgänglig för en viss undantag eller problem-ID, en **öppna Debug ögonblicksbild** knappen visas i den [undantag](app-insights-asp-net-exceptions.md) i Application Insights-portalen.

![Öppna Debug ögonblicksbild knappen undantag](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

I vyn Debug ögonblicksbild ser du en anropsstacken och en variabler rutan. När du väljer ramar i anropsstacken i fönstret anropet stacken, kan du visa lokala variabler och parametrar för funktionen anropa i fönstret variabler.

![Visa Debug ögonblicksbild i portalen](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Ögonblicksbilder kan innehålla känslig information, och som standard är de inte kan visas. Om du vill visa ögonblicksbilder, måste du ha den `Application Insights Snapshot Debugger` roll som tilldelats dig.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Felsöka ögonblicksbilder med Visual Studio 2017 Enterprise
1. Klicka på den **hämta ögonblicksbild** för att hämta en `.diagsession` fil som kan öppnas i Visual Studio 2017 Enterprise. 

2. Öppna den `.diagsession` -fil, måste du först [ladda ned och installera tillägget ögonblicksbild felsökare för Visual Studio](https://aka.ms/snapshotdebugger).

3. När du öppnar ögonblicksbildfilen, visas sidan Minidump felsökning i Visual Studio. Klicka på **Debug förvaltad kod** att starta felsökningen ögonblicksbilden. Ögonblicksbilden öppnar kodraderna där undantaget utlöstes så att du kan felsöka det aktuella tillståndet för processen.

    ![Visa debug ögonblicksbild i Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Hämtade ögonblicksbilden innehåller symbolfiler som hittades på webbservern för programmet. Symbolfilerna krävs för att associera ögonblicksbilddata med källkoden. Se till att aktivera symbolen distribution när du publicerar ditt webbprogram för Apptjänst-appar.

## <a name="how-snapshots-work"></a>Så här fungerar ögonblicksbilder

När programmet startas skapas en separat ögonblicksbild överföring process som övervakar ditt program för snapshot-begäranden. När en ögonblicksbild har begärts görs en skuggkopia av processen som körs i cirka 10 till 20 millisekunder. Processen shadow analyseras och skapa en ögonblicksbild av medan huvudsakliga processen fortsätter att köra och hantera trafik till användare. Ögonblicksbilden överförs sedan till Application Insights tillsammans med alla relevanta (.pdb)-symbolfiler som behövs för att visa ögonblicksbilden.

## <a name="current-limitations"></a>Aktuella begränsningar

### <a name="publish-symbols"></a>Publicera symboler
Snapshot-felsökaren kräver symbolfiler på produktionsservern att avkoda variabler och att tillhandahålla felsökning i Visual Studio. 15.2 versionen av Visual Studio 2017 publicerar symboler för versionen versioner som standard när den publicerar till App Service. I tidigare versioner måste du lägga till följande rad i din publiceringsprofil `.pubxml` så att symboler publiceras i versionsläge:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Se till att symbolfiler finns i samma mapp för programmets DLL-filen för Azure Compute och andra typer (vanligtvis `wwwroot/bin`) eller som är tillgängliga på den aktuella sökvägen.

### <a name="optimized-builds"></a>Optimerad versioner
I vissa fall, kan lokala variabler inte visas i versionen versioner på grund av optimeringar som ska användas när du skapar.

## <a name="troubleshooting"></a>Felsökning

Dessa tips hjälpa dig att felsöka problem med ögonblicksbild felsökningsprogrammet.

### <a name="verify-the-instrumentation-key"></a>Verifiera instrumentation nyckeln

Kontrollera att du använder rätt instrumentation nyckeln i ditt publicerade program. Application Insights läser vanligtvis instrumentation nyckeln från filen applicationinsights.config. Kontrollera att värdet är samma som den instrumentation nyckeln för Application Insights-resurs som visas i portalen.

### <a name="check-the-uploader-logs"></a>Kontrollera loggarna för överföring

När du har skapat en ögonblicksbild skapas en minidumpfil (.dmp) på disken. En separat överförare-process tar minidump filen och överförs, tillsammans med alla associerade PDB-filer, till Application Insights ögonblicksbild felsökare lagring. När minidump har laddats bort den från disken. Loggfiler för minidump-överföring är kvar på disken. I en Apptjänst-miljö kan du hitta dessa loggar i `D:\Home\LogFiles\Uploader_*.log`. Använd Kudu hanteringswebbplats för Apptjänst för att hitta dessa loggfiler.

1. Öppna din Apptjänst-program i Azure-portalen.

2. Välj den **avancerade verktyg** bladet eller söka efter **Kudu**.
3. Klicka på **Gå**.
4. I den **Felsökningskonsolen** listrutan markerar **CMD**.
5. Klicka på **loggfiler**.

Du bör se minst en fil med ett namn som börjar med `Uploader_` och en `.log` tillägg. Klicka på önskad ikon för att hämta alla loggfiler eller öppna dem i en webbläsare.
Filnamnet innehåller namnet på datorn. Om din App Service-instans finns på flera datorer, finns separata loggfiler för varje dator. När överföring identifierar en ny minidumpfil, registreras den i loggfilen. Här är ett exempel på uppladdningen lyckas:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

I det förra exemplet instrumentation nyckeln är `c12a605e73c44346a984e00000000000`. Det här värdet ska matcha instrumentation nyckeln för ditt program.
Minidump är associerad med en ögonblicksbild med ID `139e411a23934dc0b9ea08a626db16c5`. Du kan använda detta ID senare för att hitta den associerade undantagstelemetri i Application Insights Analytics.

Överföring söker efter nya PDB-filer om var 15: e minut. Här är ett exempel:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

För program som är _inte_ finns i App Service, överföring loggarna finns i samma mapp som minidumpar: `%TEMP%\Dumps\<ikey>` (där `<ikey>` är instrumentation-nyckel).

För roller i molntjänster kanske tillfälliga standardmappen för liten för minidumpfiler. I så fall kan du ange en annan mapp via egenskapen TempFolder i ApplicationInsights.config.

```xml
<TelemetryProcessors>
  <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
    <!-- Use an alternative folder for minidumps -->
    <TempFolder>C:\Snapshots\Go\Here</TempFolder>
    </Add>
</TelemetryProcessors>
```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Använd Application Insights Sök efter undantag med ögonblicksbilder

När en ögonblicksbild skapas är utlösande undantaget märkta med en ögonblicksbild-ID. När undantagstelemetri rapporterats till Application Insights ögonblicksbilds-ID ingår som en anpassad egenskap. Med hjälp av bladet Sök i Application Insights, du kan hitta all telemetri med den `ai.snapshot.id` anpassad egenskap.

1. Bläddra till Application Insights-resurs i Azure-portalen.
2. Klicka på **Sök**.
3. Typen `ai.snapshot.id` i sökrutan och tryck på RETUR.

![Sök efter telemetri med en ögonblicksbild ID i portalen](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Om sökningen returnerar inga resultat har inga ögonblicksbilder rapporterats till Application Insights för ditt program i det valda tidsintervallet.

Om du vill söka efter en specifik ögonblicksbilds-ID från överförare loggar, skriver du detta ID i sökrutan. Om du inte hittar telemetri för en ögonblicksbild som du vet överfördes, gör du följande:

1. Kontrollera att du tittar på rätt Application Insights-resursen genom att verifiera instrumentation nyckeln.

2. Med tidsstämpeln från överföring loggen kan justera filtret tidsintervall för sökningen att täcka det tidsintervallet.

Om du fortfarande inte ser ett undantag med detta ID för ögonblicksbild rapporterats undantagstelemetri inte till Application Insights. Detta kan inträffa om programmet kraschade när det tog ögonblicksbilden men innan den rapporterades undantagstelemetri. I det här fallet finns i loggarna Apptjänst under `Diagnose and solve problems` att se om det fanns oväntade omstarter eller ohanterade undantag.

## <a name="next-steps"></a>Nästa steg

* [Ange snappoints i koden](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) att hämta ögonblicksbilder utan att vänta på ett undantag.
* [Diagnostisera undantag i web apps](app-insights-asp-net-exceptions.md) förklarar hur du se flera undantag till Application Insights. 
* [Identifiering för smartkort](app-insights-proactive-diagnostics.md) upptäcker automatiskt prestandaavvikelser.
