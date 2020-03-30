---
title: Felsöka ögonblicksbildfelsökare för Azure Application Insights
description: Den här artikeln innehåller felsökningssteg och information som hjälper utvecklare som har problem med att aktivera eller använda Snapshot Debugger för Application Insights.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671417"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Felsöka problem med att aktivera ögonblicksbildfelsökare för ögonblicksbilder av programinsikter eller visa ögonblicksbilder
Om du har aktiverat Snapshot Debugger för programmet för program, men inte ser ögonblicksbilder för undantag, kan du använda dessa instruktioner för felsökning. Det kan finnas många olika orsaker till att ögonblicksbilder inte genereras. Du kan köra hälsokontrollen för ögonblicksbilder för att identifiera några av de möjliga vanliga orsakerna.

## <a name="use-the-snapshot-health-check"></a>Använd hälsokontrollen för ögonblicksbilder
Flera vanliga problem resulterar i att open debug snapshot inte visas. Använda en inaktuell Snapshot Collector, till exempel; når den dagliga uppladdningsgränsen. eller kanske ögonblicksbilden tar bara lång tid att ladda upp. Använd hälsokontrollen ögonblicksbild för att felsöka vanliga problem.

Det finns en länk i undantagsfönstret i spårningsvyn från slutna till slutna händer som tar dig till hälsokontrollen Ögonblicksbild.

![Ange hälsokontroll av ögonblicksbilder](./media/snapshot-debugger/enter-snapshot-health-check.png)

Det interaktiva, chattliknande gränssnittet letar efter vanliga problem och hjälper dig att åtgärda dem.

![Hälsokontroll](./media/snapshot-debugger/healthcheck.png)

Om det inte löser problemet läser du följande steg för manuell felsökning.

## <a name="verify-the-instrumentation-key"></a>Verifiera instrumenteringsnyckeln

Kontrollera att du använder rätt instrumenteringsnyckel i det publicerade programmet. Vanligtvis läss instrumentnyckeln från filen ApplicationInsights.config. Kontrollera att värdet är detsamma som instrumenteringsnyckeln för application insights-resursen som visas i portalen.

