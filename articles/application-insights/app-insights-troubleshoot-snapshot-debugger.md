---
title: Azure Application Insights Snapshot Debugger felsökningsguide | Microsoft Docs
description: Vanliga frågor om Azure Application Insights Snapshot Debugger.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 285f42a6b52819077b92abce78c1f51756780604
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648870"
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Snapshot Debugger: Felsökningsguide

Application Insights Snapshot Debugger kan du automatiskt samla in en felsökning för ögonblicksbilder från live-webbprogram. Ögonblicksbilden visar tillståndet för källkoden och variabler för tillfället ett undantag inträffade. Den här artikeln beskriver hur felsökningsprogrammet fungerar och ger lösningar på vanliga problem.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Hur fungerar Application Insights Snapshot Debugger

Application Insights Snapshot Debugger är en del av Application Insights telemetry pipeline (en instans av ITelemetryProcessor). Snapshot Collector övervakar både undantag i koden (AppDomain.FirstChanceException) och undantagstelemetri som rapporterats till Application Insights via `TelemetryClient.TrackException`. När du har lagt till Snapshot Collector-paketet i projektet och en händelse har identifierats i telemetri på pipeline finns en anpassad händelse med namnet 'AppInsightsSnapshotCollectorLogs' och 'SnapshotCollectorEnabled' i anpassade Data Skickat. På samma gång, den börjar en process som kallas ”SnapshotUploader.exe” (version 1.2.0 eller senare) eller ”MinidumpUploader.exe” (före version 1.2.0 eller senare), ladda upp den insamlade ögonblicksbilder datafiler till Application Insights.  När gilgit-processen startar skickar den en anpassad händelse med namnet ”UploaderStart”. Efter det anger snapshot collector dess normala beteendet för övervakning.

När ögonblicksbildsinsamlaren övervakar undantagstelemetri för Application Insights, använder parametrar (till exempel ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) som definierats i den konfiguration för att avgöra när du ska samla in en ögonblicksbild. När alla regler uppfylls begär insamlaren en ögonblicksbild för nästa undantagsfelet på samma ställe. Samtidigt, kommer ett Application Insights anpassad händelse med namnet 'AppInsightsSnapshotCollectorLogs' och 'RequestSnapshots' att skickas. Eftersom kompilatorn optimerar ”Release-kod, kanske lokala variabler inte visas i insamlade ögonblicksbilden. Ögonblicksbildsinsamlaren försöker deoptimize den metod som utlöste undantaget, när det begär ögonblicksbilder. Under den här tiden kan skickas en anpassad händelse från Application Insights med namnet 'AppInsightsSnapshotCollectorLogs' och 'ProductionBreakpointsDeOptimizeMethod' i anpassade data.  När ögonblicksbilden av nästa undantag som samlas in, ska lokala variabler vara tillgänglig. När ögonblicksbilden har samlats in, kommer den reoptimize koden. 

> [!NOTE]
> Deoptimization kräver Application Insights-webbplatstillägg som ska installeras.

När en ögonblicksbild har begärts för ett specifikt undantag, kommer ögonblicksbildsinsamlaren börja övervaka ditt programs undantagshantering pipeline (AppDomain.FirstChanceException). När undantaget inträffar igen startar insamlaren en ögonblicksbild (Application Insights anpassad händelse med namnet 'AppInsightsSnapshotCollectorLogs' och 'SnapshotStart' i anpassade data). Och sedan görs en skuggkopia av processen som körs (tabellen sidan ska dupliceras). Det tar normalt 10 till 20 millisekunder. Därefter kan skickas en anpassad händelse från Application Insights med namnet 'AppInsightsSnapshotCollectorLogs' och 'SnapshotStop' i anpassade data. När den förgrenade processen skapas, kommer den totala mängden växlingsbara minnet ökas med samma belopp som växlingsbart minne för programmet som körs (arbetsminnet är mycket mindre). När din programprocessen körs normalt den skuggkopior dumpade till disk processens minne och överförs till Application Insights. När ögonblicksbilden har överförts, skickas en anpassad händelse Application Insights med namnet ”UploadSnapshotFinish”.

## <a name="is-the-snapshot-collector-working-properly"></a>Snapshot collector fungerar korrekt?

### <a name="how-to-find-snapshot-collector-logs"></a>Så här hittar du Snapshot Collector loggar
Snapshot collector loggarna skickas till Application Insights-konto om det [Snapshot Collector NuGet-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) är version 1.1.0 eller senare. Kontrollera att den *ProvideAnonymousTelemetry* inte är inställt på false (värdet är true som standard).

* Gå till Application Insights-resursen i Azure-portalen.
* Klicka på *Search* i översiktsavsnittet.
* Ange följande sträng i sökrutan:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Obs: ändra den *tidsintervall* om det behövs.

