---
title: Felsöka problem med Azure Application Insights Snapshot Debugger | Microsoft Docs
description: Den här artikeln beskriver vi felsökning steg och information som hjälper utvecklare som har problem med att aktivera eller med hjälp av Application Insights Snapshot Debugger.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: bf19d4f5ce60411413c21fce12f9fe9d2f391bf1
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2019
ms.locfileid: "58094947"
---
# <a id="troubleshooting"></a> Felsöka problem med att aktivera Application Insights Snapshot Debugger och visa ögonblicksbilder
Om du aktiverade Application Insights Snapshot Debugger för ditt program, men inte ser ögonblicksbilder för undantag, kan du använda de här anvisningarna för att felsöka. Det kan finnas många olika skäl varför ögonblicksbilder inte genereras. Du kan köra hälsokontroller ögonblicksbild för att identifiera möjliga vanliga orsaker.

## <a name="use-the-snapshot-health-check"></a>Använd ögonblicksbild-hälsokontroll
Flera vanliga problem som kan leda till öppen Felsök ögonblicksbilden inte visas. Med hjälp av en inaktuell Snapshot Collector, till exempel nå daglig uppladdningsgräns; eller kanske ögonblicksbilden tar bara lång tid att ladda upp. Använd ögonblicksbild hälsokontrollen för att felsöka vanliga problem.

Det finns en länk i fönstret undantag av vyn slutpunkt till slutpunkt-spårning som leder till ögonblicksbild hälsokontrollen.

![Ange ögonblicksbild-hälsokontroll](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktiv, chatt-liknande användargränssnitt söker efter vanliga problem och hjälper dig att åtgärda dem.

![Hälsokontroll](./media/snapshot-debugger/healthcheck.png)

Om det inte löser problemet, referera till följande manuella åtgärder för felsökning.

## <a name="verify-the-instrumentation-key"></a>Kontrollera instrumenteringsnyckeln

Kontrollera att du använder rätt instrumenteringsnyckeln i ditt publicerade program. Instrumenteringsnyckeln är vanligtvis läsa från filen ApplicationInsights.config. Kontrollera att värdet är samma som instrumenteringsnyckeln för Application Insights-resursen som visas i portalen.

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uppgradera till den senaste versionen av NuGet-paketet

Om Snapshot Debugger aktiverades via den [Application Insights-fönstret i portalen](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), och sedan ditt program bör köras senaste NuGet-paketet. Om Snapshot Debugger har aktiverats genom att inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet, Använd Visual Studio NuGet Package Manager för att kontrollera att du använder den senaste versionen av Microsoft.ApplicationInsights.SnapshotCollector. Viktig information finns på https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Kontrollera loggarna överförare

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

## <a name="troubleshooting-cloud-services"></a>Felsöka Cloud Services
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

## <a name="overriding-the-shadow-copy-folder"></a>Åsidosätta mappen Shadow Copy

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Använd Application Insights-sökning för att hitta undantag med ögonblicksbilder

När en ögonblicksbild skapas, är utlösande undantaget taggade med en ögonblicksbild-ID. Detta ID för ögonblicksbild ingår som en anpassad egenskap när undantagstelemetri som som har rapporterats till Application Insights. Med hjälp av **Search** i Application Insights, hittar du all telemetri med den `ai.snapshot.id` anpassad egenskap.

1. Bläddra till Application Insights-resursen i Azure-portalen.
2. Klicka på **Sök**.
3. Typ `ai.snapshot.id` i sökrutan och tryck på RETUR.

![Sök efter telemetri med en ögonblicksbild-ID i portalen](./media/snapshot-debugger/search-snapshot-portal.png)

Om den här sökningen returnerar inga resultat, har inga ögonblicksbilder rapporterats till Application Insights för ditt program i det valda tidsintervallet.

För att söka efter en specifik ögonblicksbild-ID från Gilgit-loggar, skriver du detta ID i sökrutan. Om du inte hittar telemetri för en ögonblicksbild som du vet har överförts, gör du följande:

1. Kontrollera att du tittar på rätt Application Insights-resurs genom att verifiera instrumenteringsnyckeln.

2. Med tidsstämpel från Gilgit loggen kan justera filtret tidsintervall för sökningen att täcka det tidsintervallet.

Om du fortfarande inte ser ett undantag med detta ögonblicksbild-ID har inte undantagstelemetri rapporterats till Application Insights. Detta kan inträffa om programmet kraschade när det tog ögonblicksbilden men innan det rapporteras undantagstelemetri som. I det här fallet finns i loggarna App Service under `Diagnose and solve problems` att se om det fanns oväntade omstarter eller ohanterade undantag.

## <a name="edit-network-proxy-or-firewall-rules"></a>Redigera regler för proxy eller brandvägg

Om programmet ansluter till Internet via en proxyserver eller brandvägg, kan du behöva redigera regler så att ditt program kan kommunicera med Snapshot Debugger-tjänsten. Här är [en lista över IP-adresser och portar som används av Snapshot Debugger](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
