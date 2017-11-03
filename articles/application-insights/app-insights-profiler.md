---
title: "Profilering live-webbappar på Azure med Application Insights | Microsoft Docs"
description: "Identifiera hot sökvägen i koden web server med en låg storleken profiler."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: f6669d90878398dcd4592df97180dcd59b146350
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="profiling-live-azure-web-apps-with-application-insights"></a>Profilering live Azure-webbappar med Application Insights

*Den här funktionen av Application Insights är GA för Apptjänster och förhandsgranskning för beräkning.*

Ta reda på hur mycket tid läggs på varje metod i webbprogrammet live med hjälp av verktyget profilering av [Azure Application Insights](app-insights-overview.md). Den visas detaljerad profiler för live-frågor som har hanteras av din app och markeras 'varm sökvägen' som används mest tid. Väljs automatiskt exempel som har olika svarstider. Profileraren använder olika metoder för att minimera kostnader.

Profileraren fungerar för närvarande för ASP.NET-webbprogram som körs på Azure App Service i minst den grundläggande prisnivån.

<a id="installation"></a>
## <a name="enable-the-profiler"></a>Aktivera profileraren

[Installera Application Insights](app-insights-asp-net.md) i koden. Om den redan är installerad, kontrollera att du har den senaste versionen. (Att göra detta, högerklicka på projektet i Solution Explorer och välja hantera NuGet-paket. Välj uppdateringar och alla uppdateringspaket.) Omdistribuera din app.

