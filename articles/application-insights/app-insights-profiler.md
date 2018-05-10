---
title: Profilen live-webbappar på Azure med Application Insights Profiler | Microsoft Docs
description: Identifiera hot sökvägen i koden web server med en låg storleken profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 34824401ec8d21949c5c5036a11197a09e240bd7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilen live Azure-webbappar med Application Insights

*Funktionen Azure Application Insights är allmänt tillgänglig för funktionen Web Apps i Azure App Service och är i förhandsvisning för Azure-beräkningsresurser. Information om [lokal användning av profileraren](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).*

Den här artikeln beskrivs hur lång tid som ägnats åt varje metod live webbprogrammets när du använder [Programinsikter](app-insights-overview.md). Application Insights Profiler visas detaljerad profiler för live-frågor som har hanteras av din app. Profileraren visar den *varm sökvägen* som används mest tid. Begäranden med olika svarstider listan på ett urval. Genom att använda olika metoder kan minimera du kostnader som är kopplad till programmet.

Profiler fungerar för närvarande för ASP.NET och ASP.NET Core webbprogram som körs på Web Apps. Grundläggande tjänst nivå eller krävs högre för att använda Profiler.

## <a id="installation"></a> Aktivera Profiler för ditt webbprogram för webbprogram
Om du redan har programmet publiceras till en webbapp, men inte har gjort något i källkoden för att använda Application Insights, gör du följande:
1. Gå till den **Apptjänster** rutan i Azure-portalen.
2. Under **övervakning**väljer **Programinsikter**, och sedan antingen följer du anvisningarna i rutan för att skapa en ny resurs eller välj en befintlig Application Insights-resurs för att övervaka din webbplats App.

   ![Aktivera App Insights på App Services-portalen][appinsights-in-appservices]

3. Om du har åtkomst till ditt projekt i källkoden [installera Application Insights](app-insights-asp-net.md).  
   Om den redan är installerad, kontrollera att du har den senaste versionen. Om du vill söka efter den senaste versionen i Solution Explorer högerklickar du på projektet och välj sedan **hantera NuGet-paket** > **uppdateringar** > **uppdatera alla paket**. Distribuera din app.

ASP.NET Core program kräver installation av Microsoft.ApplicationInsights.AspNetCore NuGet-paketet 2.1.0-beta6 eller senare för att arbeta med Profiler. Tidigare versioner stöds inte från och med den 27 juni 2017.

