---
title: Azure Application Insights Snapshot Debugger för .NET-appar | Microsoft Docs
description: Felsök ögonblicksbilder automatiskt samlas in när undantag utlöses i produktion .NET-appar
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: 11e9099ba9008ead6583eaf30292cdec63475f02
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994260"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Felsök ögonblicksbilder på undantag i .NET-appar

Om ett undantag inträffar, kan du automatiskt samla in en felsökning för ögonblicksbilder från ditt webbprogram. Ögonblicksbilden visar tillståndet för källkoden och variabler för tillfället som undantaget uppstod. Felsökning av ögonblicksbild (förhandsversion) i [Azure Application Insights](app-insights-overview.md) övervakar undantagstelemetri från din webbapp. Den samlar in ögonblicksbilder i din upp utlöser undantag så att du har den information du behöver att diagnostisera problem i produktionsmiljön. Inkludera den [Snapshot collector NuGet-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) i ditt program och om du vill konfigurera insamlingsparametrarna i [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ögonblicksbilder som visas på [undantag](app-insights-asp-net-exceptions.md) i Application Insights-portalen.

Du kan visa debug ögonblicksbilder i portalen för att se anropet stacken och inspektera variabler vid varje anropsstacken. För att få en mer kraftfullt felsökningsverktyg som fungerar med källkoden kan du öppna ögonblicksbilder med Visual Studio 2017 Enterprise. I Visual Studio kan du också [ange Snappoints att interaktivt ta ögonblicksbilder](https://aka.ms/snappoint) utan att behöva vänta ett undantag.

Felsök ögonblicksbilder lagras i sju dagar. Den här bevarandeprincipen är inställd på basis av per program. Om du vill öka det här värdet kan du begära en ökning genom att öppna ett supportärende i Azure-portalen.

Ögonblicksbild samling är tillgängligt för:
* .NET framework och ASP.NET-program med .NET Framework 4.5 eller senare.
* .NET core 2.0 och ASP.NET Core 2.0-program som körs på Windows.

Följande miljöer stöds:
* Azure App Service.
* Azure Cloud Service som kör OS-familj 4 eller senare.
* Azure Service Fabric-tjänster som körs på Windows Server 2012 R2 eller senare.
* Azure-datorer med Windows Server 2012 R2 eller senare.
* Lokala virtuella eller fysiska datorer som kör Windows Server 2012 R2 eller senare.

> [!NOTE]
> Klientprogram (till exempel WPF, Windows-formulär eller UWP) stöds inte.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurera insamling av ögonblicksbild för ASP.NET-program

1. [Aktivera Application Insights i din webbapp](app-insights-asp-net.md), om du inte gjort det ännu.

2. Inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

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

4. Ögonblicksbilder samlas endast på undantag som har rapporterats till Application Insights. I vissa fall (exempelvis äldre versioner av .NET-plattformen) kan du behöva [konfigurera insamling av undantag](app-insights-asp-net-exceptions.md#exceptions) vill visa undantag med ögonblicksbilder i portal.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurera insamling av ögonblicksbild för ASP.NET Core 2.0-program

1. [Aktivera Application Insights i din ASP.NET Core-webbapp](app-insights-asp-net-core.md), om du inte gjort det ännu.

    > [!NOTE]
    > Att se till att ditt program refererar till version 2.1.1 eller senare av Microsoft.ApplicationInsights.AspNetCore-paketet.

2. Inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ändra programmets `Startup` klassen för att lägga till och konfigurera den Ögonblicksbildsinsamlaren telemetri processor.

    Lägg till följande using-satser till `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Lägg till följande `SnapshotCollectorTelemetryProcessorFactory` klassen till `Startup` klass.

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
    Lägg till den `SnapshotCollectorConfiguration` och `SnapshotCollectorTelemetryProcessorFactory` tjänster till Start-pipelinen:

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

4. Konfigurera Snapshot Collector genom att lägga till ett avsnitt SnapshotCollectorConfiguration appsettings.json. Exempel:

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

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurera insamling av ögonblicksbild för andra .NET-program

1. Om programmet inte redan har instrumenterats med Application Insights, Kom igång genom att [att aktivera Application Insights och ange instrumenteringsnyckeln](app-insights-windows-desktop.md).

2. Lägg till den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ögonblicksbilder samlas endast på undantag som har rapporterats till Application Insights. Du kan behöva ändra koden för att rapportera dem. Undantagshantering kod är beroende av strukturen för ditt program, men ett exempel är lägre än:
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

Åtkomst till ögonblicksbilder är skyddat av rollbaserad åtkomstkontroll (RBAC). Om du vill kontrollera en ögonblicksbild, måste du först läggas till rollen krävs av en prenumerationsägare.

> [!NOTE]
> Ägare och deltagare har inte den här rollen automatiskt. Om de vill visa ögonblicksbilder måste de lägga till sig själva till rollen.

Prenumerationsägare ska tilldela den `Application Insights Snapshot Debugger` roll till användare som inspektera ögonblicksbilder. Den här rollen kan tilldelas till enskilda användare eller grupper av prenumerationsägare för målets Application Insights-resursen eller dess resursgrupp eller prenumeration.

1. Gå till Application Insights-resurs i Azure-portalen.
1. Klicka på **åtkomstkontroll (IAM)**.
1. Klicka på den **+ Lägg till rolltilldelning** knappen.
1. Välj **Application Insights Snapshot Debugger** från den **roller** listrutan.
1. Sök efter och ange ett namn för att lägga till.
1. Klicka på den **spara** för att lägga till användaren till rollen.


> [!IMPORTANT]
> Ögonblicksbilder kan innehålla personligt och annan känslig information i variabeln och parametervärden.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Felsök ögonblicksbilder i Application Insights-portalen

Om en ögonblicksbild är tillgängligt för ett visst undantag eller ett problem-ID, en **Open Debug Snapshot** knappen visas i den [undantag](app-insights-asp-net-exceptions.md) i Application Insights-portalen.

![Öppna Felsök ögonblicksbild knappen undantag](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

I vyn Felsök ögonblicksbild visas en anropsstack och en variabler-fönstret. När du väljer bildrutor i anropsstacken i fönstret anrop stack, du kan visa lokala variabler och parametrar för funktionen anropa i fönstret variabler.

![Visa Felsök ögonblicksbilder i portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Ögonblicksbilder kan innehålla känslig information och som standard de inte kan visas. Om du vill visa ögonblicksbilder, måste du ha den `Application Insights Snapshot Debugger` roll som tilldelats dig.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Felsök ögonblicksbilder med Visual Studio 2017 Enterprise
1. Klicka på den **ladda ned ögonblicksbild** för att ladda ned en `.diagsession` -fil som kan öppnas av Visual Studio 2017 Enterprise.

2. Öppna den `.diagsession` filen, måste du ha installerat komponenten Snapshot Debugger VS. Snapshot Debugger-komponenten är en obligatorisk del av arbetsbelastningen ASP.net i VS och kan väljas från listan över enskilda komponenter i VS-installationsprogrammet. Om du använder en version av Visual Studio före 15.5 måste du installerar tillägget från den [VS marketplace](https://aka.ms/snapshotdebugger).

3. När du har öppnat ögonblicksbildfilen visas sidan Minidump felsökning i Visual Studio. Klicka på **felsöka förvaltad kod** att starta felsökningen ögonblicksbilden. Ögonblicksbilden öppnar rad med kod som undantaget uppstod där så att du kan felsöka det aktuella tillståndet för processen.

    ![Visa ögonblicksbild för felsökning i Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Hämtade ögonblicksbilden innehåller symbolfiler som hittades på webbservern för programmet. Symbolfilerna krävs för att associera ögonblicksbilddata med källkoden. Se till att aktivera symbolen distribution när du publicerar dina web apps för App Service-appar.

## <a name="how-snapshots-work"></a>Så här fungerar ögonblicksbilder

Snapshot Collector implementeras som en [Application Insights Telemetry Processor](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). När programmet körs, läggs Snapshot Collector telemetri processorn till programmets telemetri pipeline.
Varje gång dina program anrop [TrackException](app-insights-asp-net-exceptions.md#exceptions), Snapshot Collector beräknar ett Problem-ID från vilken typ av undantag som genereras och metoden utlösande.
Varje gång programmet anropar TrackException, ökar en räknaren stegvis för lämpliga problemet-ID. När räknaren når den `ThresholdForSnapshotting` värde, Problem-ID läggs till i en samling-Plan.

Snapshot Collector övervakar även undantag när de är utlöst genom att prenumerera på den [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) händelse. När händelsen utlöses beräknas Problem-ID för undantaget och jämföra mot Problem-ID: N i samlingen Plan.
Om det finns en matchning, skapas en ögonblicksbild av processen som körs. Ögonblicksbilden har tilldelats en unik identifierare och undantaget är stämplad med en identifierare. När FirstChanceException hanteraren returnerar bearbetas undantaget utlöses som vanligt. Undantaget når så småningom igen metoden TrackException där den, tillsammans med ögonblicksbild-ID har rapporterats till Application Insights.

Den huvudsakliga processen fortsätter att köra och hantera trafik till användare med lite avbrott. Under tiden kan skickas ögonblicksbilden till ögonblicksbild Gilgit-processen. Ögonblicksbild-överförare skapar en minidump och överför dem till Application Insights tillsammans med alla relevanta symbolfiler (.pdb).

> [!TIP]
> - En process ögonblicksbild är en pausad klon av processen.
> - Det tar cirka 10 till 20 millisekunder för att skapa ögonblicksbilden.
> - Standardvärdet för `ThresholdForSnapshotting` är 1. Detta är också det minsta värdet. Därför kan din app har att utlösa samma undantag **två gånger** innan en ögonblicksbild skapas.
> - Ange `IsEnabledInDeveloperMode` till true om du vill generera ögonblicksbilder vid felsökning i Visual Studio.
> - Frekvensen för ögonblicksbilder skapas begränsas av de `SnapshotsPerTenMinutesLimit` inställningen. Som standard är gränsen en ögonblicksbild varje tio minuter.
> - Mer än 50 ögonblicksbilder per dag kan laddas upp.

## <a name="current-limitations"></a>Aktuella begränsningar

### <a name="publish-symbols"></a>Publicera symboler
Snapshot Debugger kräver symbolfiler på produktionsservern att avkoda variabler och felsöka problem i Visual Studio.
Version 15.2 (eller senare) för Visual Studio 2017 publicerar symboler för versionen skapar som standard när du publicerar den till App Service. I tidigare versioner måste du lägga till följande rad i din publiceringsprofil `.pubxml` filen så att symboler som publiceras i Produktionsläge:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

För Azure Compute och andra typer, se till att symbolfiler finns i samma mapp med huvudprogrammet DLL-filen (vanligtvis `wwwroot/bin`) eller är tillgängliga på den aktuella sökvägen.

### <a name="optimized-builds"></a>Optimerad versioner
I vissa fall kan visas lokala variabler inte i version versioner på grund av optimeringar av JIT-kompilatorn.
I Azure App Services, kan dock Snapshot Collector deoptimize utlösande metoder som är en del av dess Plan för datainsamling.

> [!TIP]
> Installera Application Insights-Platstillägget i din App Service för att få deoptimization support.

## <a name="troubleshooting"></a>Felsökning

De här tipsen hjälper dig att felsöka problem med Snapshot Debugger.

### <a name="use-the-snapshot-health-check"></a>Använd ögonblicksbild-hälsokontroll
Flera vanliga problem som kan leda till öppen Felsök ögonblicksbilden inte visas. Med hjälp av en inaktuell Snapshot Collector, till exempel nå daglig uppladdningsgräns; eller kanske ögonblicksbilden tar bara lång tid att ladda upp. Använd ögonblicksbild hälsokontrollen för att felsöka vanliga problem.

Det finns en länk i fönstret undantag av vyn slutpunkt till slutpunkt-spårning som leder till ögonblicksbild hälsokontrollen.

![Ange ögonblicksbild-hälsokontroll](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Interaktiv, chatt-liknande användargränssnitt söker efter vanliga problem och hjälper dig att åtgärda dem.

![Hälsokontroll](./media/app-insights-snapshot-debugger/healthcheck.png)

Om det inte löser problemet, referera till följande manuella åtgärder för felsökning.

### <a name="verify-the-instrumentation-key"></a>Kontrollera instrumenteringsnyckeln

Kontrollera att du använder rätt instrumenteringsnyckeln i ditt publicerade program. Instrumenteringsnyckeln är vanligtvis läsa från filen ApplicationInsights.config. Kontrollera att värdet är samma som instrumenteringsnyckeln för Application Insights-resursen som visas i portalen.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uppgradera till den senaste versionen av NuGet-paketet

Använd Visual Studio NuGet Package Manager för att kontrollera att du använder den senaste versionen av Microsoft.ApplicationInsights.SnapshotCollector. Viktig information finns på https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Kontrollera loggarna överförare

När en ögonblicksbild skapas, skapas en minidumpfil (.dmp) på disken. En separat gilgit process skapar minidump filen och överför den, tillsammans med alla associerade PDB-filer, till Application Insights Snapshot Debugger lagring. När minidump har överförts, bort den från disken. Loggfiler för att gilgit sparas på disk. I en App Service-miljö, hittar du de här loggarna i `D:\Home\LogFiles`. Använd Kudu hanteringswebbplats för App Service för att hitta dessa loggfiler.

1. Öppna App Service-programmet i Azure-portalen.
2. Klicka på **avancerade verktyg**, eller Sök efter **Kudu**.
3. Klicka på **Gå**.
4. I den **Felsökningskonsolen** nedrullningsbara listrutan **CMD**.
5. Klicka på **LogFiles**.

Du bör se minst en fil med ett namn som börjar med `Uploader_` eller `SnapshotUploader_` och en `.log` tillägget. Klicka på önskad ikon för att hämta alla loggfiler eller öppna dem i en webbläsare.
Filnamnet innehåller ett unikt suffix som identifierar App Service-instans. Om din App Service-instans finns på flera datorer, finns det separata loggfiler för varje dator. När gilgit upptäcker en ny minidumpfil, registreras den i loggfilen. Här är ett exempel på en lyckad ögonblicksbild och ladda upp:

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
> I exemplet ovan är från version 1.2.0 eller senare av Microsoft.ApplicationInsights.SnapshotCollector NuGet-paketet. I tidigare versioner gilgit kallas `MinidumpUploader.exe` och loggen mindre detaljerad.

I exemplet ovan är instrumenteringsnyckeln `c12a605e73c44346a984e00000000000`. Det här värdet måste matcha instrumenteringsnyckeln för programmet.
Minidump är associerad med en ögonblicksbild med ID: T `139e411a23934dc0b9ea08a626db16c5`. Du kan använda detta ID senare för att hitta den associerade undantagstelemetri i Application Insights Analytics.

Gilgit söker efter nya PDB-filer om var 15: e minut. Här är ett exempel:

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

För program som _inte_ på App Service gilgit loggarna finns i samma mapp som minidumpar: `%TEMP%\Dumps\<ikey>` (där `<ikey>` är din instrumentationsnyckel).

### <a name="troubleshooting-cloud-services"></a>Felsöka Cloud Services
För roller i molntjänster kanske tillfälliga standardmappen för liten för att rymma minidumpfiler, vilket leder till förlorad ögonblicksbilder.
Utrymmet som krävs är beroende av det totala arbetsminnet för ditt program och antalet samtidiga ögonblicksbilder.
Arbetsminnet för en 32-bitars ASP.NET-webbroll är vanligtvis mellan 200 MB och 500 MB.
Tillåt för minst två samtidiga ögonblicksbilder.
Till exempel om programmet använder 1 GB total arbetsminne, bör du se till att det finns minst 2 GB diskutrymme för lagring av ögonblicksbilder.
Följ dessa steg för att konfigurera din molntjänstroll med en dedikerad lokal resurs för ögonblicksbilder.

1. Lägg till en ny lokal resurs till din molntjänst genom att redigera filen molntjänst tjänstedefinitionsfilen (.csdef). I följande exempel definieras en resurs med namnet `SnapshotStore` med en storlek på 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Ändra din roll Start-koden för att lägga till en miljövariabel som pekar på den `SnapshotStore` lokal resurs. För arbetsroller finns koden ska läggas till din roll `OnStart` metoden:
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

### <a name="overriding-the-shadow-copy-folder"></a>Åsidosätta mappen Shadow Copy

När Snapshot Collector startas, försöker den hitta en mapp på disk som är lämplig för att köra processen ögonblicksbild överföring. Den valda mappen kallas Shadow Copy-mappen.

Snapshot Collector kontrollerar några välkända platser, vilket gör att den har behörighet att kopiera ögonblicksbilden Gilgit-binärfiler. Följande miljövariabler används:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Om det inte går att hitta en lämplig mapp, Snapshot Collector rapporterar ett fel som säger _”det gick inte att hitta en lämplig shadow copy-mapp”._

Om kopieringen misslyckas Snapshot Collector rapporterar en `ShadowCopyFailed` fel.

Om överföring inte kan startas, Snapshot Collector rapporterar ett `UploaderCannotStartFromShadowCopy` fel. Brödtexten i meddelandet innehåller ofta `System.UnauthorizedAccessException`. Det här felet beror vanligtvis på att programmet körs under ett konto med minskade behörigheter. Kontot har behörighet att skriva till mappen shadow copy, men den har inte behörighet att köra kod.

Eftersom dessa fel sker vanligtvis under starten, de kommer vanligtvis följas av ett `ExceptionDuringConnect` fel som säger _”Gilgit gick inte att starta”._

Om du vill undvika dessa fel kan du ange shadow copy-mappen manuellt via den `ShadowCopyFolder` konfigurationsalternativet. Till exempel använder ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Eller, om du använder appsettings.json med en .NET Core-program:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Använd Application Insights-sökning för att hitta undantag med ögonblicksbilder

När en ögonblicksbild skapas, är utlösande undantaget taggade med en ögonblicksbild-ID. Detta ID för ögonblicksbild ingår som en anpassad egenskap när undantagstelemetri som som har rapporterats till Application Insights. Med hjälp av **Search** i Application Insights, hittar du all telemetri med den `ai.snapshot.id` anpassad egenskap.

1. Bläddra till Application Insights-resursen i Azure-portalen.
2. Klicka på **Search**.
3. Typ `ai.snapshot.id` i sökrutan och tryck på RETUR.

![Sök efter telemetri med en ögonblicksbild-ID i portalen](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Om den här sökningen returnerar inga resultat, har inga ögonblicksbilder rapporterats till Application Insights för ditt program i det valda tidsintervallet.

För att söka efter en specifik ögonblicksbild-ID från Gilgit-loggar, skriver du detta ID i sökrutan. Om du inte hittar telemetri för en ögonblicksbild som du vet har överförts, gör du följande:

1. Kontrollera att du tittar på rätt Application Insights-resurs genom att verifiera instrumenteringsnyckeln.

2. Med tidsstämpel från Gilgit loggen kan justera filtret tidsintervall för sökningen att täcka det tidsintervallet.

Om du fortfarande inte ser ett undantag med detta ögonblicksbild-ID har inte undantagstelemetri rapporterats till Application Insights. Detta kan inträffa om programmet kraschade när det tog ögonblicksbilden men innan det rapporteras undantagstelemetri som. I det här fallet finns i loggarna App Service under `Diagnose and solve problems` att se om det fanns oväntade omstarter eller ohanterade undantag.

### <a name="edit-network-proxy-or-firewall-rules"></a>Redigera regler för proxy eller brandvägg

Om programmet ansluter till Internet via en proxyserver eller brandvägg, kan du behöva redigera regler så att ditt program kan kommunicera med Snapshot Debugger-tjänsten. Här är [en lista över IP-adresser och portar som används av Snapshot Debugger](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Nästa steg

* [Ange snappoints i din kod](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) att hämta ögonblicksbilder utan att behöva vänta ett undantag.
* [Diagnostisera undantag i dina webbappar](app-insights-asp-net-exceptions.md) förklarar hur du gör flera undantag som är synliga för Application Insights.
* [Smart identifiering](app-insights-proactive-diagnostics.md) identifierar automatiskt prestandaavvikelser.
