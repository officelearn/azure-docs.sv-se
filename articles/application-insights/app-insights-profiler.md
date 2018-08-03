---
title: Profilera live-webbappar på Azure med Application Insights Profiler | Microsoft Docs
description: Identifiera den heta sökvägen i serverkoden web med en låg fotavtryck profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 07/13/2018
ms.author: mbullwin
ms.openlocfilehash: 52f0e6c90aa9fa0dc439eae3801b2d4ac29387a9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429688"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilera live-Azure-webbappar med Application Insights

Den här funktionen i Azure Application Insights är allmänt tillgängliga för funktionen Web Apps i Azure App Service och förhandsversion av Azure-beräkningsresurser. Information angående [vid lokal användning av profiler](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Den här artikeln beskrivs hur lång tid som går åt för varje metod för ditt webbprogram när du använder [Application Insights](app-insights-overview.md). Application Insights Profiler visas detaljerad profiler för live-frågor som har hanteras av din app. Profiler visar den *heta sökvägen* som använder mest tid. Begäranden med olika svarstider profilerade på basis av sampling. Genom att använda en mängd olika tekniker kan minimera du kostnader som är associerat med programmet.

Profiler fungerar för närvarande för ASP.NET och ASP.NET Core web apps som körs på Web Apps. Grundläggande tjänstenivån eller krävs senare för att använda Profiler.

## <a id="installation"></a> Aktivera Profiler för Web Apps

När du har distribuerat en Webbapp, gör oavsett om du ingår i App Insights SDK i källkoden, du följande:

1. Gå till den **Apptjänster** fönstret i Azure-portalen.
1. Gå till **Inställningar > övervakning** fönstret.

   ![Aktivera App Insights på App Services-portalen](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Antingen följer du anvisningarna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka ditt webbprogram. Acceptera alla standardalternativ. **Diagnostik på kodnivå** är aktiverad som standard och gör det möjligt för Profiler.

   ![Lägg till App Insights-webbplatstillägg][Enablement UI]

1. Profiler har installerats med App Insights-webbplatstillägg och aktiveras med hjälp av en Appinställning för App Services.

    ![App-inställning för Profiler][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Aktivera Profiler för Azure-beräkningsresurser (förhandsversion)

Mer information finns i den [förhandsversionen av Profiler för Azure-beräkningsresurser](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Visa profiler data

Kontrollera att ditt program tar emot trafik. Om du genomför ett experiment kan du generera begäranden till web app med [prestandatestning för Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Om du nyligen aktiverat Profiler, kan du köra ett kort belastningstest för ungefär 15 minuter, som ska generera profiler-spårningar. Om du har haft Profiler som redan aktiverat på ett tag, håll Tänk på att Profiler körs slumpmässigt två gånger i timmen och under en period på två minuter varje gång det körs. Vi rekommenderar att du först köra belastningstestet för en timme att se till att du får exempel profiler-spårningar.

När ditt program tar emot en del trafik, går du till den **prestanda** väljer **vidta åtgärder** att visa profiler-spårningar och välj sedan den **Profiler-spårningar** knappen.

![Förhandsversion av Application Insights prestanda fönstret Profiler-spårningar][performance-blade-v2-examples]

Välj ett exempel för att visa en kod på servernivå uppdelning av tid kör på begäran.

![Application Insights trace-Utforskaren][trace-explorer]

Spårningen explorer visar följande information:

* **Visa frekvent sökväg**: öppnas det största löv noden eller Stäng minst något. I de flesta fall är den här noden bredvid en flaskhals för prestanda.
* **Etiketten**: namnet på funktionen eller händelse. Trädet visar en blandning av kod och händelser som inträffade (till exempel SQL- och HTTP-händelser). Översta händelsen representerar varaktighet för begäran.
* **Förfluten tid**: tidsintervallet mellan början av åtgärden och den igen.
* **När**: den tid när funktionen eller händelse kördes i förhållande till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestandadata

Profiler för Microsoft-tjänst använder en kombination av sampling metoder och instrumentation för att analysera prestanda för ditt program. När detaljerad samling pågår samplar service profiler instruktion pekare på varje dator CPU varje millisekund. Varje exempel samlar in fullständiga anropsstacken för tråden som körs för tillfället. Det ger detaljerad information om vad den tråden gjorde, både på hög nivå och på en låg nivå Abstraktionslager. Tjänsten profileraren samlar även in andra händelser för att spåra aktivitet korrelation och orsakssamband, inklusive vilket sammanhang växlar händelser, uppgift parallella bibliotek (TPL) händelser och tråd pool händelser.

Anropsstacken som visas i tidslinjevyn är resultatet av sampling och instrumentering. Eftersom varje exempel samlar in fullständiga anropsstacken för tråden, innehåller koden från Microsoft .NET Framework och andra ramverk som du hänvisar till.

### <a id="jitnewobj"></a>Objektet allokering (clr! JIT\_New eller clr! JIT\_Newarr1)

**CLR! JIT\_New** och **clr! JIT\_Newarr1** är hjälpfunktioner i .NET Framework som en hanterad heap för att allokera minne. **CLR! JIT\_New** anropas när ett objekt har allokerats. **CLR! JIT\_Newarr1** anropas när en objektmatris som har allokerats. De här två funktionerna är vanligtvis snabba och ta relativt lite tid. Om du ser **clr! JIT\_New** eller **clr! JIT\_Newarr1** ta ganska lång tid din tidslinje, betyder det att koden kan allokera många objekt och förbrukar stora mängder minne.

### <a id="theprestub"></a>Läser in koden (clr! ThePreStub)

**CLR! ThePreStub** är en hjälpfunktionen i .NET Framework som förbereder koden som körs för första gången. Detta vanligtvis omfattar, men är inte begränsat till just-in-time (JIT) kompilering. För varje C#-metoden **clr! ThePreStub** ska anropas en gång under livslängden för en process.

Om **clr! ThePreStub** tar lång tid för en begäran, detta anger att begäran är det första som kör den metoden. Tiden för körningen av .NET Framework att läsa in den första metoden är betydande. Du kan använda en värma upp processen som kör den delen av koden innan dina användare åtkomst till den eller Överväg att köra Native Image Generator (ngen.exe) på dina sammansättningar.

### <a id="lockcontention"></a>Låskonkurrens (clr! JITutil\_MonContention eller clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** eller **clr! JITutil\_MonEnterWorker** anger att den aktuella tråden väntar en låsas upp. Texten visas ofta när du kör ett C# **Lås** -instruktionen, vid den **Monitor.Enter** metod, eller när du startar en metod med det **MethodImplOptions.Synchronized** attribut. Låskonkurrens uppstår vanligen när tråden _A_ skaffar en lock och tråd _B_ försöker hämta samma Lås innan tråd _A_ släpper den.

### <a id="ngencold"></a>Läser in koden ([kall])

Om metodnamnet innehåller **[KALLA]**, till exempel **mscorlib.ni! [ COLD]system.Reflection.CustomAttribute.IsDefined**, .NET Framework-körningen kör kod för första gången som inte har optimerats av <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil guidad optimering</a>. För varje metod ska det visas högst en gång under processen livslängd.

Om du läser in koden tar lång tid för en begäran, indikerar det att begäran är den första mallen för att köra icke-optimerad delen av metoden. Överväg att använda en värma upp processen som kör den delen av koden innan dina användare åtkomst till den.

### <a id="httpclientsend"></a>Skicka HTTP-begäran

Metoder som **HttpClient.Send** tyda på att koden väntar en HTTP-begäran att slutföra.

### <a id="sqlcommand"></a>Databasåtgärden

Metoder som **SqlCommand.Execute** tyda på att koden väntar en databasåtgärd ska slutföras.

### <a id="await"></a>Väntar på (AWAIT\_tid)

**AWAIT\_tid** anger att koden är att en annan aktivitet ska slutföras. Detta inträffar vanligen med C# **AWAIT** instruktionen. När koden har ett C# **AWAIT**tråden unwinds och returnerar kontrollen till trådpoolen och det finns ingen tråd som har blockerats väntar på den **AWAIT** ska slutföras. Dock logiskt tråden som gjorde den **AWAIT** är ”blockerad” och väntar på att åtgärden slutförs. Den **AWAIT\_tid** instruktionen anger blockerade tiden att vänta tills åtgärden har slutförts.

### <a id="block"></a>Blockerade tid

**BLOCKED_TIME** anger att koden väntar på en annan resurs ska vara tillgängliga. Det kan till exempel vänta för ett synkroniseringsobjekt, för en tråd ska vara tillgängliga eller för att slutföra en begäran.

### <a id="cpu"></a>CPU-tid

Processorn är upptagen med att köra instruktionerna.

### <a id="disk"></a>Disktid i procent

Programmet utför diskåtgärder.

### <a id="network"></a>Nätverkstid

Programmet utför nätverksåtgärder.

### <a id="when"></a>När kolumnen

Den **när** kolumnen är en visualisering av hur INKLUDERANDE exemplen som samlats in för en nod kan variera över tid. Det totala intervallet för begäran är uppdelad i 32 tid buckets. Inkluderande exemplen för noden ackumuleras i dessa 32 buckets. Varje bucket representeras som en stapel. Höjden på fältet representerar ett skalade värde. För noder som är markerade **CPU_TIME** eller **BLOCKED_TIME**, eller där det finns en uppenbar relation som förbrukar en resurs (till exempel CPU, disk eller tråd), i fältet representerar användningen av en av resurser under perioden för bucket. Det är möjligt att hämta ett värde är större än 100 procent av det förbrukande flera resurser för de här måtten. Om du använder, i genomsnitt två processorer under ett intervall får du till exempel 200 procent.

## <a name="limitations"></a>Begränsningar

Standardkvarhållningsperioden för data är fem dagar. Den maximala data som matas in per dag är 10 GB.

Det kostar inget att använda Profiler-tjänsten. Om du vill använda tjänsten Profiler måste din webbapp måste vara finns i Basic-nivån av Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Omkostnader och samplingsalgoritmen

Profiler körs slumpmässigt två minuter varje timme på varje virtuell dator som är värd för programmet som har aktiverat för att samla in spårningar Profiler. När Profiler körs läggs från 5 procent till 15 procent processoråtgång till servern.

Fler servrar som är tillgängliga för att vara värd för programmet, mindre påverkan Profiler har på övergripande prestanda. Det beror på att samplingsalgoritmen resulterar i Profiler som körs på endast 5 procent av servrar när som helst. Flera servrar är tillgängliga för att hantera webbegäranden ska förskjutas serverbelastningen orsakade genom att köra Profiler.

## <a name="disable-profiler"></a>Inaktivera Profiler

Stoppa eller starta om Profiler för en enskild webbapp-instansen under **Webbjobb**går du till Web Apps-resurs. Ta bort Profiler genom att gå till **tillägg**.

![Inaktivera Profiler för ett webbjobb][disable-profiler-webjob]

Vi rekommenderar att du har Profiler som är aktiverad på alla dina webbprogram för att identifiera några prestandaproblem så tidigt som möjligt.

Om du använder WebDeploy för att distribuera ändringar i ditt webbprogram, kontrollerar du att du undantar mappen App_Data tas bort under distributionen. Annars kan tas tillägget Profiler filer bort nästa gång du distribuerar webbappen till Azure.


## <a id="troubleshooting"></a>Felsökning

### <a name="too-many-active-profiling-sessions"></a>För många aktiva sessioner för profilering

För närvarande kan du aktivera Profiler på upp till fyra Azure web apps och distributionsplatser som körs i samma service-abonnemang. Om Profiler webbjobb rapporterar för många aktiva profilering sessioner, flytta webbappar till en annan service-planen.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur vet jag om Application Insights Profiler körs?

Profiler körs som ett kontinuerligt webbjobb i webbapp. Du kan öppna webbappresursen som i den [Azure-portalen](https://portal.azure.com). I den **WebJobs** fönstret Kontrollera status för **ApplicationInsightsProfiler**. Om den inte körs öppnar **loggar** vill ha mer information.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Varför hittar inte alla exempel som stack, även om Profiler körs?

Här följer några saker som du kan kontrollera:

* Kontrollera att web app service-planen är nivån Basic eller högre.
* Kontrollera att webbappen har Application Insights SDK 2.2 Beta eller senare aktiverad.
* Se till att webbappen har den **APPINSIGHTS_INSTRUMENTATIONKEY** inställningen som konfigurerats med samma instrumenteringsnyckeln som används av Application Insights SDK.
* Kontrollera att din webbapp körs på .NET Framework 4.6.
* Om din webbapp är ett ASP.NET Core-program kontrollerar [de nödvändiga beroendena](#aspnetcore).

När Profiler har startat, finns det en kort uppvärmningsperiod då Profiler aktivt samlar in flera prestandaspårningar. Samlar in prestandaspårningar för varje timme med två minuter efter det Profiler.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Jag har använt Azure Service-profiler. Vad hände med det?

När du aktiverar Application Insights Profiler, är profiler-agent för Azure-tjänsten inaktiverad.

### <a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall kan är stack visningsprogrammet måttet total tid större än varaktigheten för begäran.

Den här situationen kan uppstå när två eller flera trådar som är associerade med en begäran och de fungerar parallellt. I så fall kan är den totala tråd-tid större än den förflutna tiden. En tråd kan vänta på den andra ska slutföras. Visningsprogrammet försöker identifiera detta och utesluter ointressanta vänta, men den Överför vid sidan av för mycket informationen i stället utelämna vad kan vara viktig information.

När du ser parallella trådar i dina spårningar avgör vilka trådar som väntar på, så du kan fastställa den kritiska vägen för begäran. I de flesta fall tråden som snabbt försätts i vänteläge helt enkelt att vänta på de andra trådarna. Koncentrera dig på de andra trådarna och ignorera tiden att vänta på trådar.

### <a id="issue-loading-trace-in-viewer"></a>Inga profildata

Här följer några saker som du kan kontrollera:

* Om de data som du vill visa är äldre än ett par veckor, försök att begränsa din tidsfiltret och försök igen.
* Se till att proxyservrar eller en brandvägg inte har blockerat åtkomsten till https://gateway.azureserviceprofiler.net.
* Kontrollera att Application Insights-instrumenteringsnyckeln som du använder i din app är samma som den Application Insights-resurs som du använde för att aktivera profilering. Nyckeln är vanligtvis i filen ApplicationInsights.config, men det kan också vara i filen web.config eller app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Felrapport i visningsprogrammet för profilering

Skicka in ett supportärende i portalen. Glöm inte att ta Korrelations-ID från felmeddelandet.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Distributionsfel: katalogen är inte tom ”D:\\home\\plats\\wwwroot\\App_Data\\Jobbens

Om du omdistribuerar din webbapp till en resurs för Web Apps med Profiler som är aktiverad, kan du få ett meddelande som liknar följande:

*Katalogen är inte tom ”D:\\home\\plats\\wwwroot\\App_Data\\Jobbens*

Det här felet uppstår om du kör Web Deploy från skript eller Distributionspipeline för Visual Studio Team Services. Lösningen är att lägga till följande parametrar för ytterligare distribution till Web Deploy-uppgift:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar ta bort mappen som används av Application Insights Profiler och avblockera distribuera om processen. De påverkar inte Profiler-instans som körs för tillfället.

## <a name="manual-installation"></a>Manuell installation

När du konfigurerar Profiler görs uppdateringar till webbappens inställningar. Du kan aktivera uppdateringar manuellt om miljön kräver den. Ett exempel kan vara att ditt program körs i en miljö med Web Apps för PowerApps.

1. I den **Web App Control** rutan Öppna **inställningar**.
1. Ange **.Net Framework-version** till **v4.6**.
1. Ange **Always On** till **på**.
1. Lägg till den **APPINSIGHTS_INSTRUMENTATIONKEY** app inställning och ange värdet till samma instrumenteringsnyckeln som används av SDK: N.
1. Öppna **Avancerade analysverktyg**.
1. Välj **Gå** att öppna Kudu-webbplatsen.
1. Välj på Kudu-webbplatsen **Platstillägg**.
1. Installera **Programinsikter** från Azure Web Apps-galleriet.
1. Starta om webbappen.

## <a id="profileondemand"></a> Utlös Profiler manuellt

Profiler kan aktiveras manuellt med ett enda musklick. Anta att du kör ett webbtest för prestanda. Du måste spårningarna för att hjälpa dig att förstå hur ditt webbprogram fungerar under belastning. Det är viktigt att ha kontroll över när spårningssessioner samlas eftersom du vet när belastningstestet ska köras, men slumpmässiga exempelintervallet kan gå miste om den.
Följande steg illustrerar hur det här scenariot fungerar:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Valfritt) Steg 1: Skapa trafik till webbappen genom att starta ett webbtest för prestanda

Om din webbapp redan har inkommande trafik, eller om du bara vill generera manuellt trafik, hoppa över det här avsnittet och gå vidare till steg 2.

Gå till Application Insights-portalen **konfigurera > prestandatestning**. Klicka på ny om du vill starta ett nytt prestandatest.
![Skapa nytt prestandatest][create-performance-test]

I den **nytt prestandatest** fönstret Konfigurera mål-URL för testning. Acceptera alla standardinställningar och börja köra belastningstestet.

![Konfigurera belastningstest][configure-performance-test]

Du kan se det nya testet är först i kön, följt av statusen pågår.

![Läs in test har skickats och väntar i kö][load-test-queued]

![belastningstest körs pågår][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Steg 2: Börja profiler på begäran

När belastningstestet körs kan börja vi profiler för att fånga in spårningar på webbappen medan den tar emot belastningen.
Gå till fönstret Konfigurera Profiler:

![konfigurera profiler fönstret post][configure-profiler-entry]

På den **konfigurera Profiler fönstret**, det finns en **profil nu** knappen för att utlösa profiler på alla instanser av de länkade web apps. Dessutom kan tillhandahålls du synlighet i när profiler kördes tidigare.

![Profiler på begäran][profiler-on-demand]

Meddelande och status ändras på profiler körningens status visas.

### <a name="step-3-view-traces"></a>Steg 3: Visa spår

Följ anvisningarna på meddelandet för att gå till bladet och visa prestandaspårningar när profiler är klar.

### <a name="troubleshooting-on-demand-profiler"></a>Felsökning av profiler för på begäran

Ibland kan du se Profiler timeout-felmeddelande efter en på begäran-session:

![Profiler tidsgränsfel][profiler-timeout]

Det kan finnas två orsaker till varför du ser det här felet:

1. På begäran profiler sessionen är klar men Application Insights tog längre tid att bearbeta insamlade data. Om data inte slutfördes bearbetas på 15 minuter, visas ett timeout-meddelande i portalen. Även om efter ett tag visas Profiler-spårningar. Om detta inträffar kan bara Ignorera felmeddelandet för tillfället. Vi arbetar på att lösa

1. Webbappen har en äldre version av Profiler-agent som inte har funktionen på begäran. Om du har aktiverat Application Insights-profilen tidigare litar du behöver uppdatera dina Profiler-agent ska starta med hjälp av funktionen på begäran.
  
Följ stegen nedan för att kontrollera och installera den senaste Profiler:

1. Gå till Appinställningar för App Services och kontrollera om följande inställningar är inställda:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersätt med rätt instrumenteringsnyckeln för Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 om någon av dessa inställningar inte ställs in, gå till fönstret Application Insights aktivering att installera den senaste webbplatstillägg.

1. Gå till Application Insights-fönstret i App Services-portalen.

    ![Aktivera Application Insights från App Services-portalen][enable-app-insights]

1. Om en ”uppdatera”-knappen på följande sida visas klickar du på den för att uppdatera Application Insights-webbplatstillägg som installerar den senaste versionen Profiler-agenten.
![Uppdatera webbplatstillägg][update-site-extension]

1. Klicka sedan på **ändra** till att se till att Profiler är på och välj **OK** att spara ändringarna.

    ![Ändra och spara appinsikter][change-and-save-appinsights]

1. Gå tillbaka till **Appinställningar** fliken för den App Service för att kontrollera följande för app-inställningar är inställda:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersätt med rätt instrumenteringsnyckeln för application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![appinställningar för profiler][app-settings-for-profiler]

1. Du kan också kontrollera tilläggsversion och se till att ingen uppdatering är tillgänglig.

    ![Sök efter uppdatering av webbappstillägget][check-for-extension-update]

## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
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
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