1. I [Azure-portalen](https://portal.azure.com), öppna Application Insights-resurs för webbappen. 
2. Välj **prestanda** > **aktivera Application Insights Profiler**.

   ![Välj Aktivera profileraren banderoll][enable-profiler-banner]

3. Du kan också välja den **Profiler** konfiguration för att visa status och aktivera eller inaktivera Profiler.

   ![Välj profileraren konfiguration][performance-blade]

   Webbprogram som är konfigurerade med Application Insights visas i den **Profiler** configuration-fönstret. Om du har följt de föregående stegen ska profileraren agenten installeras. 

4. I den **Profiler** configuration-fönstret, Välj **aktivera profileraren**.

5. Följ instruktionerna för att installera agenten Profiler. Om inget webbprogram har konfigurerats med Application Insights, Välj **Lägg till länkade appar**.

   ![Konfigurera alternativ för][linked app services]

Till skillnad från webbprogram som är värd via Web Apps planer hanteras program som finns i Azure-beräkningsresurser (till exempel Azure virtuella datorer, virtuella datorer, Azure Service Fabric eller Azure Cloud Services) direkt inte av Azure. I det här fallet finns inga webbprogram för att länka till. I stället för att länka till en app, Välj den **aktivera profileraren** knappen.

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Aktivera Profiler för Azure-beräkningsresurser (förhandsgranskning)

Mer information finns i [förhandsversionen av Profiler för Azure-beräkningsresurser](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Visa profiler data

Kontrollera att ditt program tar emot trafik. Om du göra ett experiment kan du generera begäranden till din web app med hjälp av [Application Insights prestandatester](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Om du nyligen har aktiverat Profiler, kan du köra ett kort belastningstest under cirka 15 minuter som ska generera profiler-spårningar. Om du har haft profileraren redan aktiverat på ett tag hålla på att profileraren slumpmässigt körs två gånger i timmen och en varaktighet på två minuter varje gång den körs. Vi rekommenderar att du först kör belastningstest för en timme för att kontrollera att du får exempel profiler-spårningar.

När programmet tar emot viss trafik, går du till den **prestanda** väljer **vidta åtgärder** att visa profiler-spårningar och välj sedan den **Profiler-spårningar** knappen.

![Application Insights prestanda rutan Förhandsgranska profileraren spårar][performance-blade-v2-examples]

Välj ett exempel för att visa en kod på objektnivå uppdelning av tid köra begäran.

![Application Insights trace explorer][trace-explorer]

Spåra explorer visar följande information:

* **Visa varm sökvägen**: öppnas den största löv nod eller Stäng minst något. I de flesta fall är den här noden gränsar till en prestandabegränsning.
* **Etiketten**: namnet på den funktion eller en händelse. En blandning av koden och händelser som inträffade (till exempel SQL- och HTTP-händelser) visar i trädet. Händelsen översta representerar varaktighet för begäran.
* **Förfluten tid**: tidsintervallet mellan starten av åtgärden och i slutet av åtgärden.
* **När**: den tid när funktionen eller händelse kördes i relation till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestandadata

Microsoft service profiler använder en kombination av provtagningsmetoder och instrumentation för att analysera prestandan för ditt program. När detaljerad samling pågår prover service profiler instruktion pekare på varje dator CPU varje millisekunder. Varje prov avbildar fullständig anropsstacken för tråden som körs för tillfället. Den ger detaljerad information om vad tråden utfördes, både på en hög nivå och på låg nivå av abstraktion. Tjänsten profileraren samlar även in andra händelser för att spåra aktivitet korrelation och orsakssamband, inklusive vilket sammanhang växla händelser, aktivitet parallella bibliotek (TPL) händelser och tråd pool händelser.

Anropsstacken som visas i tidslinjevyn är resultatet av provtagning och instrumentation. Eftersom varje prov fångar fullständig anropsstacken trådens, inkluderar den kod från Microsoft .NET Framework och från andra ramverk som du refererar till.

### <a id="jitnewobj"></a>Objektet allokering clr! JIT\_nytt eller clr! JIT\_Newarr1)
**CLR! JIT\_ny** och **clr! JIT\_Newarr1** helper-funktioner i .NET Framework som du allokera minne från en hanterad heap. **CLR! JIT\_ny** anropas när ett objekt har allokerats. **CLR! JIT\_Newarr1** anropas när en objektmatris allokeras. Dessa två funktioner är vanligtvis snabba och ta relativt små mängder tid. Om du ser **clr! JIT\_ny** eller **clr! JIT\_Newarr1** ta en lång tid i tidslinjen, anger det att koden kan fördela många objekt och förbrukar stora mängder minne.

### <a id="theprestub"></a>Inläsning av kod clr! ThePreStub)
**CLR! ThePreStub** är en hjälpfunktion i .NET Framework som förbereder koden ska köras för första gången. Detta vanligtvis inkluderar, men är inte begränsat till just-in-time (JIT) kompilering. För varje C#-metoden **clr! ThePreStub** ska anropas en gång under livslängden för en process.

Om **clr! ThePreStub** tar lång tid en begäran detta anger att begäran är den första som kör den här metoden. Tiden för körningen av .NET Framework att läsa in den första metoden är viktig. Du kan använda en upp process som körs som del av koden innan användarna åtkomst till den eller Överväg att köra Native Image Generator (ngen.exe) på din sammansättningar.

### <a id="lockcontention"></a>Låskonkurrens clr! JITutil\_MonContention eller clr! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** eller **clr! JITutil\_MonEnterWorker** anger att den aktuella tråden väntar på en låsas upp. Den här texten visas vanligen när du kör en C# **Lås** -instruktion, när du anropar den **Monitor.Enter** -metoden eller när du startar en metod med den **MethodImplOptions.Synchronized** attribut. Låskonkurrens inträffar vanligtvis när tråden _A_ hämtar ett lås och tråd _B_ försöker låsa samma innan tråd _A_ frigör den.

### <a id="ngencold"></a>Inläsning av kod ([KALLA])
Om metodnamnet innehåller **[COLD]**, som **mscorlib.ni! [ COLD]system.Reflection.CustomAttribute.IsDefined**, .NET Framework-körningen kör kod för första gången som inte är optimerad av <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil interaktiv optimering</a>. För varje metod ska det visas högst en gång under livslängden för processen.

Om du läser in koden tar lång tid en begäran, anger att begäran är den första att köra den icke-optimerad delen av metoden. Överväg att använda en upp process som körs som del av koden innan användarna åtkomst till den.

### <a id="httpclientsend"></a>Skicka HTTP-begäran
Metoder som **HttpClient.Send** indikerar att koden väntar på en HTTP-begäran ska slutföras.

### <a id="sqlcommand"></a>Databasåtgärden
Metoder som **SqlCommand.Execute** indikerar att koden väntar en åtgärd ska slutföras.

### <a id="await"></a>Väntar på (AWAIT\_tid)
**AWAIT\_tid** anger att koden väntar på en annan aktivitet ska slutföras. Detta inträffar vanligtvis med C# **AWAIT** instruktionen. När koden har en C# **AWAIT**tråden unwinds och returnerar kontrollen till trådpoolen och det finns ingen tråd blockeras väntar på att den **AWAIT** ska slutföras. Dock logiskt tråden som gjorde den **AWAIT** ”blockeras”, och väntar på att åtgärden ska slutföras. Den **AWAIT\_tid** instruktionen anger blockerade väntar på att slutföra uppgiften.

### <a id="block"></a>Blockerade tid
**BLOCKED_TIME** anger att koden väntar på en annan resurs ska vara tillgängliga. Det kan till exempel Vänta ett synkroniseringsobjekt, en tråd ska vara tillgängliga eller en begäran om att avsluta.

### <a id="cpu"></a>CPU-tid
Processorn är upptagen med att köra instruktionerna.

### <a id="disk"></a>Disktid i procent
Programmet utför diskåtgärder.

### <a id="network"></a>Tid för nätverk
Programmet utför nätverksåtgärder.

### <a id="when"></a>När kolumnen
Den **när** kolumnen är en visualisering av hur INKLUSIVA exemplen som samlas in för en nod variera över tid. Det totala intervallet för begäran är uppdelat i 32 tid buckets. Sammanlagt exemplen för noden ackumuleras i dessa 32 buckets. Varje bucket representeras som en stapel. Höjden på fältet representerar ett skalade värde. För noder som är markerade **CPU_TIME** eller **BLOCKED_TIME**, eller om det finns en tydlig relation som förbrukar en resurs (t.ex, CPU, disk eller tråd), i fältet representerar användningen av en av resurser under perioden som bucket. För de här måtten är det möjligt att hämta ett värde som är större än 100 procent genom att använda flera resurser. Om du använder, i genomsnitt två processorer under ett intervall får till exempel 200 procent.

## <a name="limitations"></a>Begränsningar

Standard-Datalagringsperiod är fem dagar. Den maximala data som inhämtas per dag är 10 GB.

Det finns inga avgifter för användning av tjänsten Profiler. Om du vill använda tjänsten profileraren måste ditt webbprogram måste vara finns i den grundläggande nivån för Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Kostnader och provtagning algoritm

Profiler körs slumpmässigt två minuter varje timme på varje virtuell dator som är värd för det program som har aktiverat för att samla in spårningar Profiler. När Profiler körs läggs från 5 till 15 procent CPU-belastning på servern.

Flera servrar som är tillgänglig som värd för programmet, mindre påverkan profileraren har på den övergripande prestandan. Det beror på att algoritmen provtagning resulterar i Profiler som körs på endast 5 procent av servrar när som helst. Flera servrar är tillgänglig för att hantera webbegäranden till förskjutning serverbelastningen orsakas genom att köra Profiler.

## <a name="disable-profiler"></a>Inaktivera Profiler
Stoppa eller starta om Profiler för en enskilda web apps-instans under **Webjobs**, gå till resursen Web Apps. Ta bort Profiler genom att gå till **tillägg**.

![Inaktivera Profiler för ett webb-jobb][disable-profiler-webjob]

Vi rekommenderar att du har Profiler som är aktiverad på alla dina webbprogram för att identifiera eventuella prestandaproblem så snart som möjligt.

Om du använder WebDeploy för att distribuera ändringarna till ditt webbprogram, kan du kontrollera att du utesluter mappen App_Data tas bort under distributionen. Annars tas tillägget profileraren filer bort nästa gång du distribuera webbappen till Azure.


## <a id="troubleshooting"></a>Felsökning

### <a name="too-many-active-profiling-sessions"></a>För många aktiva profilering sessioner

För närvarande kan du aktivera Profiler på högst fyra Azure-webbappar och distributionsplatser som körs i samma service-plan. Om Profiler web jobbet rapporterar för många aktiva profilering sessioner, flytta vissa webbprogram till en annan serviceplan.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur tar jag reda på om Application Insights Profiler körs?

Profiler körs som en kontinuerlig web-jobb i webbprogrammet. Du kan öppna appen webbresurs i den [Azure-portalen](https://portal.azure.com). I den **WebJobs** rutan kontrollerar du statusen för **ApplicationInsightsProfiler**. Om den inte körs, öppna **loggar** för mer information.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Varför hittar inte någon stack-exempel, trots att profileraren körs?

Här följer några saker som du kan kontrollera:

* Kontrollera att web app service-plan är grundläggande nivån eller högre.
* Kontrollera att ditt webbprogram har Application Insights SDK 2.2 Beta eller senare aktiverad.
* Kontrollera att ditt webbprogram har den **APPINSIGHTS_INSTRUMENTATIONKEY** inställningar som konfigureras med samma instrumentation nyckel som används av Application Insights SDK.
* Kontrollera att ditt webbprogram körs på .NET Framework 4.6.
* Kontrollera om ditt webbprogram är ett program för ASP.NET Core [de nödvändiga beroendena](#aspnetcore).

Finns en kort uppvärmningsperiod då profileraren aktivt samlar in spårningar för flera prestanda när profileraren har startats. Samlar in prestanda spårningar i två minuter varje timme efter det Profiler.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Jag använde Azure Service profiler. Vad hände med den?

När du aktiverar Application Insights profileraren har Azure Service profiler-agenten inaktiverats.

### <a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall är total tidsmått i visningsprogrammet för stacken större än varaktigheten för begäran.

Den här situationen kan inträffa när två eller flera trådar som är associerade med en begäran och de fungerar parallellt. I så fall är den totala tråd tid större än tid som gått. En tråd kan vänta på den andra ska slutföras. Visningsprogrammet försöker identifiera detta och utesluter ointressanta vänta, men den överför kanten av visning av för mycket information istället utelämna vad kan vara viktig information.

När du ser parallella trådar i dina spår avgör vilka trådar som väntar på så att du kan fastställa kritiska för begäran. I de flesta fall är helt enkelt tråden som snabbt försätts i tillståndet vänta väntar på andra trådar. Koncentrera dig på de andra trådarna och ignorera tiden i väntande trådar.

### <a id="issue-loading-trace-in-viewer"></a>Inga profildata

Här följer några saker som du kan kontrollera:

* Om de data som du försöker visa är äldre än några veckor, begränsa tiden filtret och försök igen.
* Se till att proxy eller brandvägg inte har blockerat åtkomsten till https://gateway.azureserviceprofiler.net.
* Kontrollera att du använder i appen Application Insights instrumentation nyckeln är samma som Application Insights-resurs som du använde när du aktiverade profilering. Nyckeln är vanligtvis i ApplicationInsights.config-filen, men det kan också vara i filen web.config eller app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Felrapport i visningsprogrammet för profilering

Skicka ett supportärende i portalen. Se till att inkludera Korrelations-ID i felmeddelandet.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Distributionsfel: katalogen är inte tom ”D:\\hem\\plats\\wwwroot\\App_Data\\jobben

Om du omdistribuera ditt webbprogram till en resurs i Web Apps med Profiler som är aktiverad, kan du se ett meddelande som liknar följande:

*Katalogen är inte tom ”D:\\hem\\plats\\wwwroot\\App_Data\\jobben*

Det här felet uppstår om du kör webbdistribution från skript eller från Visual Studio Team Services distribution Pipeline. Lösningen är att lägga till följande ytterligare parametrar Web Deploy-uppgiften:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar bort mappen som används av Application Insights Profiler och avblockera Omdistributionen processen. De påverkar inte Profiler-instans som körs för tillfället.


## <a name="manual-installation"></a>Manuell installation

När du konfigurerar profileraren görs uppdateringar till webbappens inställningar. Du kan tillämpa uppdateringarna manuellt om din miljö kräver. Ett exempel kan vara att ditt program körs i en miljö med Web Apps för PowerApps.

1. I den **App webbkontroll** rutan Öppna **inställningar**.
2. Ange **.Net Framework-version** till **v4.6**.
3. Ange **alltid på** till **på**.
4. Lägg till den **APPINSIGHTS_INSTRUMENTATIONKEY** app inställningen och värdet till samma instrumentation nyckel som används av SDK.
5. Öppna **avancerade verktyg**.
6. Välj **Gå** att öppna Kudu-webbplatsen.
7. Välj på webbplatsen Kudu **plats tillägg**.
8. Installera **Programinsikter** från galleriet Azure Web Apps.
9. Starta om webbprogrammet.

## <a id="profileondemand"></a> Utlösa Profiler manuellt
När vi har utvecklat profileraren vi har lagt till ett kommandoradsgränssnitt så att vi kan testa Profiler på apptjänster. Med hjälp av det här gränssnittet för samma kan användare också anpassa hur Profiler startar. På en hög nivå använder Profiler Web Apps Kudu System för att hantera profilering i bakgrunden. När du installerar Application Insights Extension kan skapa vi ett webbjobb jobb som är värd för Profiler. Vi använder samma tekniken för att skapa ett nytt jobb för webbprogram som du kan anpassa efter dina behov.

Det här avsnittet beskrivs hur du:

* Skapa ett webb-jobb som kan starta Profiler med en knapp i två minuter.
* Skapa ett webb-jobb som kan schemalägga Profiler ska köras.
* Ange argument för Profiler.


### <a name="set-up"></a>Konfigurera
Först bekanta dig med jobbet web instrumentpanelen. Under **inställningar**, Välj den **WebJobs** fliken.

![webjobs-bladet](./media/app-insights-profiler/webjobs-blade.png)

Som du ser visar den här instrumentpanelen alla web-jobb som är installerade på din plats. Du kan se ApplicationInsightsProfiler2 web jobb som har Profiler jobbet körs. Detta är där vi skapa nya webb-jobb för manuell och schemalagd profilering.

Hämta binärfiler som du måste göra följande:

1.  På Kudu-platsen på den **utvecklingsverktyg** väljer den **avancerade verktyg** Kudu-tangenten och välj sedan **Gå**.  
   En ny plats öppnas och du loggas in automatiskt.
2.  För att hämta Profiler binärfiler, gå till Utforskaren via **Felsökningskonsolen** > **CMD**, som finns längst upp på sidan.
3.  Välj **plats** > **wwwroot** > **App_Data** > **jobb**  >   **Kontinuerlig**.  
   Du bör se en mapp med namnet *ApplicationInsightsProfiler2*. 
4. Längst till vänster i mappen, Välj den **hämta** ikon.  
   Den här åtgärden hämtar den *ApplicationInsightsProfiler2.zip* fil. Vi rekommenderar att du skapar en ren katalog om du vill flytta till den här zip-arkivet.

### <a name="setting-up-the-web-job-archive"></a>Konfigurera jobbet webbarkiv
När du lägger till ett nytt jobb web Azure-webbplatsen du princip för att skapa ett zip-arkiv med en *run.cmd* filen i. Den *run.cmd* filen anger web jobbet vad du ska göra när du kör jobbet för webbprogram.

1.  Skapa en ny mapp (till exempel *RunProfiler2Minutes*).
2.  Kopiera filerna från den extraherade *ApplicationInsightProfiler2* till den här nya mappen.
3.  Skapa en ny *run.cmd* fil.  
    Av praktiska skäl kan öppna du arbetsmappen i Visual Studio Code innan du börjar.
4.  Lägg till kommando i filen `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`. Kommandona beskrivs på följande sätt:

    * `start`: Anger Profiler för att starta.  
    * `--engine-mode immediate`: Anger Profiler ska börja profilering omedelbart.  
    * `--single`: Anger Profiler för att köra och sedan stoppa automatiskt.  
    * `--immediate-profiling-duration 120`: Anger Profiler ska köras i 120 sekunder eller 2 minuter.

5.  Spara ändringarna.
6.  Arkivera mappen genom att högerklicka på den och sedan välja **skicka till** > **komprimerad mapp**.  
   Den här åtgärden skapar en .zip-fil med namnet på mappen för.

![Starta profileraren kommando](./media/app-insights-profiler/start-profiler-command.png)

Nu har du skapat ett webbprogram jobbet .zip-filen som du kan använda för att ställa in webjobs på din plats.

### <a name="add-a-new-web-job"></a>Lägg till ett nytt web-jobb
I det här avsnittet du lägga till ett nytt jobb webbprogram på webbplatsen. I följande exempel visas hur du lägger till ett jobb manuellt utlösta web. När du har lagt till jobbet manuellt utlösta web är processen nästan desamma för ett schemalagda web-jobb.

1.  Gå till den **Web jobb** instrumentpanelen.
2.  I verktygsfältet väljer **Lägg till**.
3.  Namnge ditt webb-jobb.  
    För att göra det kan hjälpa att matcha namnet på dina Arkiv och öppna den för en mängd olika versioner av den *run.cmd* fil.
4.  I den **filuppladdning** område i formuläret väljer den **öppna filen** ikon och söker efter .zip-filen som du skapade i föregående avsnitt.

    a.  I den **typen** väljer **Triggered**.  
    b.  I den **utlösare** väljer **manuell**.

5.  Välj **OK**.

![Starta profileraren kommando](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Kör Profiler

Nu när du har ett nytt jobb för webbprogram som du kan utlösa manuellt kan du försöka köra det genom att följa anvisningarna i det här avsnittet.

Utformning, du kan bara ha en *ApplicationInsightsProfiler.exe* process som körs på en dator samtidigt. Så, innan du kan inaktivera den *kontinuerlig* web jobbet från den här instrumentpanelen. 
1. Välj raden med det nya webb-projektet och välj sedan **stoppa**. 
2. I verktygsfältet väljer **uppdatera**, och bekräfta att status anger att jobbet har stoppats.
3. Välj raden med det nya webb-projektet och välj sedan **kör**.
4. Med raden fortfarande markerat i verktygsfältet väljer du den **loggar** kommando.  
    Den här åtgärden öppnar en instrumentpanel med web jobb för nya webb-jobbet och visar de senaste körs och deras resultat.
5. Välj instansen av kör som du just har börjat.  
    Om du har har utlösts nya web jobbet, kan du visa vissa diagnostikloggar kommer från Profiler som bekräftar du att den profilering har startats.

### <a name="things-to-consider"></a>Saker att tänka på

Även om den här metoden är relativt enkla, Tänk på följande:

* Eftersom web-jobb inte hanteras av vår tjänst, har vi inte går att uppdatera agenten binärfiler för web-jobbet. Vi inte har en stabil hämtningssidan för våra binärfilerna, så att det enda sättet att hämta de senaste binärfilerna är genom att uppdatera din anknytning och ta tag det från den *kontinuerlig* mapp som du gjorde i föregående steg.

* Eftersom den här processen använder kommandoradsargument som ursprungligen har utformats för utvecklare i stället för slutanvändare, kan argument ändras i framtiden. Vara medveten om eventuella ändringar när du uppgraderar. Det får inte vara en stor del av ett problem, eftersom du kan lägga till ett webb-jobb, köra den och testa för att säkerställa att den fungerar. Slutligen skapar vi ett gränssnitt för att hantera det utan den manuella processen.

* Funktionen Web jobb i Web Apps är unikt. När den körs jobbet web ser till att processen har samma miljövariabler och appinställningar som webbplatsen ska ha. Det innebär att du inte behöver skicka nyckeln instrumentation via kommandoraden till Profiler. Profiler ska hämta nyckeln instrumentation från miljön. Om du vill köra Profiler på dev-rutan eller på en dator utanför Web Apps måste du dock ange en instrumentation nyckel används. Du kan göra det genom att skicka ett argument, `--ikey <instrumentation-key>`. Det här värdet måste matcha instrumentation nyckeln som används av ditt program. Loggutdata från profileraren anger vilka ikey profileraren igång med och om vi har upptäckt aktivitet från instrumentation nyckeln när vi profilering.

* Manuellt utlösta web jobb kan aktiveras via webben Hook. Du kan hämta den här URL: en genom att högerklicka web-jobbet på instrumentpanelen och visa egenskaperna. I verktygsfältet, kan du markera **egenskaper** när du har valt web jobbet i tabellen. Den här metoden öppnar oändliga möjligheter som utlöser Profiler från din CI/CD-pipeline (till exempel VSTS) eller liknande Microsoft Flow (https://flow.microsoft.com/en-us/). Slutligen välja beror på hur komplexa som du vill göra din *run.cmd* filen (som kan vara en *run.ps1* filen), men flexibiliteten som det är.

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