*Med hjälp av ASP.NET Core? [Markera den här kryssrutan](#aspnetcore).*

I [https://portal.azure.com](https://portal.azure.com), öppna Application Insights-resurs för webbappen. Öppna **prestanda** och på **aktivera Application Insights Profiler...** .

![Klicka på Aktivera profileraren banderoll][enable-profiler-banner]

Du kan alltid Klicka **konfigurera** för att visa status, aktivera eller inaktivera profileraren.

![Klicka på Konfigurera i bladet prestanda][performance-blade]

Webbprogram som är konfigurerade med Application Insights visas på Konfigurera bladet. Följ instruktionerna för att installera agenten Profiler om det behövs. Om inget webbprogram har konfigurerats med Application Insights ännu, klickar du på *Lägg till länkade appar*.

Använd den *aktivera profileraren* eller *inaktivera profileraren* knapparna i bladet konfigurera att styra Profiler på alla dina länkade webbprogram.

![Konfigurera bladet][linked app services]

Till skillnad från web apps värd via App Service-planer program finns i *Azure Compute* resurser (t.ex.: virtuella datorer, virtuella datorn, Service Fabric, Cloud Services) som inte hanteras direkt av Azure. I detta fall det inte finns några webbprogram för att länka hit och du behöver bara klicka för att aktivera Profiler på skärmen.

## <a name="disable-the-profiler"></a>Inaktivera profileraren
För att stoppa eller starta om profiler för en enskild App Service-instans, hittar du den **i Apptjänst resursen**i **Webjobs**. Om du vill ta bort den, tittar du under **tillägg**.

![Inaktivera profiler för en webb-jobb][disable-profiler-webjob]

Vi rekommenderar att du har den profilerare som är aktiverad på alla dina webbprogram för att identifiera eventuella prestandaproblem så snart som möjligt.

Om du använder WebDeploy för att distribuera ändringarna till ditt webbprogram, kontrollera att du utesluter den **App_Data** mappen tas bort under distributionen. Annars tillägget profileraren filer tas bort när du sedan distribuera webbappen till Azure.

### <a name="using-profiler-with-azure-vms-and-compute-resources-preview"></a>Med hjälp av profiler med Azure Virtual Machines och beräkningsresurser (förhandsgranskning)

När du [aktivera Application Insights för Azure app Service vid körning](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), Profiler är automatiskt tillgänglig. (Om du redan har aktiverat Programinsikter för resursen du kan behöva uppdatera till den senaste versionen genom den **konfigurera** guiden.)

Det finns en [förhandsversionen av profileraren för Azure Compute resurser](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Begränsningar

Standard-datalagring är 5 dagar. Maximal 10 GB inhämtas per dag.

Det är gratis för tjänsten profiler. Ditt webbprogram måste vara finns i den grundläggande nivån av App-tjänster.

## <a name="overhead-and-sampling-algorithm"></a>Kostnader och provtagning algoritm

Profileraren körs slumpmässigt 2 minuter varje timme på varje virtuell dator som värd för programmet med Profiler som är aktiverade för att fånga in spårningar. När profileraren körs läggs mellan 5 till 15% CPU-belastning på servern.
Flera servrarna tillgänglig som värd för programmet, mindre påverkan profileraren har på den övergripande prestandan. Detta eftersom algoritmen provtagning resulterar i profiler som körs på bara körs på 5% av servrarna vid en given tidpunkt och flera servrar kommer att kunna hantera webbegäranden till förskjutning servrar med kostnader från profileraren.


## <a name="viewing-profiler-data"></a>Visa profiler data

Öppna bladet för prestanda och rulla ned till listan med åtgärden.




![Application Insights prestanda bladet exempel kolumn][performance-blade-examples]

Kolumner i tabellen är:

* **Antal** -antal dessa begäranden i tidsintervallet på bladet.
* **Medianvärdet** – typiska överföringstiden för din app svara på en begäran. Hälften av alla svar var snabbare än så.
* **95: e percentilen** 95% av svar var snabbare än så. Om denna bild är bland annat medianvärdet, kan det finnas ett tillfälligt problem med din app. (Eller det kan förklaras av en design-funktion, till exempel cachelagring.)
* **Profiler-spårningar** -en ikon som indikerar att profileraren har hämtats stackspår för den här åtgärden.

Klicka på knappen Visa öppna trace explorer. Utforskaren innehåller flera exempel att profileraren har hämtats, klassificeras efter svarstid.

Om du använder Preview prestanda bladet går du till **vidta åtgärder** avsnitt i det nedre högra hörnet att visa profiler-spårningar. Klicka Profiler-spårningar.

![Application Insights prestanda bladet preview Profiler-spårningar][performance-blade-v2-examples]

Välj ett exempel för att visa en kod på objektnivå uppdelning av tid köra begäran.

![Application Insights Trace Explorer][trace-explorer]

**Visa varm sökvägen** öppnas den största löv nod eller Stäng minst något. I de flesta fall den här noden inte gränsar till en prestandabegränsning.



* **Etiketten**: namnet på den funktion eller en händelse. Trädet innehåller en blandning av koden och händelser som inträffade (till exempel SQL- och HTTP-händelser). Händelsen översta representerar varaktighet för begäran.
* **Förfluten tid**: tidsintervallet mellan start av åtgärden och slut.
* **När**: Visar när funktionen/händelsen körs i förhållande till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestandadata

Microsoft service profiler använder en kombination av urvalsmetoden och instrumentation för att analysera prestandan för ditt program.
När detaljerad samling pågår prover service profiler instruktionspekaren för den datorn CPU i varje millisekunder.
Varje prov avbildar fullständig anropsstacken för tråden för närvarande körs, ger detaljerad och användbar information om vad som tråden som utfördes på båda hög och låg gjord. Service profiler samlar även in andra händelser, till exempel kontexten växling händelser, TPL händelser och trådpoolen händelser för att spåra aktivitet korrelation och orsakssamband.

Anropsstacken visas i tidslinjevyn är resultatet av provtagning och instrumentation ovan. Eftersom varje prov fångar fullständig anropsstacken trådens, inkluderar den kod från .NET framework, samt andra ramverk som du refererar till.

### <a id="jitnewobj"></a>Objekt-allokering (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1`är hjälpfunktioner i .NET framework som allokerar minne från hanterad heap. `clr!JIT\_New`anropas när ett objekt har allokerats. `clr!JIT\_Newarr1`anropas när en objektmatris allokeras. Dessa två funktioner är vanligtvis mycket snabbt och bör ta relativt små lång tid. Om du ser `clr!JIT\_New` eller `clr!JIT\_Newarr1` ta en lång tid i tidslinjen, det är en indikation på att koden fördela många objekt och förbruka mycket minne.

### <a id="theprestub"></a>Läser in kod (`clr!ThePreStub`)
`clr!ThePreStub`är en hjälpfunktion i .NET framework som förbereder koden ska köras för första gången. Detta innefattar vanligtvis sådana, men inte begränsat till, JIT (Just i tid)-kompilering. För varje C#-metoden `clr!ThePreStub` ska anropas en gång under livslängden för en process.

Om du ser `clr!ThePreStub` tar lång tid en begäran anger att begäran är den första som kör den metoden och tiden för körningen av .NET framework att läsa in den här metoden är viktig. Du kan överväga en värmts process som körs som del av koden innan användarna åtkomst till den eller Överväg att köra NGen på din sammansättningar.

### <a id="lockcontention"></a>Låskonkurrens (`clr!JITutil\_MonContention` eller `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention`eller `clr!JITutil\_MonEnterWorker` indikerar den aktuella tråden väntar på att en låsas upp. Detta normalt visas när du kör en C# lock-instruktion, startar Monitor.Enter metod eller anropa en metod med attributet MethodImplOptions.Synchronized. Låskonkurrens inträffar oftast när tråden A hämtar ett lås och tråd B försöker hämta samma låset innan tråd A släpper den.

### <a id="ngencold"></a>Läser in kod (`[COLD]`)
Om metodnamnet innehåller `[COLD]`, som `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`, innebär det att .NET framework-körningen kör kod som inte är optimerad av <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil interaktiv optimering</a> för första gången. För varje metod ska det visas högst en gång under livslängden för processen.

Om inläsning av koden tar lång tid en begäran, anger att begäran är den första att köra den icke-optimerad delen av metoden. Du kan överväga en varmt in process som körs som del av koden innan användarna åtkomst till den.

### <a id="httpclientsend"></a>Skicka HTTP-begäran
Metoder som `HttpClient.Send` ange koden som väntar på att en HTTP-begäran att slutföra.

### <a id="sqlcommand"></a>Databasåtgärden
Metoden, till exempel SqlCommand.Execute anger koden väntar på en databasåtgärd ska slutföras.

### <a id="await"></a>Väntar på (`AWAIT\_TIME`)
`AWAIT\_TIME`Anger kod som väntar på en annan aktivitet ska slutföras. Detta inträffar vanligtvis med C#-await-instruktionen. När koden har en C#-await-tråden unwinds och returnerar kontrollen till trådpoolen och det finns ingen tråd blockeras väntar på 'await' om du vill avsluta. Dock blockeras logiskt tråden som gjorde await' ”väntar på att åtgärden har slutförts. Den `AWAIT\_TIME` anger blockerade väntar på att åtgärden har slutförts.

### <a id="block"></a>Blockerade tid
`BLOCKED_TIME`Anger kod som väntar på en annan resursen ska vara tillgängliga, till exempel väntar på ett synkroniseringsobjekt väntar på en tråd ska vara tillgängliga eller väntar på att en begäran om att avsluta.

### <a id="cpu"></a>CPU-tid
Processorn är upptagen med att köra instruktionerna.

### <a id="disk"></a>Disktid i procent
Programmet utför diskåtgärder.

### <a id="network"></a>Network Time
Programmet utför nätverksåtgärder.

### <a id="when"></a>När kolumnen
Det här är en visualisering av hur INKLUSIVA exemplen som samlas in för en nod variera över tid. Det totala intervallet för begäran är uppdelat i 32 tid buckets och inklusiva exemplen för noden ackumuleras till dessa 32 buckets. Varje bucket sedan representeras som en stapel vars höjd representerar ett skalade värde. För markerade noder `CPU_TIME` eller `BLOCKED_TIME`, eller om det finns en tydlig relationen mellan förbrukar en resurs (cpu, disk, tråd), i fältet representerar förbrukar en av dessa resurser för att bucket tidsperiod. De här måtten kan du få mer än 100% genom att använda flera resurser. Till exempel om i genomsnitt du använder två processorer via ett intervall som får sedan du 200%.


## <a id="troubleshooting"></a>Felsökning

### <a name="too-many-active-profiling-sessions"></a>För många aktiva profilering sessioner

För närvarande kan du aktivera profiler på högst 4 Azure Web Apps och distributionsplatser som körs på samma service-plan. Om du ser profileraren web jobbet reporting för många aktiva sessioner för vilka behöver du flytta vissa webbprogram till en annan serviceplan.

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>Hur vet jag om Application Insights profiler körs?

Profileraren körs som en kontinuerlig web-jobb i webbprogrammet. Du kan öppna resursen webbprogram i https://portal.azure.com och kontrollera statusen för ”ApplicationInsightsProfiler” i bladet WebJobs. Om den inte körs, öppna **loggar** vill veta mer.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Varför hittar inte någon stack-exempel trots att profileraren körs?

Här följer några saker du kan kontrollera.

1. Se till att din Web App Service-Plan grundläggande nivån och högre.
2. Kontrollera att ditt webbprogram har Application Insights SDK 2.2 Beta och ovan aktiverad.
3. Kontrollera att ditt webbprogram har inställningen APPINSIGHTS_INSTRUMENTATIONKEY med samma instrumentation nyckel som används av Application Insights SDK.
4. Kontrollera att ditt webbprogram som körs på .net Framework 4.6.
5. Om det är ett ASP.NET Core-program kan också kontrollera [de nödvändiga beroendena](#aspnetcore).

Finns en kort värmts period när profileraren aktivt samlar in spårningar för flera prestanda när profileraren har startats. Samlar in prestanda spårningar i två minuter i varje timme efter att profileraren.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Jag använde Azure Service Profiler. Vad hände med den?  

När du aktiverar Application Insights profileraren har Azure Service Profiler-agenten inaktiverats.

### <a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall är totala tidsmått i visningsprogrammet för stacken större än den verkliga varaktigheten av begäran.

Detta kan inträffa när det finns två eller flera trådar som är associerade med en begäran körs parallellt. Den totala tråd tiden är sedan mer än tid som gått. I många fall kan du väntar på en tråd på den andra ska slutföras. Visningsprogrammet försöker identifiera detta och utelämna ointressanta vänta, men överför kanten av visar för mycket i stället för att utelämna vad som kan vara viktig information.  

När du ser parallella trådar i dina spår måste du bestämma vilka trådar som väntar på och identifiera kritiska för begäran. I de flesta fall är helt enkelt tråden som snabbt försätts i tillståndet vänta väntar på andra trådar. Koncentrera dig på de andra och ignorera tiden i väntande trådar.

### <a id="issue-loading-trace-in-viewer"></a>Inga profildata

1. Om de data som du försöker visa är äldre än några veckor, begränsa tiden filtret och försök igen.

2. Kontrollera att proxyservrar eller en brandvägg har inte blockerat åtkomsten till https://gateway.azureserviceprofiler.net.

3. Kontrollera att du använder i appen Application Insights instrumentation nyckeln är samma som du har aktiverat profilering med Application Insights-resursen. Nyckeln är vanligtvis i ApplicationInsights.config men finns också i web.config eller app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Felrapport i visningsprogrammet för profilering

Filen ett supportärende från portalen. Inkludera Korrelations-ID i felmeddelandet.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Distributionsfel Directory är inte tomt ' D:\\hem\\plats\\wwwroot\\App_Data\\jobben

Om du omdistribuera ditt webbprogram till en resurs för Apptjänster med Profiler som aktiverad, kan det uppstå fel liknar följande: katalogen är inte tom ”D:\\hem\\plats\\wwwroot\\App_Data\\jobben felet inträffar om du kör webbdistribution från skript eller på VSTS distribution Pipeline.
Lösning på problemet är att lägga till följande ytterligare parametrar Web Deploy-uppgiften:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Detta tar bort mappen som används av App Insights Profiler och avblockera Omdistributionen processen. Pågående profileraren instansen påverkas inte.


## <a name="manual-installation"></a>Manuell installation

När du konfigurerar profileraren görs följande uppdateringar till Webbappens inställningar. Du kan göra dem själv manuellt om din miljö kräver, till exempel om programmet körs i Azure App Service miljö (ASE):

1. Öppna inställningar i bladet web app kontroll.
2. Ange ”.net Framework-version” till v4.6.
3. Värdet ”alltid på” på.
4. Lägga till appinställningen ”__APPINSIGHTS_INSTRUMENTATIONKEY__” och ange värdet till samma instrumentation nyckel som används av SDK.
5. Öppna avancerade verktyg.
6. Klicka på ”Gå” för att öppna Kudu-webbplatsen.
7. Välj ”webbplatstillägg” i Kudu-webbplatsen.
8. Installera ”__Programinsikter__” från galleriet.
9. Starta om webbprogrammet.

## <a id="aspnetcore"></a>ASP.NET Core-stöd

ASP.NET Core programmet behöver installera Microsoft.ApplicationInsights.AspNetCore Nuget-paketet 2.1.0-beta6 eller högre för att fungera med profileraren. Vi stöder inte längre lägre versioner efter 6/27/2017.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
