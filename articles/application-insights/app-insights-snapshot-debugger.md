---
title: Azure Application Insights ögonblicksbild felsökare för .NET-appar | Microsoft Docs
description: Felsöka ögonblicksbilder automatiskt samlas in när undantag utlöstes i produktion .NET appar
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mbullwin; pharring
ms.openlocfilehash: 66339e5f5d2cc7447df0f8faf70d2d9fd45db738
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Felsöka ögonblicksbilder på undantag i .NET-appar

När ett undantag inträffar kan du automatiskt samla in en debug ögonblicksbild webbtillämpningen live. Ögonblicksbilden visar tillståndet för källkoden och variabler för tillfället undantagsfel uppstod. Felsökaren ögonblicksbild (förhandsgranskning) i [Azure Application Insights](app-insights-overview.md) övervakar undantagstelemetri från ditt webbprogram. Den samlar in ögonblicksbilder på de översta utlösande undantag så att du har den information du behöver att diagnostisera problem i produktion. Inkludera den [ögonblicksbild insamlaren NuGet-paketet](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) i ditt program och du kan också konfigurera samlingen parametrar i [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ögonblicksbilder visas på [undantag](app-insights-asp-net-exceptions.md) i Application Insights-portalen.

Du kan visa debug ögonblicksbilder i portalen för att se anropet stacken och inspektera variabler vid varje anropsstacken. För att få en mer kraftfulla felsökning upplevelse med källkoden, öppna ögonblicksbilder med Visual Studio 2017 Enterprise av [hämtar ögonblicksbild felsökare tillägget för Visual Studio](https://aka.ms/snapshotdebugger). I Visual Studio kan du också [ange Snappoints till interaktivt ta ögonblicksbilder](https://aka.ms/snappoint) utan att vänta på ett undantag.

Ögonblicksbild samlingen är tillgänglig för:
* .NET framework och ASP.NET-program med .NET Framework 4.5 eller senare.
* .NET core 2.0 och ASP.NET Core 2.0 program som körs på Windows.

Följande miljöer stöds:
* Azure App Service.
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
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
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

    Lägg till följande using-instruktioner till `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Lägg till följande `SnapshotCollectorTelemetryProcessorFactory` undergrupp till `Startup` klass.

   ```csharp
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
       ...
    ```
    Lägg till den `SnapshotCollectorConfiguration` och `SnapshotCollectorTelemetryProcessorFactory` startade pipeline-tjänster:

    ```csharp
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
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurera ögonblicksbild samling för andra .NET-program

1. Om programmet inte är redan instrumenterats med Application Insights, Kom igång genom att [aktivera Application Insights och ange nyckeln instrumentation](app-insights-windows-desktop.md).

2. Lägg till den [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ögonblicksbilder samlas endast för undantag som har rapporterats till Application Insights. Du kan behöva ändra koden för att rapportera dem. Koden för undantagshantering beror på strukturen för ditt program, men ett exempel är lägre än:
    ```csharp
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

1. Gå till Application Insights-resurs i Azure-portalen.
1. Klicka på **åtkomstkontroll (IAM)**.
1. Klicka på den **+ Lägg till** knappen.
1. Välj **Application Insights ögonblicksbild felsökare** från den **roller** listrutan.
1. Sök efter och ange ett namn för användaren att lägga till.
1. Klicka på den **spara** för att lägga till användaren i rollen.


> [!IMPORTANT]
> Ögonblicksbilder kan innehålla personliga och annan känslig information i variabeln och parametervärden.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Felsöka ögonblicksbilder i Application Insights-portalen

Om en ögonblicksbild som är tillgänglig för en viss undantag eller problem-ID, en **öppna Debug ögonblicksbild** knappen visas i den [undantag](app-insights-asp-net-exceptions.md) i Application Insights-portalen.

![Öppna Debug ögonblicksbild knappen undantag](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

I vyn Debug ögonblicksbild ser du en anropsstacken och en variabler rutan. När du väljer ramar i anropsstacken i fönstret anropet stacken, kan du visa lokala variabler och parametrar för funktionen anropa i fönstret variabler.

![Visa Debug ögonblicksbild i portalen](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Ögonblicksbilder kan innehålla känslig information och som standard de inte kan visas. Om du vill visa ögonblicksbilder, måste du ha den `Application Insights Snapshot Debugger` roll som tilldelats dig.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Felsöka ögonblicksbilder med Visual Studio 2017 Enterprise
1. Klicka på den **hämta ögonblicksbild** för att hämta en `.diagsession` fil som kan öppnas i Visual Studio 2017 Enterprise.

2. Öppna den `.diagsession` -fil, måste du först [ladda ned och installera tillägget ögonblicksbild felsökare för Visual Studio](https://aka.ms/snapshotdebugger).

3. När du öppnar ögonblicksbildfilen, visas sidan Minidump felsökning i Visual Studio. Klicka på **Debug förvaltad kod** att starta felsökningen ögonblicksbilden. Ögonblicksbilden öppnar kodraderna där undantaget utlöstes så att du kan felsöka det aktuella tillståndet för processen.

    ![Visa debug ögonblicksbild i Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Hämtade ögonblicksbilden innehåller symbolfiler som hittades på webbservern för programmet. Symbolfilerna krävs för att associera ögonblicksbilddata med källkoden. Se till att aktivera symbolen distribution när du publicerar ditt webbprogram för Apptjänst-appar.

## <a name="how-snapshots-work"></a>Så här fungerar ögonblicksbilder

Ögonblicksbild insamlaren implementeras som en [Application Insights telemetri Processor](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). När programmet körs, har ögonblicksbild insamlaren telemetri Processor lagts till programmets telemetri pipeline.
Varje gång programmet-anrop [TrackException](app-insights-asp-net-exceptions.md#exceptions), ögonblicksbild insamlaren beräknar Problem-ID från typ av undantag som genereras och utlösande-metoden.
När programmet anropar TrackException, ökas en räknare för lämplig Problem-ID. När räknaren når den `ThresholdForSnapshotting` värde, Problem-ID har lagts till i en Plan för datainsamling.

Ögonblicksbild insamlaren också övervakar undantag när de utlöses genom att prenumerera på den [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) händelse. När händelsen utlöses beräknas Problem-ID för undantaget och jämförs med Problem-ID: N i Plan för datainsamling.
Om det finns en matchning, skapas en ögonblicksbild av processen som körs. Ögonblicksbilden tilldelas en unik identifierare och undantaget är stämplad med en identifierare. När FirstChanceException hanteraren returnerar bearbetas undantaget utlöses som vanligt. Undantaget når så småningom igen metoden TrackException där, tillsammans med identifieraren ögonblicksbild har rapporterats till Application Insights.

Den huvudsakliga processen fortsätter att köra och hantera trafik för användare med lite avbrott. Under tiden är ögonblicksbilden levererat till ögonblicksbild överförare-processen. Snapshot-överföring skapas en minidump och överförs till Application Insights tillsammans med alla relevanta symbolfiler (.pdb).

> [!TIP]
> - En ögonblicksbild av en process är en pausade kloning av processen som körs.
> - Skapa ögonblicksbilden tar cirka 10 till 20 millisekunder.
> - Standardvärdet för `ThresholdForSnapshotting` är 1. Detta är det minsta värdet. Appen måste därför att utlösa samma undantag **två gånger** innan en ögonblicksbild skapas.
> - Ange `IsEnabledInDeveloperMode` till true om du vill generera ögonblicksbilder vid felsökning i Visual Studio.
> - Takt för skapande av ögonblicksbilder är begränsat av det `SnapshotsPerTenMinutesLimit` inställningen. Som standard är gränsen en ögonblicksbild av varje tio minuter.
> - Mer än 50 ögonblicksbilder per dag kan överföras.

## <a name="current-limitations"></a>Aktuella begränsningar

### <a name="publish-symbols"></a>Publicera symboler
Snapshot-felsökaren kräver symbolfiler på produktionsservern att avkoda variabler och att tillhandahålla felsökning i Visual Studio. 15.2 versionen av Visual Studio 2017 publicerar symboler för versionen versioner som standard när den publicerar till App Service. I tidigare versioner måste du lägga till följande rad i din publiceringsprofil `.pubxml` så att symboler publiceras i versionsläge:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Se till att symbolfiler finns i samma mapp för programmets DLL-filen för Azure Compute och andra typer (vanligtvis `wwwroot/bin`) eller som är tillgängliga på den aktuella sökvägen.

### <a name="optimized-builds"></a>Optimerad versioner
I vissa fall, kan lokala variabler inte visas i versionen versioner på grund av optimeringar av JIT-kompilatorn.
I Azure App Services kan dock ögonblicksbild insamlaren deoptimize utlösande metoder som är en del av dess Plan för datainsamling.

> [!TIP]
> Installera Application Insights plats Extension i din App Service som du kan få support för deoptimization.

## <a name="troubleshooting"></a>Felsökning

Dessa tips hjälpa dig att felsöka problem med ögonblicksbild felsökningsprogrammet.

### <a name="use-the-snapshot-health-check"></a>Använd hälsokontroll ögonblicksbild
Några vanliga problem resultera i öppna Debug ögonblicksbilden inte visas. Med hjälp av en gammal ögonblicksbild insamlare, till exempel; nå daglig uppladdningsgräns; eller kanske ögonblicksbilden tar bara lång tid att överföra. Använd ögonblicksbild Health Check för att felsöka vanliga problem.

Det finns en länk i fönstret undantag för slutpunkt till slutpunkt trace vyn som tar dig till ögonblicksbild Health Check.

![Ange ögonblicksbild hälsokontroll](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Gränssnittet interaktiv, chatt-liknande söker efter vanliga problem och hjälper dig att åtgärda dem.

![Hälsokontroll](./media/app-insights-snapshot-debugger/healthcheck.png)

Om det inte löser problemet, referera till följande manuella åtgärder för felsökning.

### <a name="verify-the-instrumentation-key"></a>Verifiera instrumentation nyckeln

Kontrollera att du använder rätt instrumentation nyckeln i ditt publicerade program. Vanligtvis läsa nyckeln instrumentation från filen applicationinsights.config. Kontrollera att värdet är samma som den instrumentation nyckeln för Application Insights-resurs som visas i portalen.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uppgradera till den senaste versionen av NuGet-paketet

Använd Pakethanteraren NuGet för Visual Studio för att kontrollera att du använder den senaste versionen av Microsoft.ApplicationInsights.SnapshotCollector. Viktig information finns på https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Kontrollera loggarna för överföring

När du har skapat en ögonblicksbild skapas en minidumpfil (.dmp) på disken. En separat överförare process skapar minidump filen och överförs, tillsammans med alla associerade PDB-filer, till Application Insights ögonblicksbild felsökare lagring. När minidump har laddats bort den från disken. Loggfiler för överföring processen sparas på disk. I en Apptjänst-miljö kan du hitta dessa loggar i `D:\Home\LogFiles`. Använd Kudu hanteringswebbplats för Apptjänst för att hitta dessa loggfiler.

1. Öppna din Apptjänst-program i Azure-portalen.
2. Klicka på **avancerade verktyg**, eller Sök efter **Kudu**.
3. Klicka på **Gå**.
4. I den **Felsökningskonsolen** listrutan markerar **CMD**.
5. Klicka på **loggfiler**.

Du bör se minst en fil med ett namn som börjar med `Uploader_` eller `SnapshotUploader_` och en `.log` tillägg. Klicka på önskad ikon för att hämta alla loggfiler eller öppna dem i en webbläsare.
Filnamnet innehåller ett unikt suffix som identifierar App Service-instans. Om din App Service-instans finns på flera datorer, finns separata loggfiler för varje dator. När överföring identifierar en ny minidumpfil, registreras den i loggfilen. Här är ett exempel på en lyckad ögonblicksbild och ladda upp:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Exemplet ovan är från version 1.2.0 av Microsoft.ApplicationInsights.SnapshotCollector NuGet-paketet. I tidigare versioner överförare kallas `MinidumpUploader.exe` och loggen mindre detaljerad.

I det förra exemplet instrumentation nyckeln är `c12a605e73c44346a984e00000000000`. Det här värdet ska matcha instrumentation nyckeln för ditt program.
Minidump är associerad med en ögonblicksbild med ID `139e411a23934dc0b9ea08a626db16c5`. Du kan använda detta ID senare för att hitta den associerade undantagstelemetri i Application Insights Analytics.

Överföring söker efter nya PDB-filer om var 15: e minut. Här är ett exempel:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

För program som är _inte_ finns i App Service, överföring loggarna finns i samma mapp som minidumpar: `%TEMP%\Dumps\<ikey>` (där `<ikey>` är instrumentation-nyckel).

### <a name="troubleshooting-cloud-services"></a>Felsökning av molntjänster
För roller i molntjänster kanske tillfälliga standardmappen för liten för minidumpfiler, vilket leder till förlorade ögonblicksbilder.
Utrymmet som krävs beror på totalt arbetsminnet för ditt program och antalet samtidiga ögonblicksbilder.
Arbetsminnet för en 32-bitars ASP.NET-webbroll är vanligtvis mellan 200 MB och 500 MB.
Tillåt för minst två samtidiga ögonblicksbilder.
Till exempel om 1 GB totala arbetsminnet används i ditt program bör du kontrollera att det finns minst 2 GB diskutrymme för lagring av ögonblicksbilder.
Följ dessa steg om du vill konfigurera din roll i Molntjänsten med en dedikerad lokal resurs för ögonblicksbilder.

1. Lägga till en ny lokal resurs i din molntjänst genom att redigera Cloud Service-definitionsfil (.csdef). I följande exempel definieras en resurs med namnet `SnapshotStore` med en storlek på 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Ändra din roll startkoden för att lägga till en miljövariabel som pekar på den `SnapshotStore` lokal resurs. För arbetsroller, kod som ska läggas till din roll `OnStart` metoden:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   För Web-roller (ASP.NET), kod som ska läggas till ditt webbprogram `Application_Start` metoden:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Uppdatera din roll ApplicationInsights.config-filen om du vill åsidosätta den temporära mapp finns som används av `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Använd Application Insights Sök efter undantag med ögonblicksbilder

När en ögonblicksbild skapas är utlösande undantaget märkta med en ögonblicksbild-ID. Detta ID ögonblicksbild ingår som en anpassad egenskap när undantagstelemetri har rapporterats till Application Insights. Med hjälp av **Sök** i Application Insights, kan du hitta all telemetri med den `ai.snapshot.id` anpassad egenskap.

1. Bläddra till Application Insights-resurs i Azure-portalen.
2. Klicka på **Sök**.
3. Typen `ai.snapshot.id` i sökrutan och tryck på RETUR.

![Sök efter telemetri med en ögonblicksbild ID i portalen](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Om sökningen returnerar inga resultat har inga ögonblicksbilder rapporterats till Application Insights för ditt program i det valda tidsintervallet.

Om du vill söka efter en specifik ögonblicksbilds-ID från överförare loggar, skriver du detta ID i sökrutan. Om du inte hittar telemetri för en ögonblicksbild som du vet överfördes, gör du följande:

1. Kontrollera att du tittar på rätt Application Insights-resursen genom att verifiera instrumentation nyckeln.

2. Med tidsstämpeln från överföring loggen kan justera filtret tidsintervall för sökningen att täcka det tidsintervallet.

Om du fortfarande inte ser ett undantag med detta ID för ögonblicksbild rapporterats undantagstelemetri inte till Application Insights. Detta kan inträffa om programmet kraschade när det tog ögonblicksbilden men innan den rapporterades undantagstelemetri. I det här fallet finns i loggarna Apptjänst under `Diagnose and solve problems` att se om det fanns oväntade omstarter eller ohanterade undantag.

### <a name="edit-network-proxy-or-firewall-rules"></a>Redigera Nätverksregler för proxyservern eller brandväggen

Om programmet ansluter till Internet via en proxyserver eller brandvägg kan behöva du redigera regler för att tillåta programmet att kommunicera med tjänsten ögonblicksbild felsökare. Här är [en lista över IP-adresser och portar som används av ögonblicksbild felsökningsprogrammet](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Nästa steg

* [Ange snappoints i koden](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) att hämta ögonblicksbilder utan att vänta på ett undantag.
* [Diagnostisera undantag i web apps](app-insights-asp-net-exceptions.md) förklarar hur du se flera undantag till Application Insights.
* [Identifiering för smartkort](app-insights-proactive-diagnostics.md) upptäcker automatiskt prestandaavvikelser.
