---
title: Felsöka problem med Azure Application Insights Profiler | Microsoft Docs
description: Den här artikeln beskriver vi felsökning steg och information som hjälper utvecklare som har problem med att aktivera eller med hjälp av Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 6ab13e1ecd5f4825270f4b3a28251b959ad98f02
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495404"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Felsöka problem med att aktivera och visa Application Insights Profiler

## <a id="troubleshooting"></a>Allmän felsökning

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler laddas bara om det finns begäranden till ditt program när Profiler körs

Azure Application Insights Profiler samlar in profildata för två minuter varje timme. De samlar även in data när du väljer den **profil nu** knappen i den **konfigurera Application Insights Profiler** fönstret. Men profileringsdata laddas bara när den kan kopplas till en begäran som inträffade när Profiler kördes. 

Profiler skriver spårningsmeddelanden och anpassade händelser till Application Insights-resursen. Du kan använda dessa händelser för att se hur Profiler körs. Om du tror att Profiler ska köra och samla in spårningar, men de visas inte i den **prestanda** fönstret som du kan kontrollera om du vill se hur Profiler körs:

1. Sök efter spårningsmeddelanden och anpassade händelser som skickas av Profiler till Application Insights-resursen. Du kan använda den här söksträng för att hitta relevanta data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Följande bild visas två exempel på sökningar från två AI-resurser: 
    
   * Längst till vänster är inte programmet tar emot begäranden när Profiler körs. Meddelandet förklarar att överföringen avbröts på grund av någon aktivitet. 

   * Till höger, Profiler igång och anpassade händelser när det har upptäckts begäranden som inträffade när Profiler kördes som skickas. Om ServiceProfilerSample anpassad händelse visas, betyder det att Profiler bifogas en begäran om en spårning och du kan visa spår i den **Insights programprestanda** fönstret.

     Om ingen telemetri visas är Profiler inte igång. Felsökning av beskrivs felsökningsavsnitt för specifika apptyper senare i den här artikeln.  

     ![Profiler telemetrisökning][profiler-search-telemetry]

1. Om det finns begäranden medan Profiler kördes, se till att begäranden hanteras av en del av ditt program som har aktiverat Profiler. Även om program ibland består av flera komponenter, har Profiler aktiverats för endast några av komponenterna. Den **konfigurera Application Insights Profiler** fönstret visar de komponenter som har överfört spårningar.

### <a name="other-things-to-check"></a>Annat att kontrollera
* Se till att din app körs på .NET Framework 4.6.
* Om din webbapp är ett ASP.NET Core-program, måste den köra minst ASP.NET Core 2.0.
* Om de data som du försöker visa är äldre än ett par veckor, försök att begränsa din tidsfiltret och försök igen. Spårningar tas bort efter sju dagar.
* Se till att proxyservrar eller en brandvägg inte har blockerat åtkomsten till https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall kan är stack visningsprogrammet måttet total tid större än varaktigheten för begäran.

Den här situationen kan uppstå när två eller flera trådar som är associerade med en begäran och de fungerar parallellt. I så fall kan är den totala tråd-tid större än den förflutna tiden. En tråd kan vänta på den andra ska slutföras. Visningsprogrammet försöker identifiera denna situation och utesluter ointressanta vänta. På så sätt kan den Överför vid sidan av för mycket informationen i stället utelämna vad kan vara viktig information.

När du ser parallella trådar i dina spårningar avgör vilka trådar som väntar på, så att du kan fastställa den kritiska vägen för begäran. Tråden som snabbt försätts i vänteläge är vanligtvis bara väntar på andra trådar. Koncentrera dig på de andra trådarna och ignorera tiden att vänta på trådar.

