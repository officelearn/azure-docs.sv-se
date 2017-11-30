---
title: "Azure Application Insights ögonblicksbild felsökare felsökningsguide för | Microsoft Docs"
description: "Vanliga frågor om Azure Application Insights ögonblicksbild felsökare."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Ögonblicksbild felsökare: Felsökningsguide

Application Insights ögonblicksbild felsökaren kan du automatiskt samla in en ögonblicksbild för felsökning från live webbprogram. Ögonblicksbilden visar tillståndet för källkoden och variabler för tillfället ett undantag utlöstes. Om du har problem med Application Insights ögonblicksbild felsökningsprogrammet komma och kör den här artikeln vägleder dig igenom hur felsökaren fungerar tillsammans med lösningar på vanliga scenarier för felsökning. 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Hur fungerar Application Insights ögonblicksbild felsökare

Application Insights ögonblicksbild felsökare är en del av Application Insights telemetri pipeline (en instans av ITelemetryProcessor), ögonblicksbild insamlaren övervakar både undantag i koden (AppDomain.FirstChanceException) och undantagen som hämta spåras av Undantagstelemetri för Application Insights-pipeline. När ögonblicksbilden insamlaren har lagts till i ditt projekt och upptäckte ett undantag i Application Insights telemetri pipeline Application Insights anpassade händelse med namnet 'AppInsightsSnapshotCollectorLogs' och ' SnapshotCollectorEnabled' i anpassade Data skickas. På samma gång startar det en process med namnet på 'MinidumpUploader.exe' att överföra datafiler insamlade ögonblicksbilder till Application Insights.  När MinidumpUploader.exe bearbeta startar, skickas en anpassad händelse med namnet 'UploaderStart'. När de föregående stegen anger ögonblicksbild insamlaren dess övervakning normalt.

När ögonblicksbilden insamlaren övervakar undantagstelemetri för Application Insights, använder parametrar (t.ex, ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) som definierats i den konfigurationen för att avgöra när du ska samla in en ögonblicksbild. När alla regler är uppfyllda begär insamlaren en ögonblicksbild för nästa undantagsfel på samma ställe. Samtidigt, kommer en Application Insights anpassade händelsen med namnet 'AppInsightsSnapshotCollectorLogs' och 'RequestSnapshots' att skickas. Eftersom kompilatorn optimerar 'Release-kod, kanske lokala variabler inte visas i den insamlade ögonblicksbilden. Ögonblicksbild insamlaren försöker deoptimize metoden som utlöste undantaget, vid begäran om ögonblicksbilder. Under den här tiden kan skickas en anpassad händelse från Application Insights med namnet 'AppInsightsSnapshotCollectorLogs' och 'ProductionBreakpointsDeOptimizeMethod' i anpassade data.  När ögonblicksbilden av nästa undantag som samlas in, blir lokala variabler tillgängliga. När ögonblicksbilden har samlats in, kommer den reoptimize koden för att säkerställa prestanda. 

> [!NOTE]
> Deoptimization kräver filnamnstillägget Application Insights platsen installeras.

När en ögonblicksbild har begärts för ett specifikt undantag startar ögonblicksbild insamlaren övervakning av programmets undantagshantering pipeline (AppDomain.FirstChanceException). När undantaget inträffar igen startar insamlaren en ögonblicksbild (Application Insights anpassade händelsen med namnet 'AppInsightsSnapshotCollectorLogs' och 'SnapshotStart' i anpassade data). Sedan görs en skuggkopia av processen körs (tabellen sidan dupliceras). Detta tar normalt 10 till 20 millisekunder. Därefter kan skickas en Application Insights anpassade händelsen med namnet 'AppInsightsSnapshotCollectorLogs' och 'SnapshotStop' i anpassade data. När andelen vridvuxna process skapas, ökas det totala växlingsbara minnet med samma mängd som växlingsbara minnet för tillämpningsprogrammet körs (arbetsminnet är mycket mindre). När din programprocessen körs normalt skuggan kopieras dumpade till disk processens minne och överförs till Application Insights. När ögonblicksbilden överförs kommer Application Insights anpassade händelse med namnet 'UploadSnapshotFinish' att skickas.

## <a name="is-the-snapshot-collector-working-properly"></a>Ögonblicksbild insamlaren fungerar?

### <a name="how-to-find-snapshot-collector-logs"></a>Hur du söker efter ögonblicksbild insamlaren loggar
Ögonblicksbild insamlaren loggar skickas till Application Insights-konto om det [ögonblicksbild insamlaren NuGet-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) är version 1.1.0 eller senare är installerat. Kontrollera att den *ProvideAnonymousTelemetry* är inte inställd på false (värdet är true som standard).

* Gå till Application Insights-resurs i Azure-portalen.
* Klicka på *Sök* i översiktsavsnittet.
* Ange följande sträng i sökrutan:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Obs: ändrar den *tidsintervallet* om det behövs.

