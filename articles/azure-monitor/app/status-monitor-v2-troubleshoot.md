---
title: Felsökning av Azure Application Insights-agent och kända problem | Microsoft-dokument
description: De kända problemen med Application Insights Agent och felsökningsexempel. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732943"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Felsöka Application Insights Agent (tidigare namnet Status Monitor v2)

När du aktiverar övervakning kan det uppstå problem som förhindrar datainsamling.
I den här artikeln visas alla kända problem och exempel på felsökning.
Om du stöter på ett problem som inte finns med här kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Kända problem

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Motstridiga DLL-filer i en apps lagerplatskatalog

Om någon av dessa DLL finns i lagerplatskatalogen kan det hända att övervakningen misslyckas:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Vissa av dessa DLL-filer ingår i Standardappmallarna för Visual Studio, även om appen inte använder dem.
Du kan använda felsökningsverktyg för att se symptomatiskt beteende:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset och appbelastning (utan telemetri). Undersök med Sysinternals (Handle.exe och ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt med IIS-delad konfiguration

Om du har ett kluster av webbservrar kanske du använder en [delad konfiguration](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule kan inte injiceras i den här delade konfigurationen.
Kör kommandot Aktivera på varje webbserver för att installera DLL i varje servers GAC.

När du har kört kommandot Aktivera gör du så här:
1. Gå till den delade konfigurationskatalogen och leta reda på filen applicationHost.config.
2. Lägg till den här raden i modulen i konfigurationen:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS kapslade program

Vi instrumenterar inte kapslade program i IIS i version 1.0.
Vi spårar det här problemet [här.](https://github.com/microsoft/ApplicationInsights-Home/issues/369)

### <a name="advanced-sdk-configuration-isnt-available"></a>Avancerad SDK-konfiguration är inte tillgänglig.

SDK-konfigurationen exponeras inte för slutanvändaren i version 1.0.
Vi spårar det här problemet [här.](https://github.com/microsoft/ApplicationInsights-Home/issues/375)

    
    
## <a name="troubleshooting"></a>Felsökning
    
### <a name="troubleshooting-powershell"></a>Felsöka PowerShell

#### <a name="determine-which-modules-are-available"></a>Ta reda på vilka moduler som är tillgängliga
Du kan `Get-Module -ListAvailable` använda kommandot för att avgöra vilka moduler som är installerade.

#### <a name="import-a-module-into-the-current-session"></a>Importera en modul till den aktuella sessionen
Om en modul inte har lästs in i en PowerShell-session kan du läsa in den `Import-Module <path to psd1>` manuellt med kommandot.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Felsöka modulen Application Insights Agent

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Lista de kommandon som är tillgängliga i modulen Application Insights Agent
Kör kommandot `Get-Command -Module Az.ApplicationMonitor` för att hämta tillgängliga kommandon:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Ta reda på den aktuella versionen av modulen Application Insights Agent
Kör `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` kommandot för att visa följande information om modulen:
   - Version av PowerShell-modul
   - Programinsikter SDK-version
   - Filsökvägar för PowerShell-modulen
    
Granska [API-referensen](status-monitor-v2-api-reference.md) för en detaljerad beskrivning av hur du använder den här cmdleten.


### <a name="troubleshooting-running-processes"></a>Felsöka processer som körs

Du kan kontrollera processerna på den instrumenterade datorn för att avgöra om alla DLL-filer läses in.
Om övervakningen fungerar ska minst 12 DLL-filer läsas in.

Använd `Get-ApplicationInsightsMonitoringStatus -InspectProcess` kommandot för att kontrollera DLL:erna.

Granska [API-referensen](status-monitor-v2-api-reference.md) för en detaljerad beskrivning av hur du använder den här cmdleten.


### <a name="collect-etw-logs-by-using-perfview"></a>Samla in ETW-loggar med hjälp av PerfView

#### <a name="setup"></a>Installation

1. Ladda ned PerfView.exe och PerfView64.exe från [GitHub](https://github.com/Microsoft/perfview/releases).
2. Starta PerfView64.exe.
3. Expandera **avancerade alternativ**.
4. Avmarkera de här kryssrutorna:
    - **Zip**
    - **Sammanfoga**
    - **.NET-symbolsamling**
5. Ange dessa **ytterligare leverantörer:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Samla in loggar

1. I en kommandokonsol med administratörsbehörighet kör du `iisreset /stop` kommandot för att inaktivera IIS och alla webbappar.
2. Välj **Starta samling**i PerfView .
3. I en kommandokonsol med administratörsbehörighet kör du `iisreset /start` kommandot för att starta IIS.
4. Försök att bläddra till din app.
5. När appen har lästs in går du tillbaka till PerfView och väljer **Stoppa samling**.



## <a name="next-steps"></a>Nästa steg

- Granska [API-referensen](status-monitor-v2-overview.md#powershell-api-reference) för att lära dig mer om parametrar som du kan ha missat.
- Om du stöter på ett problem som inte finns med här kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
