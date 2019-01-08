---
title: Felsöka problem med Azure Application Insights Profiler | Microsoft Docs
description: Den här sidan innehåller felsökning steg och information som hjälper utvecklare som har problem med att aktivera eller med hjälp av Application Insights profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d424316cb8287dcf2a9ff04cf8e6889d5bada2cd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083238"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Felsöka problem med att aktivera och visa Application Insights Profiler

## <a id="troubleshooting"></a>Allmän felsökning

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>Profiler överförs endast om det finns begäranden till ditt program när profiler körs
Application Insights Profiler profiler samlas data in för varje timme, minut eller när den [ **profil nu** ](profiler-settings.md ?toc=/azure/azure-monitor/toc.json) knappen trycks den **konfigurera Application Insights Profiler**sidan. Men profileringsdata laddas bara när den kan kopplas till en begäran som inträffade när profiler kördes. 

Profiler skriver spårningsmeddelanden och anpassade händelser till application insights-resurs. Du kan använda dessa händelser för att se hur profiler körs. Om du tror att profileraren bör köra och samla in spårningar men inte ser dem på sidan prestanda, kan du kontrollera hur profiler körs:

1. Sök efter spårningsmeddelanden och anpassade händelser som skickas av profiler till Application Insights-resursen. Du kan använda den här söksträng för att hitta relevanta data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Här är ett exempel från två sökningar från två olika AI-resurser på skärmbilden nedan. 
    
    * Det till vänster är från ett program som inte få begäranden när profiler körs. Du kan se meddelandet att överföringen avbröts på grund av någon aktivitet. 

    * I det här exemplet till höger ser du att profileraren igång och anpassade händelser när det har upptäckts begäranden som inträffade när profiler kördes. Om du ser den anpassade händelsen ServiceProfilerSample innebär profiler bifogas en begäran om en spårning och du kan visa spårningen från sidan för Application Insights-prestanda.

    * Om du inte ser någon telemetri alls, körs inte profiler. Du kan behöva läsa felsökningsavsnitt innehåller information för specifika apptyper på det här dokumentet som nedan.  

     ![Profiler Telemetrisökning][profiler-search-telemetry]

1. Om det finns begäranden under tidsperioden profiler kördes, se till att begäranden hanteras av en del av ditt program som har aktiverat profiler. Program består ibland av flera komponenter men Profiler är endast aktiverad i vissa fall inte alla, komponenter. Konfigurera Application Insights Profiler-sidan visas de komponenter som har överfört spårningar.

### <a name="other-things-to-check"></a>Annat att kontrollera:
* Din app körs på .NET Framework 4.6.
* Om din webbapp är ett ASP.NET Core-program, måste den köra minst ASP.NET Core 2.0.
* Om de data som du vill visa är äldre än ett par veckor, försök att begränsa din tidsfiltret och försök igen. Spårningar tas bort efter sju dagar.
* Se till att proxyservrar eller en brandvägg inte har blockerat åtkomsten till https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall kan är stack visningsprogrammet måttet total tid större än varaktigheten för begäran.

Den här situationen kan uppstå när två eller flera trådar som är associerade med en begäran och de fungerar parallellt. I så fall kan är den totala tråd-tid större än den förflutna tiden. En tråd kan vänta på den andra ska slutföras. Visningsprogrammet försöker identifiera denna situation och utesluter ointressanta vänta, men den Överför vid sidan av för mycket informationen i stället utelämna vad kan vara viktig information.

När du ser parallella trådar i dina spårningar avgör vilka trådar som väntar på, så du kan fastställa den kritiska vägen för begäran. I de flesta fall tråden som snabbt försätts i vänteläge helt enkelt att vänta på de andra trådarna. Koncentrera dig på de andra trådarna och ignorera tiden att vänta på trådar.

### <a name="error-report-in-the-profile-viewer"></a>Felrapport i visningsprogrammet för profil
Skicka in ett supportärende i portalen. Glöm inte att ta Korrelations-ID från felmeddelandet.