![Skärmbild av Sök ögonblicksbild insamlaren loggar](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Granska loggarna för ögonblicksbild insamlaren
När du söker efter ögonblicksbild insamlaren loggar kan finnas det 'UploadSnapshotFinish' händelser i tidsintervallet. Om du fortfarande inte ser 'Öppna ögonblicksbild Debug-knappen för att öppna ögonblicksbilden skickar du ett e-postmeddelande till snapshothelp@microsoft.com med din Programinsikter Instrumentation nyckel.

![Skärmbild av granska loggarna för ögonblicksbild insamlaren](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Du kan inte hitta en ögonblicksbild på Öppna
Om följande inte hjälper dig att lösa problemet, skickar du ett e-postmeddelande till snapshothelp@microsoft.com med din Programinsikter Instrumentation nyckel.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Steg 1: Kontrollera att ditt program skickar telemetridata och Undantagsdata till Application Insights
Navigera till Application Insights-resurs, kontrollera att det finns data som skickas från ditt program.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Steg 2: Kontrollera att ögonblicksbild insamlaren läggs korrekt för ditt program Application Insights Telemetry pipeline
Du kan ignorera det här steget om du kan hitta loggar i steget, hur du söker efter ögonblicksbild insamlaren loggar' ögonblicksbild insamlaren korrekt har lagts till i ditt projekt.

Om det finns inga ögonblicksbilder insamlaren loggar kan du kontrollera följande:
* Sök efter raden för klassiska ASP.NET-program,  *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">*  i den *ApplicationInsights.config* fil.

* För ASP.NET viktiga program, kontrollera att den *ITelemetryProcessorFactory* med *SnapshotCollectorTelemetryProcessor* har lagts till i *IServiceCollection* tjänster .

* Kontrollera också att du använder rätt instrumentation nyckeln i ditt publicerade program.

* Ögonblicksbild insamlaren har inte stöd för flera instrumentation nycklar i ett enda program, skickar ögonblicksbilder till instrumentation nyckeln för det första undantaget som den registrerar.

* Om du ställer in den *InstrmentationKey* manuellt i din kod, uppdatera det *InstrumentationKey* element från den *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Steg 3: Kontrollera minidump-överföring är igång
I ögonblicksbild insamlaren loggarna, söker du efter *UploaderStart* (typen UploaderStart i sökrutan). Det bör finnas en händelse när ögonblicksbilden insamlaren övervakas det första undantaget. Kontrollera andra loggar information om den här händelsen inte finns. Ett sätt för att lösa det här problemet att starta om programmet.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Steg 4: Kontrollera ögonblicksbild insamlaren uttryckt sin avsikt att samla in ögonblicksbilderna
I ögonblicksbild insamlaren loggarna, söker du efter *RequestSnapshots* (typen ```RequestSnapshots``` i sökrutan).  Om det inte finns någon Kontrollera konfigurationen, t.ex. *ThresholdForSnapshotting* som anger antalet en särskilda undantag som kan uppstå innan den börjar samla ögonblicksbilder.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Steg 5: Kontrollera att ögonblicksbilden inte har inaktiverats på grund av minnesskydd
För att skydda ditt programs prestanda, kommer en ögonblicksbild bara hämtas när det är en bra minnesbuffert. I ögonblicksbild insamlaren loggarna, söka efter 'CannotSnapshotDueToMemoryUsage'. Händelsens anpassade data har en detaljerad orsak. Om programmet körs i ett Azure Web App vara begränsningen strikt. Eftersom Azure Web App startar om din app när regler minne är uppfyllda. Du kan försöka att skala upp din serviceplan till datorer med mer minne för att lösa problemet.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Steg 6: Kontrollera att ögonblicksbilder har hämtats
I ögonblicksbild insamlaren loggarna, söker du efter ```RequestSnapshots```.  Om ingen är tillgänglig, kontrollera konfigurationen, t.ex. ```ThresholdForSnapshotting``` detta anger antalet särskilda undantag innan samlas in en ögonblicksbild.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Steg 7: Kontrollera att ögonblicksbilderna överförs korrekt
I ögonblicksbild insamlaren loggarna, söker du efter ```UploadSnapshotFinish```.  Om det inte finns, skicka e-postmeddelande till snapshothelp@microsoft.com med din Programinsikter Instrumentation nyckel. Om händelsen finns öppna någon av loggarna och kopiera 'SnapshotId'-värde i anpassade Data. Sök sedan efter värdet. Detta kommer att hitta undantag som motsvarar ögonblicksbilden. Klicka på undantag och öppna debug ögonblicksbild. (Om det finns inget motsvarande undantag, undantagstelemetri kanske provtagning, på grund av hög volym, försök med en annan snapshotId.)

![Skärmbild av Sök SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Skärmbild av öppna undantag](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Skärmbild av öppna debug ögonblicksbild](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Ögonblicksbild Visa lokala variabler är inte sluförda

Vissa av lokala variabler saknas. Om programmet körs viktig kod optimerar kompileraren några variabler som är direkt. Exempel:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Om ditt fall skiljer sig, kan det vara ett programfel. Skicka e-postmeddelande till snapshothelp@microsoft.com med din Programinsikter Instrumentation nyckel tillsammans med kodfragmentet.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Visa ögonblicksbild: Går inte att hämta värdet för den lokala variabeln eller argumentet
Kontrollera att den [Application Insights plats tillägget](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) är installerad. Om problemet kvarstår skickar du ett e-postmeddelande till snapshothelp@microsoft.com med din Programinsikter Instrumentation nyckel.
