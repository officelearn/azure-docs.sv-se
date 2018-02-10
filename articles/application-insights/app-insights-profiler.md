---
title: "Profilen live-webbappar på Azure med Application Insights Profiler | Microsoft Docs"
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
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 80792a82adbb93e80c94b4829b704b70d2a8ed23
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilen live Azure-webbappar med Application Insights

*Den här funktionen av Application Insights är allmänt tillgänglig för Azure App Service och är i förhandsvisning för Azure-beräkningsresurser.*

Ta reda på hur mycket tid läggs i varje webbprogrammet live-metoden när du använder [Programinsikter](app-insights-overview.md). Application Insights profilering verktyget visas detaljerad profiler för live-frågor som har hanteras av din app och markeras den *varm sökvägen* som används mest tid. Begäranden med olika svarstider listan på ett urval. Kostnader för programmet minimeras med hjälp av olika metoder.

Profileraren fungerar för närvarande för ASP.NET och ASP.NET Core webbprogram som körs på Azure App Service. Den **grundläggande** tjänst nivå eller högre krävs för att använda profileraren.

## <a id="installation"></a>Aktivera profiler för App Services Web App
Om du redan har program som publicerats till en App Services, men inte har gjort något i källkoden för att använda Application Insights, gå till fönstret Apptjänster på Azure portal, gå till **övervakning | Application Insights**, följer du anvisningarna i rutan för att skapa en ny resurs eller välj en befintlig Application Insights-resurs för att övervaka ditt webbprogram.

![Aktivera App Insights på App Services-portalen][appinsights-in-appservices]

Om du har åtkomst till ditt projekt i källkoden [installera Application Insights](app-insights-asp-net.md). Om den redan är installerad, kontrollera att du har den senaste versionen. Om du vill söka efter den senaste versionen i Solution Explorer högerklickar du på projektet och välj sedan **hantera NuGet-paket** > **uppdateringar** > **uppdatera alla paket**. Distribuera din app.

ASP.NET Core program kräver installation av Microsoft.ApplicationInsights.AspNetCore NuGet-paketet 2.1.0-beta6 eller senare för att arbeta med profileraren. Tidigare versioner stöds inte från och med den 27 juni 2017.