## <a name="troubleshooting-profiler-on-app-services"></a>Felsökning av Profiler på App Services
### <a name="for-the-profiler-to-work-properly"></a>För profiler ska fungera korrekt:
* Web app service-planen måste vara Basic-nivån eller högre.
* Din webbapp måste ha Application Insights som aktiveras.
* Din webbapp måste ha den **APPINSIGHTS_INSTRUMENTATIONKEY** appinställningen som konfigurerats med samma instrumenteringsnyckeln som används av Application Insights SDK.
* Din webbapp måste ha den **APPINSIGHTS_PROFILERFEATURE_VERSION** appinställningen definieras och inställd på 1.0.0.
* Din webbapp måste ha den **DiagnosticServices_EXTENSION_VERSION** appinställningen definieras och ange värdet till ~ 3.
* Den **ApplicationInsightsProfiler3** webbjobb måste köras. Du kan kontrollera web-jobbet genom att gå till [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/), och öppna den **WebJobs-instrumentpanelen** under Verktyg-menyn. Som du ser i skärmbilderna nedan, genom att klicka på länken ApplicationInsightsProfiler2 kan se du information av webbjobbet, inklusive loggen.

    Här är en länk som du måste klicka för att se webjob-information: 

    ![profiler-webbjobb]

    Här är den sida som visar information. Du kan hämta loggen och skicka den till vårt team om du förstår inte varför profiler inte fungerar för dig.
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>Manuell installation

När du konfigurerar Profiler görs uppdateringar till webbappens inställningar. Du kan aktivera uppdateringar manuellt om miljön kräver den. Ett exempel kan vara att ditt program körs i en miljö med Web Apps för PowerApps.

1. I den **Web App Control** rutan Öppna **inställningar**.
1. Ange **.Net Framework-version** till **v4.6**.
1. Ange **Always On** till **på**.
1. Lägg till den **APPINSIGHTS_INSTRUMENTATIONKEY** app inställning och ange värdet till samma instrumenteringsnyckeln som används av SDK: N.
1. Lägg till den **APPINSIGHTS_PROFILERFEATURE_VERSION** appinställningen och ange 1.0.0.
1. Lägg till den **DiagnosticServices_EXTENSION_VERSION** appinställningen och ange värdet till ~ 3.

### <a name="too-many-active-profiling-sessions"></a>För många aktiva sessioner för profilering

För närvarande kan du aktivera Profiler på upp till fyra Azure web apps och distributionsplatser som körs i samma service-abonnemang. Om du har fler web apps än som körs i en app service-plan kan du se en Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException som utlöses av profiler. Profiler körs separat för varje webbapp och försöker starta en ETW-session för varje app. Men det finns ett begränsat antal ETW-sessioner som kan vara aktiv i taget. Om Profiler webbjobb rapporterar för många aktiva profilering sessioner, flytta webbappar till en annan service-planen.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Distributionsfel: Katalogen är inte tom ”D:\\home\\plats\\wwwroot\\App_Data\\Jobbens

Om du omdistribuerar din webbapp till en resurs för Web Apps med Profiler som är aktiverad, visas följande meddelande:

*Katalogen är inte tom ”D:\\home\\plats\\wwwroot\\App_Data\\Jobbens*

Det här felet uppstår om du kör Web Deploy från skript eller från Azure DevOps pipelinen. Lösningen är att lägga till följande parametrar för ytterligare distribution till Web Deploy-uppgift:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar ta bort mappen som används av Application Insights Profiler och avblockera distribuera om processen. De påverkar inte Profiler-instans som körs för tillfället.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur vet jag om Application Insights Profiler körs?

Profiler körs som ett kontinuerligt webbjobb i webbapp. Du kan öppna webbappresursen som i den [Azure-portalen](https://portal.azure.com). I den **WebJobs** fönstret Kontrollera status för **ApplicationInsightsProfiler**. Om den inte körs öppnar **loggar** vill ha mer information.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Felsökning av problem med Profiler och WAD

Det finns tre saker att kontrollera att profileraren har konfigurerats korrekt med WAD. Du måste först kontrollera att innehållet i WAD konfigurationen som har distribuerats är det du förväntar dig. Dessutom måste du kontrollera WAD skickar rätt iKey på profiler från kommandoraden. Det tredje kan du kontrollera loggfilen profiler för att se om profiler kördes, men får ett fel. 

Om du vill kontrollera de inställningar som har använts för att konfigurera WAD, måste du logga in på den virtuella datorn och öppna loggfilen på den här platsen: 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
Du kan söka efter strängen ”WadCfg” i filen, och du hittar de inställningar som skickades till den virtuella datorn för att konfigurera WAD. Du kan kontrollera den iKey som används av profiler-mottagare är korrekt.

Dessutom kan du kontrollera den kommandorad som används för att starta profiler. Följande fil innehåller de argument som används för att starta profiler.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Kontrollera att nyckeln på kommandoraden profiler är korrekt. 

Det tredje med hjälp av sökvägen i filen config.json, kontrollera loggfilen profiler. Den visas som anger inställningar med hjälp av profiler och status och felmeddelanden från profiler felsökningsinformation. Om profiler körs medan programmet tar emot begäranden, visas det här meddelandet: Aktivitet identifierad från iKey. När spårningen laddas, visas det här meddelandet: Starta överföring av spårningen. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webbjobb]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