### <a name="error-report-in-the-profile-viewer"></a>Felrapport i visningsprogrammet för profil
Skicka in ett supportärende i portalen. Glöm inte att ta Korrelations-ID från felmeddelandet.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Felsökning av Profiler på Azure App Service
För Profiler ska fungera korrekt:
* Web app service-planen måste vara Basic-nivån eller högre.
* Din webbapp måste ha Application Insights som aktiveras.
* Din webbapp måste ha följande inställningar för appen:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för Application Insights-resursen    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Den **ApplicationInsightsProfiler3** webbjobb måste köras. Så här kontrollerar webbjobbet:
   1. Gå till [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. I den **verktyg** menyn och välj **WebJobs-instrumentpanelen**.  
      Den **WebJobs** öppnas fönstret. 
   
      ![profiler-webjob]   
   
   1. Om du vill visa information om webbjobbet, inklusive loggen, Välj den **ApplicationInsightsProfiler3** länk.  
     Den **kontinuerlig WebJob information** öppnas fönstret.

      ![profiler-webjob-log]

Om du förstår inte varför Profiler inte fungerar för dig, kan du hämta loggen och skicka den till vårt team för att få hjälp. 
    
### <a name="manual-installation"></a>Manuell installation

När du konfigurerar Profiler görs uppdateringar till webbappens inställningar. Om din miljö kräver det, kan du aktivera uppdateringar manuellt. Ett exempel kan vara att ditt program körs i en miljö med Web Apps för PowerApps. Tillämpa uppdateringar manuellt:

1. I den **Web App Control** rutan Öppna **inställningar**.

1. Ange **.NET Framework-version** till **v4.6**.

1. Ange **Always On** till **på**.
1. Skapa dessa inställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för Application Insights-resursen    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>För många aktiva sessioner för profilering

För närvarande kan du aktivera Profiler på upp till fyra Azure web apps och distributionsplatser som körs i samma service-abonnemang. Om du har fler än fyra web apps som körs i en app service-plan Profiler kan generera en *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler körs separat för varje webbapp och försöker starta en session för varje app som gäller för Windows ETW (Event Tracing). Men ett begränsat antal ETW-sessioner kan vara aktiv i taget. Om Profiler webbjobbet rapporterar för många aktiva profilering sessioner, flytta webbappar till en annan service-plan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Distributionsfel: Katalogen är inte tom ”D:\\home\\plats\\wwwroot\\App_Data\\Jobbens

Om du omdistribuera din webbapp till en resurs för Web Apps med Profiler som är aktiverad, visas följande meddelande:

*Katalogen är inte tom ”D:\\home\\plats\\wwwroot\\App_Data\\Jobbens*

Det här felet uppstår om du kör Web Deploy från skript eller från Azure DevOps-distributionspipeline. Lösningen är att lägga till följande parametrar för ytterligare distribution till Web Deploy-uppgift:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar ta bort mappen som används av Application Insights Profiler och avblockera distribuera om processen. De påverkar inte Profiler-instans som körs för tillfället.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur vet jag om Application Insights Profiler körs?

Profiler körs som ett kontinuerligt webbjobb i webbapp. Du kan öppna webbappresursen som i den [Azure-portalen](https://portal.azure.com). I den **WebJobs** fönstret Kontrollera status för **ApplicationInsightsProfiler**. Om den inte körs öppnar **loggar** vill ha mer information.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Felsöka problem med Profiler och Azure Diagnostics

>**Fel i profiler som levereras i WAD för molntjänster har åtgärdats.** Den senaste versionen av WAD (1.12.2.0) för Cloud Services fungerar med alla de senaste versionerna av App Insights SDK. Cloud Service värdar uppgraderar WAD automatiskt, men det sker inte omedelbart. Du kan distribuera om din tjänst eller starta om noden om du vill framtvinga en uppgradering.

Om du vill se om Profiler är korrekt konfigurerad genom Azure Diagnostics-data, gör du följande tre saker: 
1. Kontrollera först om du vill se om innehållet i Azure Diagnostics-konfiguration som distribueras är vad du förväntar dig. 

1. Därefter kontrollerar du att Azure Diagnostics klarar rätt iKey Profiler från kommandoraden. 

1. Det tredje Kontrollera loggfilen Profiler för att se om Profiler kördes, men returnerade ett fel. 

Så här kontrollerar du de inställningar som har använts för att konfigurera Azure Diagnostics:

1. Logga in på den virtuella datorn (VM) och sedan öppna loggfilen på den här platsen. (Enheten kan vara c: eller d: och version för plugin-programmet kan vara olika.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    eller
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. I filen som du kan söka efter strängen **WadCfg** du hittar de inställningar som skickades till den virtuella datorn att konfigurera Azure Diagnostics. Du kan kontrollera om iKey som används av Profiler-mottagare är korrekt.

1. Kontrollera den kommandorad som används för att starta Profiler. Argument som används för att starta Profiler finns i följande fil. (Enheten kan behöva c: eller d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Se till att iKey på kommandoraden Profiler är korrekt. 

1. Med hjälp av sökvägen hittades i det föregående *config.json* fil, finns i loggfilen för Profiler. Den visar felsökningsinformation som anger de inställningar som med hjälp av Profiler. Den visar även status och felmeddelanden från Profiler.  

    Om Profiler körs medan programmet tar emot begäranden, visas följande meddelande: *Aktivitet identifierad från iKey*. 

    När spårningen laddas, visas följande meddelande: *Starta överföring av spårning*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