![Skärmbild av Search Snapshot Collector loggar](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Granska loggarna för Snapshot collector
När du söker efter Snapshot Collector loggar, bör det finnas 'UploadSnapshotFinish' händelser i det aktuella tidsintervallet. Om du fortfarande inte ser knappen ”Öppna Felsök ögonblicksbild' för att öppna ögonblicksbilden skicka e-postmeddelande till snapshothelp@microsoft.com med din Application Insights-Instrumentationsnyckeln.

![Skärmbild av undersöka snapshot collector loggar](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Jag kan inte hitta en ögonblicksbild till öppen
Om följande inte hjälper dig att lösa problemet, skicka e-postmeddelande till snapshothelp@microsoft.com med din Application Insights-Instrumenteringsnyckeln.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Steg 1: Kontrollera att ditt program skickar telemetridata och Undantagsdata till Application Insights
Gå till Application Insights-resurs bör du kontrollera att det finns data som skickas från ditt program.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Steg 2: Kontrollera ögonblicksbildsinsamlaren har lagts till korrekt till ditt programs Application Insights Telemetry pipeline
Om du hittar loggar i ”så här hittar du Snapshot Collector loggar” steg snapshot collector korrekt har lagts till i ditt projekt och du kan ignorera det här steget.

Om det finns inga Snapshot collector loggar kan du kontrollera följande:
* Klassiska ASP.NET-program finns i den här raden *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* i den *ApplicationInsights.config* fil.

* För ASP.NET Core-program, se till att den *ITelemetryProcessorFactory* med *SnapshotCollectorTelemetryProcessor* läggs till i *IServiceCollection* tjänster .

* Kontrollera också att du använder rätt instrumenteringsnyckeln i ditt publicerade program.

* Snapshot collector stöder inte flera instrumenteringsnycklar i ett enda program, skickar den ögonblicksbilder till instrumenteringsnyckeln för det första undantaget den iakttar.

* Om du ställer in den *InstrmentationKey* manuellt i din kod, uppdatera den *InstrumentationKey* element från den *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Steg 3: Kontrollera minidump-överföring är igång
I snapshot collector loggarna, söker du efter *UploaderStart* (typen UploaderStart i rutan Sök text). Det bör finnas en händelse när ögonblicksbildsinsamlaren övervakas det första undantaget. Kontrollera andra loggar information om den här händelsen inte finns. Ett sätt för att lösa det här problemet att starta om ditt program.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Steg 4: Kontrollera att Snapshot Collector uttryckt avsikten att samla in ögonblicksbilder
I snapshot collector loggarna, söker du efter *RequestSnapshots* (typ ```RequestSnapshots``` i rutan Sök text).  Om det inte finns något, kontrollera din konfiguration. Till exempel *ThresholdForSnapshotting*, vilket anger antalet ett specifikt undantag som kan uppstå innan den börjar samla in ögonblicksbilder.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Steg 5: Kontrollera att ögonblicksbild inte är inaktiverat på grund av minnesskydd
För att skydda ditt programs prestanda kan kommer en ögonblicksbild bara hämtas när det finns en bra minnesbuffert. Sök efter ”CannotSnapshotDueToMemoryUsage” i snapshot collector-loggar. I den anpassade händelsedata har en detaljerad orsak. Om ditt program körs i ett Azure Web Apps, vara begränsningen strikt. Eftersom Azure Web App startas om din app när vissa regler, minne är uppfyllda. Du kan försöka att skala upp din service-plan till datorer med mer minne att lösa problemet.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Steg 6: Kontrollera att ögonblicksbilder har hämtats
I snapshot collector loggarna, söker du efter ```RequestSnapshots```.  Om det inte finns, kontrollera din konfiguration. Till exempel *ThresholdForSnapshotting*, vilket anger antalet ett specifikt undantag innan du samla in en ögonblicksbild.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Steg 7: Kontrollera att ögonblicksbilderna överförs korrekt
I snapshot collector loggarna, söker du efter ```UploadSnapshotFinish```.  Om det inte finns, skicka e- snapshothelp@microsoft.com med din Application Insights-Instrumenteringsnyckeln. Om händelsen finns öppna någon av loggarna och kopiera värdet 'SnapshotId' i anpassade Data. Sök sedan efter värdet. Det finns undantag för ögonblicksbilden. Klicka på undantag och öppna ögonblicksbild för felsökning. (Om det finns inget motsvarande undantag måste undantagsfel telemetri prov på grund av hög volym. Prova en annan snapshotId.)

![Skärmbild av hitta SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Skärmbild av öppna undantag](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Skärmbild av öppna ögonblicksbild för felsökning](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Ögonblicksbild Visa lokala variabler är inte klar

Vissa lokala variabler saknas. Om ditt program körs viktig kod, optimerar kompilatorn några variabler som är direkt. Exempel:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Om ditt ärende skiljer, kan det tyda på en bugg. Skicka e- snapshothelp@microsoft.com med din Application Insights-Instrumentationsnyckeln tillsammans med kodfragmentet.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Ögonblicksbildvyn: Det går inte att hämta värdet för lokal variabel eller argument
Kontrollera att den [Application Insights-webbplatstillägg](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) är installerad. Om problemet kvarstår kan du skicka e-postmeddelande till snapshothelp@microsoft.com med din Application Insights-Instrumenteringsnyckeln.