## <a name="preview-versions-of-net-core"></a>Förhandsgranska versioner av .NET Core
Om programmet använder en förhandsversion av .NET Core och Ögonblicksbildfelsökare har aktiverats via [fönstret Programinsikter](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) i portalen, kanske ögonblicksbildfelsökaren inte startar. Följ instruktionerna på [Aktivera felsökare för ögonblicksbilder för andra miljöer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som först ska inkludera paketet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet med programmet ***förutom*** att aktivera via fönstret [Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uppgradera till den senaste versionen av NuGet-paketet

Om Snapshot Debugger aktiverades via [fönstret Programstatistik i portalen](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)bör ditt program redan köra det senaste NuGet-paketet. Om Snapshot Debugger aktiverades genom att inkludera [Paketet Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet använder du Visual Studios NuGet Package Manager för att kontrollera att du använder den senaste versionen av Microsoft.ApplicationInsights.SnapshotCollector. Viktig information finns påhttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Kontrollera uppladdaren loggar

När en ögonblicksbild har skapats skapas en minidumpfil (.dmp) på disken. En separat uppladdare process skapar att minidump fil och laddar upp den, tillsammans med eventuella associerade PDBs, till Application Insights Snapshot Debugger lagring. När minidumpen har laddats upp tas den bort från disken. Loggfilerna för uppladdaren förs på disken. I en App Service-miljö hittar du `D:\Home\LogFiles`dessa loggar i . Använd Kudu-hanteringsplatsen för App Service för att hitta dessa loggfiler.

1. Öppna ditt App Service-program i Azure-portalen.
2. Klicka på **Avancerade verktyg**eller sök efter **Kudu**.
3. Klicka på **Gå**.
4. Välj **CMD**i listrutan **Felsökningskonsol** .
5. Klicka på **Loggfiler**.

Du bör se minst en fil med `Uploader_` ett `SnapshotUploader_` namn `.log` som börjar med eller och ett tillägg. Klicka på lämplig ikon för att hämta loggfiler eller öppna dem i en webbläsare.
Filnamnet innehåller ett unikt suffix som identifierar App Service-instansen. Om apptjänstinstansen finns på mer än en dator finns det separata loggfiler för varje dator. När uppladdaren upptäcker en ny minidumpfil registreras den i loggfilen. Här är ett exempel på en lyckad ögonblicksbild och uppladdning:

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
> Exemplet ovan är från version 1.2.0 av Paketet Microsoft.ApplicationInsights.SnapshotCollector NuGet. I tidigare versioner anropas `MinidumpUploader.exe` uppladdaren och loggen är mindre detaljerad.

I föregående exempel är `c12a605e73c44346a984e00000000000`instrumenteringsnyckeln . Det här värdet bör matcha instrumenteringsnyckeln för ditt program.
Minidumpen är associerad med en `139e411a23934dc0b9ea08a626db16c5`ögonblicksbild med ID. Du kan använda det här ID:t senare för att hitta den associerade undantagstelemetrin i Application Insights Analytics.

Uppladdaren söker efter nya PDBs ungefär en gång var 15 minuter. Här är ett exempel:

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

För program som _inte finns_ i App Service finns uppladdarens loggar i samma mapp `%TEMP%\Dumps\<ikey>` som `<ikey>` minidumparna: (var är instrumenteringsnyckeln).

## <a name="troubleshooting-cloud-services"></a>Felsöka molntjänster
För roller i Molntjänster kan standardmappen för tillfällig mapp vara för liten för att innehålla minidumpfilerna, vilket leder till förlorade ögonblicksbilder.
Vilket utrymme som behövs beror på den totala arbetsuppsättningen för ditt program och antalet samtidiga ögonblicksbilder.
Arbetsminnet för en 32-bitars ASP.NET webbroll är vanligtvis mellan 200 MB och 500 MB.
Tillåt minst två samtidiga ögonblicksbilder.
Om programmet till exempel använder 1 GB totalt arbetsminne bör du se till att det finns minst 2 GB diskutrymme för att lagra ögonblicksbilder.
Följ dessa steg för att konfigurera din Cloud Service-roll med en dedikerad lokal resurs för ögonblicksbilder.

1. Lägg till en ny lokal resurs i molntjänsten genom att redigera filen Cloud Service definition (.csdef). I följande exempel definieras `SnapshotStore` en resurs som anropas med storleken 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Ändra rollens startkod för att lägga till `SnapshotStore` en miljövariabel som pekar på den lokala resursen. För arbetarroller ska koden läggas till `OnStart` i rollens metod:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   För webbroller (ASP.NET) ska koden läggas till i `Application_Start` webbprogrammets metod:
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

3. Uppdatera filen ApplicationInsights.config för att åsidosätta den tillfälliga mappplatsen som används av`SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Åsidosätta mappen Skuggkopia

När Snapshot Collector startar försöker den hitta en mapp på disk som är lämplig för att köra processen för uppladdare för ögonblicksbilder. Den valda mappen kallas mappen Skuggkopia.

Snapshot Collector kontrollerar några välkända platser och ser till att den har behörighet att kopiera binärfilerna för ögonblicksbilduppladdare. Följande miljövariabler används:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- Appdata
- Temp

Om det inte går att hitta en lämplig mapp rapporterar Snapshot Collector ett felmeddelande _med texten "Det gick inte att hitta en lämplig skuggkopiamapp"._

Om kopian misslyckas rapporterar Snapshot Collector ett `ShadowCopyFailed` fel.

Om uppladdaren inte kan startas rapporterar `UploaderCannotStartFromShadowCopy` Snapshot Collector ett fel. Meddelandets brödtext innehåller `System.UnauthorizedAccessException`ofta . Det här felet uppstår vanligtvis eftersom programmet körs under ett konto med nedsatt behörighet. Kontot har behörighet att skriva till skuggkopieringsmappen, men har inte behörighet att köra kod.

Eftersom dessa fel vanligtvis inträffar under start, kommer `ExceptionDuringConnect` de vanligtvis att följas av ett felmeddelande som säger _"Uploader misslyckades med att starta."_

Om du vill lösa dessa fel kan du ange `ShadowCopyFolder` skuggkopieringsmappen manuellt via konfigurationsalternativet. Till exempel med ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Eller om du använder appsettings.json med ett .NET Core-program:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Använd application insights-sökning för att hitta undantag med ögonblicksbilder

När en ögonblicksbild skapas taggas det kasta undantaget med ett ögonblicksbild-ID. Det ögonblicksbild-ID:t ingår som en anpassad egenskap när undantagstelemetrin rapporteras till Application Insights. Med **Sök** i Application Insights hittar du all `ai.snapshot.id` telemetri med den anpassade egenskapen.

1. Bläddra till din Application Insights-resurs i Azure-portalen.
2. Klicka på **Sök**.
3. Skriv `ai.snapshot.id` i textrutan Sök och tryck på Retur.

![Sök efter telemetri med ett ögonblicksbild-ID i portalen](./media/snapshot-debugger/search-snapshot-portal.png)

Om sökningen inte returnerar några resultat har inga ögonblicksbilder rapporterats till Application Insights för ditt program i det valda tidsintervallet.

Om du vill söka efter ett visst snapshot-ID från Uploader-loggarna skriver du det ID:et i sökrutan. Om du inte hittar telemetri för en ögonblicksbild som du vet har överförts gör du så här:

1. Dubbelkolla att du tittar på rätt Application Insights-resurs genom att verifiera instrumenteringsnyckeln.

2. Justera filtret Tidsintervall för sökningen med hjälp av tidsstämpeln från uppladdarens logg.

Om du fortfarande inte ser ett undantag med det ögonblicksbild-ID, rapporterades inte undantagstelemetrin till Application Insights. Den här situationen kan inträffa om ditt program kraschade efter att det tog ögonblicksbilden men innan den rapporterade undantagstelemetrin. I det här fallet kontrollerar `Diagnose and solve problems` du apptjänstloggarna under för att se om det fanns oväntade omstarter eller ohanterade undantag.

## <a name="edit-network-proxy-or-firewall-rules"></a>Redigera nätverksproxy- eller brandväggsregler

Om ditt program ansluter till Internet via en proxy eller en brandvägg kan du behöva redigera reglerna så att ditt program kan kommunicera med tjänsten Ögonblicksbildfelsökning. De IPs som används av Snapshot Debugger ingår i Azure Monitor-tjänsttaggen.