I [Azure-portalen](https://portal.azure.com), öppna Application Insights-resurs för webbappen. Välj **prestanda** > **aktivera Application Insights Profiler**.

![Välj Aktivera profileraren banderoll][enable-profiler-banner]

Du kan också välja **Profiler** konfiguration för att visa status och aktivera eller inaktivera profileraren.

![Välj profileraren konfigurationen under prestanda][performance-blade]

Webbprogram som är konfigurerade med Application Insights visas i den **Profiler** configuration-fönstret. Om du följde stegen ovan installeras profileraren agenten redan. Välj **aktivera profileraren** i den **Profiler** configuration-fönstret.

Följ instruktionerna för att installera agenten profiler om det behövs. Om inget webbprogram har konfigurerats med Application Insights, Välj **Lägg till länkade appar**.

![Konfigurera alternativ för][linked app services]

Till skillnad från webbprogram som är värd via programtjänstplaner hanteras program som finns i Azure-beräkningsresurser (till exempel Azure virtuella datorer, virtuella datorer, Azure Service Fabric eller Azure Cloud Services) direkt inte av Azure. I det här fallet finns inga webbprogram för att länka till. I stället för att länka till en app, Välj den **aktivera profileraren** knappen.

### <a name="enable-the-profiler-for-azure-compute-resources-preview"></a>Aktivera profiler för Azure-beräkningsresurser (förhandsgranskning)

Hämta information en [förhandsversionen av profileraren för Azure-beräkningsresurser](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Visa profiler data

**Kontrollera att ditt program tar emot trafik.** Om du gör ett experiment kan du generera begäranden till webbprogram med hjälp av [Application Insights prestandatester](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Om du nyligen har aktiverat profileraren kan du köra ett kort belastningstest under cirka 15 minuter som ska generera profiler-spårningar. Om du har haft profileraren redan aktiverat på ett tag hålla på att profileraren slumpmässigt körs två gånger i timmen och en varaktighet på två minuter varje gång den körs. Vi rekommenderar att du först kör belastningstest för en timme för att kontrollera att du får exempel profiler-spårningar.

När programmet tar emot viss trafik, gå till den **prestanda** bladet > **vidta åtgärder** att visa profiler spårar. Välj den **Profiler-spårningar** knappen.

![Application Insights prestanda rutan Förhandsgranska profiler-spårningar][performance-blade-v2-examples]

Välj ett exempel för att visa en kod på objektnivå uppdelning av tid köra begäran.

![Application Insights trace explorer][trace-explorer]

Spåra explorer visar följande information:

* **Visa varm sökvägen**: öppnas den största löv nod eller Stäng minst något. I de flesta fall är den här noden gränsar till en prestandabegränsning.
* **Etiketten**: namnet på den funktion eller en händelse. Trädet innehåller en blandning av koden och händelser som inträffade (till exempel SQL- och HTTP-händelser). Händelsen översta representerar varaktighet för begäran.
* **Förfluten tid**: tidsintervallet mellan starten av åtgärden och i slutet av åtgärden.
* **När**: när funktionen eller händelse kördes i relation till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestandadata

Microsoft service profiler använder en kombination av provtagningsmetoder och instrumentation för att analysera prestandan för ditt program. När detaljerad samling pågår prover service profiler instruktionspekaren över alla processorer på den datorn varje millisekunder. Varje prov avbildar fullständig anropsstacken för tråden som körs för tillfället. Den ger detaljerad information om vad tråden utfördes, både på en hög nivå och på låg nivå av abstraktion. Tjänsten profileraren samlar även in andra händelser för att spåra aktivitet korrelation och orsakssamband, inklusive vilket sammanhang växla händelser, aktivitet parallella bibliotek (TPL) händelser och tråd pool händelser.

Anropsstacken som visas i tidslinjevyn är resultatet av provtagning och instrumentation. Eftersom varje prov fångar fullständig anropsstacken trådens, inkluderar den kod från Microsoft .NET Framework och från andra ramverk som du refererar till.

### <a id="jitnewobj"></a>Objektet allokering clr! JIT\_nytt eller clr! JIT\_Newarr1)
**CLR! JIT\_ny** och **clr! JIT\_Newarr1** helper-funktioner i .NET Framework som du allokera minne från en hanterad heap. **CLR! JIT\_ny** anropas när ett objekt har allokerats. **CLR! JIT\_Newarr1** anropas när en objektmatris allokeras. Dessa två funktioner är vanligtvis snabba och ta relativt små mängder tid. Om du ser **clr! JIT\_ny** eller **clr! JIT\_Newarr1** ta en lång tid i tidslinjen, det är en indikation på att koden kan fördela många objekt och förbrukar stora mängder minne.

### <a id="theprestub"></a>Inläsning av kod clr! ThePreStub)
**CLR! ThePreStub** är en hjälpfunktion i .NET Framework som förbereder koden ska köras för första gången. Detta vanligtvis inkluderar, men är inte begränsat till just-in-time (JIT) kompilering. För varje C#-metoden **clr! ThePreStub** ska anropas en gång under livslängden för en process.

Om **clr! ThePreStub** tar lång tid en begäran detta anger att begäran är den första som kör den här metoden. Tiden för körningen av .NET Framework att läsa in den första metoden är viktig. Du kan använda en upp process som körs som del av koden innan användarna åtkomst till den eller Överväg att köra Native Image Generator (ngen.exe) på din sammansättningar.

### <a id="lockcontention"></a>Låskonkurrens clr! JITutil\_MonContention eller clr! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** eller **clr! JITutil\_MonEnterWorker** anger att den aktuella tråden väntar på en låsas upp. Detta normalt visas när du kör en C# **Lås** -instruktion, när du anropar den **Monitor.Enter** metod, eller när du startar en metod med det **MethodImplOptions.Synchronized**attribut. Låskonkurrens uppstår vanligen när tråden _A_ hämtar ett lås och tråd _B_ försöker låsa samma innan tråd _A_ frigör den.

### <a id="ngencold"></a>Inläsning av kod ([KALLA])
Om metodnamnet innehåller **[COLD]**, som **mscorlib.ni! [ COLD]system.Reflection.CustomAttribute.IsDefined**, .NET Framework-körningen kör kod för första gången som inte är optimerad av <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil interaktiv optimering</a>. För varje metod ska det visas högst en gång under livslängden för processen.

Om du läser in koden tar lång tid en begäran, anger att begäran är den första att köra den icke-optimerad delen av metoden. Överväg att använda en upp process som körs som del av koden innan användarna åtkomst till den.

### <a id="httpclientsend"></a>Skicka HTTP-begäran
Metoder som **HttpClient.Send** indikerar att koden väntar på en HTTP-begäran ska slutföras.

### <a id="sqlcommand"></a>Databasåtgärden
Metoder som **SqlCommand.Execute** indikerar att koden väntar en åtgärd ska slutföras.

### <a id="await"></a>Väntar på (AWAIT\_tid)
**AWAIT\_tid** anger att koden väntar på en annan aktivitet ska slutföras. Det händer vanligtvis med C# **AWAIT** instruktionen. När koden har en C# **AWAIT**tråden unwinds och returnerar kontrollen till trådpoolen och det finns ingen tråd blockeras väntar på att den **AWAIT** ska slutföras. Dock logiskt tråden som gjorde den **AWAIT** ”blockeras”, och väntar på att åtgärden ska slutföras. Den **AWAIT\_tid** instruktionen anger blockerade väntar på att slutföra uppgiften.

### <a id="block"></a>Blockerade tid
**BLOCKED_TIME** anger att koden väntar på en annan resurs ska vara tillgängliga. Det kan till exempel Vänta ett synkroniseringsobjekt, en tråd ska vara tillgängliga eller en begäran om att avsluta.

### <a id="cpu"></a>CPU-tid
Processorn är upptagen med att köra instruktionerna.

### <a id="disk"></a>Disktid i procent
Programmet utför diskåtgärder.

### <a id="network"></a>Tid för nätverk
Programmet utför nätverksåtgärder.

### <a id="when"></a>När kolumnen
Den **när** kolumnen är en visualisering av hur INKLUSIVA exemplen som samlas in för en nod variera över tid. Det totala intervallet för begäran är uppdelat i 32 tid buckets. Sammanlagt exemplen för noden ackumuleras i dessa 32 buckets. Varje bucket representeras som en stapel. Höjden på fältet representerar ett skalade värde. För markerade noder **CPU_TIME** eller **BLOCKED_TIME**, eller om det finns en tydlig relationen mellan förbrukar en resurs (CPU, disk, tråd), i fältet representerar förbrukar en av resurserna för tidsperioden den tid på att bucket. För de här måtten är det möjligt att hämta ett värde som är större än 100% genom att använda flera resurser. Till exempel om du använder i genomsnitt två processorer under ett intervall måste hämta du 200%.

## <a name="limitations"></a>Begränsningar

Standard-datalagring är fem dagar. Den maximala data som inhämtas per dag är 10 GB.

Det finns inga avgifter för användning av tjänsten profiler. Om du vill använda tjänsten profileraren ditt webbprogram måste vara finns i den grundläggande nivån av Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Kostnader och provtagning algoritm

Profileraren körs slumpmässigt två minuter varje timme på varje virtuell dator som är värd för det program som har aktiverats för att samla in spårningar profileraren. När profileraren körs läggs mellan 5 och 15% CPU-belastning på servern.
Flera servrar som är tillgänglig som värd för programmet, mindre påverkan profileraren har på den övergripande prestandan. Det beror på att algoritmen provtagning resulterar i profiler som körs på 5% av servrarna när som helst. Flera servrar är tillgänglig för att hantera webbegäranden till förskjutning serverbelastningen orsakas genom att köra profileraren.

## <a name="disable-the-profiler"></a>Inaktivera profileraren
Stoppa eller starta om profiler för en enskild App Service-instans under **Webjobs**, gå till App Service-resurs. Om du vill ta bort profileraren, gå till **tillägg**.

![Inaktivera profiler för en webb-jobb][disable-profiler-webjob]

Vi rekommenderar att du har den profilerare som är aktiverad på alla dina webbprogram för att identifiera eventuella prestandaproblem så snart som möjligt.

Om du använder WebDeploy för att distribuera ändringarna till ditt webbprogram, kan du kontrollera att du utesluter mappen App_Data tas bort under distributionen. Annars tas tillägget profileraren filer bort nästa gång du distribuera webbappen till Azure.


## <a id="troubleshooting"></a>Felsökning

### <a name="too-many-active-profiling-sessions"></a>För många aktiva profilering sessioner

För närvarande kan du aktivera profiler på högst fyra Azure-webbappar och distributionsplatser som körs i samma service-plan. Om profiler web jobbet rapporterar för många aktiva profilering sessioner, flytta vissa webbprogram till en annan serviceplan.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur tar jag reda på om Application Insights Profiler körs?

Profileraren körs som en kontinuerlig web-jobb i webbprogrammet. Du kan öppna appen webbresurs i [Azure-portalen](https://portal.azure.com). I den **WebJobs** rutan kontrollerar du statusen för **ApplicationInsightsProfiler**. Om den inte körs, öppna **loggar** för mer information.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Varför hittar inte någon stack-exempel, trots att profileraren körs?

Här följer några saker som du kan kontrollera:

* Se till att web app service-plan är grundläggande nivån eller senare.
* Kontrollera att ditt webbprogram har Application Insights SDK 2.2 Beta eller senare aktiverad.
* Kontrollera att ditt webbprogram har den **APPINSIGHTS_INSTRUMENTATIONKEY** inställningar som konfigureras med samma instrumentation nyckel som används av Application Insights SDK.
* Kontrollera att ditt webbprogram körs på .NET Framework 4.6.
* Kontrollera om ditt webbprogram är ett program för ASP.NET Core [de nödvändiga beroendena](#aspnetcore).

Finns en kort uppvärmningsperiod då profileraren aktivt samlar in spårningar för flera prestanda när profileraren har startats. Samlar in prestanda spårningar i två minuter varje timme efter att profileraren.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Jag använde Azure Service Profiler. Vad hände med den?

När du aktiverar Application Insights profileraren har Azure Service Profiler-agenten inaktiverats.

### <a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall är total tidsmått i visningsprogrammet för stacken större än varaktigheten för begäran.

Detta kan inträffa när det finns två eller flera trådar som är associerade med en begäran och de fungerar parallellt. I så fall är den totala tråd tid större än tid som gått. En tråd kan vänta på den andra ska slutföras. Visningsprogrammet försöker identifiera detta och utesluter ointressanta vänta, men den överför kanten av visar för mycket i stället för att utelämna vad kan vara viktig information.

När du ser parallella trådar i dina spår avgör vilka trådar som väntar på så att du kan fastställa kritiska för begäran. I de flesta fall är helt enkelt tråden som snabbt försätts i tillståndet vänta väntar på andra trådar. Koncentrera dig på de andra trådarna och ignorera tiden i väntande trådar.

### <a id="issue-loading-trace-in-viewer"></a>Inga profildata

Här följer några saker som du kan kontrollera:

* Om de data som du försöker visa är äldre än några veckor, begränsa tiden filtret och försök igen.
* Kontrollera att proxyservrar eller en brandvägg har inte blockerat åtkomsten till https://gateway.azureserviceprofiler.net.
* Kontrollera att du använder i appen Application Insights instrumentation nyckeln är samma som Application Insights-resurs som du använde när du aktiverade profilering. Nyckeln är vanligtvis i ApplicationInsights.config, men kan även finns i filen web.config eller app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Felrapport i visningsprogrammet för profilering

Skicka ett supportärende i portalen. Se till att inkludera Korrelations-ID i felmeddelandet.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Distributionsfel: katalogen är inte tom ”D:\\hem\\plats\\wwwroot\\App_Data\\jobben

Om du omdistribuera ditt webbprogram till en resurs i App Service med profiler som är aktiverad, kan du se ett meddelande som ser ut som följande:

Katalogen är inte tom ”D:\\hem\\plats\\wwwroot\\App_Data\\jobben

Det här felet uppstår om du kör webbdistribution från skript eller från Visual Studio Team Services distribution Pipeline. Lösningen är att lägga till följande ytterligare parametrar Web Deploy-uppgiften:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar bort mappen som används av Application Insights Profiler och avblockera Omdistributionen processen. De påverkar inte profiler-instans som körs för tillfället.


## <a name="manual-installation"></a>Manuell installation

När du konfigurerar profileraren görs uppdateringar till webbappens inställningar. Du kan tillämpa uppdateringarna manuellt om din miljö kräver. Till exempel om programmet körs i en Apptjänst-miljö för PowerApps.

1. I fönstret web app kontroll öppna **inställningar**.
2. Ange **.Net Framework-version** till **v4.6**.
3. Ange **alltid på** till **på**.
4. Lägg till den __APPINSIGHTS_INSTRUMENTATIONKEY__ app inställningen och värdet till samma instrumentation nyckel som används av SDK.
5. Öppna **avancerade verktyg**.
6. Välj **Gå** att öppna Kudu-webbplatsen.
7. Välj på webbplatsen Kudu **plats tillägg**.
8. Installera __Programinsikter__ från galleriet Azure Web Apps.
9. Starta om webbprogrammet.

## <a id="profileondemand"></a>Utlösa profiler manuellt
När vi har utvecklat profileraren vi har lagt till ett kommandoradsgränssnitt så att vi kan testa profileraren på apptjänster. Använda samma gränssnitt som användarna kan också anpassa hur profileraren startar. På en hög nivå använder profileraren App Service Kudu System för att hantera profilering i bakgrunden. När du installerar Application Insights Extension kan skapa vi ett webbjobb jobb som är värd för profileraren. Vi använder samma tekniken för att skapa ett nytt jobb för webbprogram som du kan anpassa efter dina behov.

Det här avsnittet beskrivs hur du:

1. Skapa ett webb-jobb som kan starta profileraren i två minuter med en knapp.
2. Skapa ett webb-jobb som kan schemalägga profiler ska köras.
3. Ange argument för profileraren.


### <a name="set-up"></a>Konfigurera
Första vi bekanta dig med jobbet web instrumentpanelen. Under inställningar klickar du på fliken WebJobs.

![webjobs-bladet](./media/app-insights-profiler/webjobs-blade.png)

Som du ser den här instrumentpanelen visar alla web jobb som är installerade på din plats. Du kan se ApplicationInsightsProfiler2 web jobbet som har profiler jobbet körs. Detta är där vi hamna vår nya web jobb skapas för manuell och schemalagd profilering.

Första ska få binärfiler som vi behöver.

1.  Gå till Kudu-webbplatsen. Fliken development tools, klicka på ”Avancerad” verktygsfliken med Kudu-logotypen. Klicka på ”Gå”. Detta leder till en ny plats och loggar du in automatiskt.
2.  Nästa vi behöver hämta profileraren binärfiler. Gå till Utforskaren via Felsökningskonsolen -> CMD längst upp på sidan.
3.  Klicka på webbplats -> wwwroot -> App_Data -> jobb -> kontinuerlig. Du bör se en mapp ”ApplicationInsightsProfiler2”. Klicka på ikonen ladda ned till vänster om mappen. ”ApplicationInsightsProfiler2.zip” filen laddas ned.
4.  Då hämtas alla filer som du behöver. Jag rekommenderar att du skapar en Rensa katalog för att flytta den här zip-arkivet till innan du fortsätter.

### <a name="setting-up-the-web-job-archive"></a>Konfigurera jobbet webbarkiv
När du lägger till ett nytt jobb web azure-webbplatsen i princip kan skapa du ett zip-arkiv med en run.cmd i. Run.cmd Anger web jobbet vad du ska göra när du kör jobbet för webbprogram.

1.  Så här startar du skapa en ny mapp, vårt exempel heter ”RunProfiler2Minutes”.
2.  Kopiera filerna från den extraherade mappen ApplicationInsightProfiler2 till den nya mappen.
3.  Skapa en ny run.cmd-fil. (Du kan öppna den här arbetsmapp i VS-kod innan du startar i informationssyfte.)
4.  Lägg till kommando `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`, och spara filen.
a.  Den `start` kommandot, anger profileraren att starta.
b.  `--engine-mode immediate`Anger profileraren vi vill starta direkt profilering.
c.  `--single`innebär att köra och sedan stoppa automatiskt d.  `--immediate-profiling-duration 120`innebär att profileraren kör för 120 sekunder eller 2 minuter.
5.  Spara filen.
6.  Arkivera mappen, kan du högerklicka på mappen och välj Skicka till -> komprimerade mappen. Detta skapar en .zip-fil med namnet på mappen.

![Starta profileraren kommando](./media/app-insights-profiler/start-profiler-command.png)

Nu har vi ett web jobbet .zip som vi kan använda för att ställa in webjobs på vår webbplats.

### <a name="add-a-new-web-job"></a>Lägg till ett nytt web-jobb
Nästa vi lägga till ett nytt jobb webbprogram på webbplatsen. Det här exemplet visar hur du lägger till ett jobb manuellt utlösta web. När du ska kunna göra det är processen nästan exakt samma för schemalagd.

1.  Gå till instrumentpanelen för web-jobb.
2.  Klicka på kommandot Lägg till i verktygsfältet.
3.  Namnge ditt webb-jobb. Det kan hjälpa att matcha namnet på dina Arkiv och öppna det har olika versioner av run.cmd för tydlighetens skull.
4.  Ladda upp en del av formatet i filen, klicka på ikonen Öppna filen och hitta .zip-filen som du gjorde ovan.
5.  Välj Triggered för typen.
6.  Välj Manuell för utlösare.
7.  Tryck på OK för att spara.

![Starta profileraren kommando](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>Kör profileraren

Nu när vi har ett nytt webbprogram jobb som vi kan utlösa manuellt försöker vi du köra den.

1. Du kan bara ha en ApplicationInsightsProfiler.exe process som körs på en dator samtidigt avsiktligt. Så om du vill börja, se till att inaktivera kontinuerlig web jobbet från den här instrumentpanelen. Klicka på raden och tryck på ”Stoppa”. Välj uppdatering i verktygsfältet och bekräfta att status anger jobbet har stoppats.
2. Klicka på raden med det nya webb-projektet som du har lagt till och klicka på Kör.
3. Raden fortfarande markerat klickar på kommandot loggar i verktygsfältet nu du till en webbplats jobb-instrumentpanel för web-jobbet har startat. Den visar de senaste körs och deras resultat.
4. Klicka på instansen av du just har börjat köras.
5. Du bör se vissa diagnostikloggar som kommer från profileraren bekräftar profilering av vi har startats om alla gått bra.

### <a name="things-to-consider"></a>Saker att tänka på

Även om den här metoden är relativt enkla, finns det vissa saker att tänka på.

- Eftersom detta inte hanteras av vår tjänst, har vi inget sätt att uppdatera agenten binärfilerna för web-jobbet. Vi har för närvarande inte en stabil hämtningssidan för våra binärfiler så att det enda sättet att hämta senast är genom att uppdatera din anknytning och ta tag den i mappen kontinuerlig som vi gjorde i föregående steg.

- Eftersom detta är att använda kommandoradsargument som ursprungligen har utformats för utvecklare Använd i stället för slutanvändarens användning, de här argumenten kan ändra i framtiden, så bara vara medveten om som när du uppgraderar. Det får inte vara en stor del av ett problem Eftersom du kan lägga till en webbtjänst jobb, kör och kontrollera att den fungerar. Slutligen skapar vi ett gränssnitt för att hantera det utan den manuella processen.

- Funktionen Web jobb för Apptjänster är unikt i den när den körs jobbet web ser till att processen har samma miljövariabler och appinställningar som din webbplats kommer att få med. Det innebär att du inte behöver skicka nyckeln instrumentation via kommandoraden till profileraren. Det bör bara välja instrumentation nyckeln från miljön. Om du vill köra profileraren på dev-rutan eller på en dator utanför Apptjänster måste du dock ange en instrumentation nyckel används. Du kan göra detta genom att passera i ett argument `--ikey <instrumentation-key>`. Det här värdet måste matcha instrumentation nyckeln med hjälp av ditt program. Loggen utdata från profileraren och du kan ta reda vilka ikey profileraren igång med och om vi har upptäckt aktivitet från instrumentation nyckeln när vi profilering.

- Manuellt utlösta web-jobb kan faktiskt aktiveras via webben Hook. Du kan hämta den här URL: en genom att högerklicka på webben jobbet från instrumentpanelen och visa egenskaperna. Eller genom att välja egenskaper i verktygsfältet när du har valt web jobbet från tabellen. Detta öppnar oändliga möjligheter som utlöser profileraren från din CI/CD-pipeline (till exempel VSTS) eller liknande Microsoft Flow (https://flow.microsoft.com/en-us/). Slutligen detta beror på hur komplexa som du vill göra din run.cmd (som också kan en run.ps1), men flexibiliteten förekommer.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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
