---
title: Felsöka problem med Profiler för Azure Application Insights
description: I den här artikeln visas felsökningssteg och information som hjälper utvecklare som har problem med att aktivera eller använda Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f284d4dfbe550c357f81c01fa0a66aa9878b6c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671570"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Felsöka problem med att aktivera eller visa Profiler för programstatistik

## <a name="active-issues"></a>Aktiva frågor

* Profilering för ASP.NET Core 3.x-program stöds inte ännu.
  * Om du måste ha Profiler på, är en lösning att använda [Application Insights Profiler för ASP.NET Core](https://github.com/microsoft/ApplicationInsights-Profiler-AspNetCore). Profileret är märkt för Linux, men det fungerar också med .NET Core 3.0 + program på Windows. Mer information finns i [versioner som stöds](https://github.com/microsoft/ApplicationInsights-Profiler-AspNetCore#supported-versions).

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Allmän felsökning

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler överförs bara om det finns förfrågningar till ditt program medan Profiler körs

Profiler för Azure Application Insights Samlar in profileringsdata i två minuter varje timme. Den samlar också in data när du väljer knappen **Profil nu** i fönstret Konfigurera profilprofiler för **programinsikter.** Men profileringsdata överförs bara när den kan kopplas till en begäran som hände medan Profiler kördes. 

Profiler skriver spårningsmeddelanden och anpassade händelser till din Application Insights-resurs. Du kan använda dessa händelser för att se hur Profiler körs. Om du tycker att Profiler ska köra och hämta spårningar, men de inte visas i **fönstret Prestanda,** kan du kontrollera hur Profiler körs:

1. Sök efter spårningsmeddelanden och anpassade händelser som skickas av Profiler till din Application Insights-resurs. Du kan använda den här söksträngen för att hitta relevanta data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Följande bild visar två exempel på sökningar från två AI-resurser: 
    
   * Till vänster tar programmet inte emot begäranden medan Profiler körs. Meddelandet förklarar att överföringen avbröts på grund av ingen aktivitet. 

   * Till höger startade Profiler och skickade anpassade händelser när begäranden som inträffade när Profiler kördes. Om den anpassade händelsen ServiceProfilerSample visas betyder det att Profiler har bifogat en spårning till en begäran och du kan visa spårningen i fönstret **Programstatistikprestanda.**

     Om ingen telemetri visas körs inte Profiler. Felsökningsavsnitten för din specifika apptyp senare i den här artikeln finns i felsökningsavsnitten för din specifika apptyp.  

     ![Sök profiler telemetri][profiler-search-telemetry]

1. Om det fanns begäranden medan Profiler kördes kontrollerar du att begärandena hanteras av den del av programmet som har Profiler aktiverat. Även om program ibland består av flera komponenter aktiveras Profiler endast för vissa komponenter. I fönstret **Konfigurera profiler profiler** för programstatistik visas de komponenter som har överfört spårningar.

### <a name="other-things-to-check"></a>Andra saker att kontrollera
* Kontrollera att appen körs på .NET Framework 4.6.
* Om din webbapp är ett ASP.NET Core-program måste den köras minst ASP.NET Core 2.0.
* Om de data du försöker visa är äldre än ett par veckor kan du försöka begränsa tidsfiltret och försöka igen. Spår raderas efter sju dagar.
* Kontrollera att proxyservrar eller en brandvägg inte https://gateway.azureserviceprofiler.nethar blockerat åtkomsten till .
* Profiler stöds inte på kostnadsfria eller delade apptjänstplaner. Om du använder någon av dessa planer kan du prova att skala upp till en av de grundläggande planerna och Profiler ska börja arbeta.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dubbelräkning i parallella trådar

I vissa fall är det totala tidsmåttet i stackvisaren mer än varaktigheten för begäran.

Den här situationen kan uppstå när två eller flera trådar är associerade med en begäran och de fungerar parallellt. I så fall är den totala trådtiden mer än den förflutna tiden. En tråd kan vänta på den andra att slutföras. Tittaren försöker identifiera den här situationen och utelämnar den ointressanta väntetiden. På så sätt felar det på sidan av att visa för mycket information snarare än att utelämna vad som kan vara kritisk information.

När du ser parallella trådar i dina spår bestämmer du vilka trådar som väntar så att du kan fastställa den kritiska linjen för begäran. Vanligtvis väntar tråden som snabbt går in i ett väntetillstånd helt enkelt på de andra trådarna. Koncentrera dig på de andra trådarna och ignorera tiden i de väntande trådarna.

### <a name="error-report-in-the-profile-viewer"></a>Felrapport i profilvisaren
Skicka in en supportbiljett i portalen. Var noga med att inkludera korrelations-ID:n från felmeddelandet.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Felsöka Profiler på Azure App Service
Så att Profiler ska fungera korrekt:
* Din webbapptjänstplan måste vara basic-nivå eller högre.
* Webbappen måste ha Application Insights aktiverat.
* Webbappen måste ha följande appinställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Webbjobben **ApplicationInsightsProfiler3** måste köras. Så här kontrollerar du webbjobbet:
   1. Gå till [Kudu.](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)
   1. Välj **WebJobs Dashboard på** **Verktyg-menyn** .  
      **Fönstret WebJobs** öppnas. 
   
      ![profiler-webjob]   
   
   1. Om du vill visa information om webbjobbet, inklusive loggen, väljer du länken **ApplicationInsightsProfiler3.**  
     Fönstret **Information om kontinuerlig webbjobb** öppnas.

      ![profiler-webjob-log]

Om du inte kan ta reda på varför Profiler inte fungerar för dig kan du serviceprofilerhelp@microsoft.comladda ned loggen och skicka den till vårt team för att få hjälp. 
    
### <a name="manual-installation"></a>Manuell installation

När du konfigurerar Profiler görs uppdateringar av webbappens inställningar. Om din miljö kräver det kan du installera uppdateringarna manuellt. Ett exempel kan vara att ditt program körs i en Web Apps-miljö för PowerApps. Så här installerar du uppdateringar manuellt:

1. Öppna **Inställningar**i kontrollfönstret **För webbapp.**

1. Ange **.NET Framework-versionen** till **v4.6**.

1. Ställ **alltid på** på **på**.
1. Skapa följande appinställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>För många aktiva profileringssessioner

För närvarande kan du aktivera Profiler på högst fyra Azure-webbappar och distributionsplatser som körs i samma serviceplan. Om du har fler än fyra webbappar som körs i en apptjänstplan kan Profiler kasta en *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler körs separat för varje webbapp och försöker starta en ETW-session (Event Tracing for Windows) för varje app. Men ett begränsat antal ETW-sessioner kan vara aktiva på en gång. Om Profiler-webbjobben rapporterar för många aktiva profileringssessioner flyttar du vissa webbappar till en annan serviceplan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Distributionsfel: Katalogen inte\\\\tom\\"D: home site wwwroot\\App_Data\\jobb"

Om du distribuerar om webbappen till en Web Apps-resurs med Profiler aktiverat kan följande meddelande visas:

*Katalogen inte tom\\\\"D: hemsida\\wwwroot\\App_Data\\jobb"*

Det här felet uppstår om du kör webbdistribution från skript eller från Azure DevOps-distributionspipelinen. Lösningen är att lägga till följande ytterligare distributionsparametrar i webbdistributionsuppgiften:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar tar bort mappen som används av Application Insights Profiler och avblockerar omdistributionsprocessen. De påverkar inte profiler-instansen som körs för tillfället.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur avgör jag om Application Insights Profiler körs?

Profiler körs som ett kontinuerligt webbjobb i webbappen. Du kan öppna webbappresursen i [Azure-portalen](https://portal.azure.com). Kontrollera status för **ApplicationInsightsProfiler**i fönstret **WebJobs** . Om den inte körs öppnar **du Loggar** för att få mer information.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Felsöka problem med Profiler och Azure Diagnostics

>**Felet i profileren som levereras i WAD för molntjänster har åtgärdats.** Den senaste versionen av WAD (1.12.2.0) för Molntjänster fungerar med alla senaste versioner av App Insights SDK. Cloud Service-värdar uppgraderar WAD automatiskt, men det är inte omedelbart. Om du vill tvinga fram en uppgradering kan du distribuera om tjänsten eller starta om noden.

Så här ser du om Profiler har konfigurerats korrekt av Azure Diagnostics gör du följande tre saker: 
1. Kontrollera först om innehållet i Azure Diagnostics-konfigurationen som distribueras är vad du förväntar dig. 

1. För det andra, se till att Azure Diagnostics skickar rätt iKey på profilkommandoraden. 

1. För det tredje kontrollerar du loggfilen Profiler för att se om Profiler kördes men returnerade ett fel. 

Så här kontrollerar du de inställningar som användes för att konfigurera Azure Diagnostics:

1. Logga in på den virtuella datorn (VM) och öppna loggfilen på den här platsen. (Enheten kan vara c: eller d: och plugin-versionen kan vara annorlunda.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    eller
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. I filen kan du söka efter strängen **WadCfg** för att hitta de inställningar som skickades till den virtuella datorn för att konfigurera Azure Diagnostics. Du kan kontrollera om iKey som används av Profiler-diskbänken är korrekt.

1. Kontrollera kommandoraden som används för att starta Profiler. Argumenten som används för att starta Profiler finns i följande fil. (Enheten kan vara c: eller d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Kontrollera att iKey på kommandoraden Profiler är korrekt. 

1. Kontrollera loggfilen profiler med sökvägen som hittades i filen *config.json* föregående konfiguration. Den visar felsökningsinformationen som anger de inställningar som Profiler använder. Den visar också status och felmeddelanden från Profiler.  

    Om Profiler körs medan programmet tar emot begäranden visas följande meddelande: *Aktivitet har identifierats från iKey*. 

    När spårningen överförs visas följande meddelande: *Börja ladda upp spårning*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Redigera nätverksproxy- eller brandväggsregler

Om ditt program ansluter till Internet via en proxy eller en brandvägg kan du behöva redigera reglerna så att ditt program kan kommunicera med application insights profiler-tjänsten. De IPs som används av Application Insights Profiler ingår i Azure Monitor-tjänsttaggen.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








