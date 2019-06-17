---
title: Felsökning för Azure Status Monitor v2 och kända problem | Microsoft Docs
description: Kända problem för Status Monitor v2 och felsökning exempel. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734954"
---
# <a name="troubleshooting-status-monitor-v2"></a>Felsökningsstatus övervaka v2

När du aktiverar övervakning kan uppleva du problem som förhindrar insamling av data.
Den här artikeln visar en lista över alla kända problem och felsökning exempel.
Om du stöter på ett problem som inte visas här kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Kända problem

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Motstridiga DLL-filer i en app bin-katalogen

Övervakning kan misslyckas om någon av dessa DLL-filer finns i bin-katalogen:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Vissa av dessa DLL-filer ingår i Visual Studio app standardmallarna, även om din app inte använder dem.
Du kan använda felsökningsverktyg för att se utgående beteende:

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

- IISReset och appen att läsa in (utan telemetri). Undersök med Sysinternals (Handle.exe och ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt med delad IIS-konfiguration

Om du har ett kluster på webbservrar kan du kanske använder en [delad konfiguration](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule kan inte vara införs i den här delad konfiguration.
Kör kommandot aktivera på varje webbserver för att installera DLL-filen i GAC för varje server.

När du har kört kommandot aktivera, gör du följande:
1. Gå till katalogen delad konfiguration och hitta applicationHost.config-filen.
2. Lägg till följande rad i modulavsnittet i konfigurationen:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Felsökning
    
### <a name="troubleshooting-powershell"></a>Felsökning av PowerShell

#### <a name="determine-which-modules-are-available"></a>Avgöra vilka moduler som är tillgängliga
Du kan använda den `Get-Module -ListAvailable` kommando för att avgöra vilka moduler är installerade.

#### <a name="import-a-module-into-the-current-session"></a>Importera en modul till den aktuella sessionen
Om en modul inte har lästs in i en PowerShell-session, kan du manuellt överföra dem med hjälp av den `Import-Module <path to psd1>` kommando.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Felsökning av statusövervakaren v2-modulen

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Lista över kommandon som är tillgängliga i statusövervakaren v2-modulen
Kör kommandot `Get-Command -Module Az.ApplicationMonitor` att hämta tillgängliga kommandon:

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Fastställa den aktuella versionen av statusövervakaren v2-modulen
Kör den `Get-ApplicationInsightsMonitoringStatus` kommando för att visa följande information om modulen:
   - PowerShell-Modulversion
   - Application Insights SDK version
   - Sökvägar för PowerShell-modulen
    
Granska den [API-referens](status-monitor-v2-api-get-status.md) för en detaljerad beskrivning av hur du använder denna cmdlet.


### <a name="troubleshooting-running-processes"></a>Felsökning av processer som körs

Du kan granska processerna på instrumenterade datorn för att avgöra om alla DLL: er har lästs in.
Om övervakning fungerar, ska minst 12 DLL-filer läsas.

Använd den `Get-ApplicationInsightsMonitoringStatus -InspectProcess` kommando för att kontrollera DLL-filer.

Granska den [API-referens](status-monitor-v2-api-get-status.md) för en detaljerad beskrivning av hur du använder denna cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Samla in ETW-loggar med PerfView

#### <a name="setup"></a>Konfiguration

1. Ladda ned PerfView.exe och PerfView64.exe från [GitHub](https://github.com/Microsoft/perfview/releases).
2. Starta PerfView64.exe.
3. Expandera **avancerade alternativ**.
4. Avmarkera kryssrutorna:
    - **Zip**
    - **Sammanfoga**
    - **.NET symbolen samling**
5. Ange dessa **ytterligare Providers**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Samla in loggar

1. I Kommandotolken med administratörsbehörigheter, kör den `iisreset /stop` för att Stäng av IIS och alla webbappar.
2. Välj i PerfView, **starta insamling**.
3. I Kommandotolken med administratörsbehörigheter, kör den `iisreset /start` kommando för att starta IIS.
4. Försök att bläddra till din app.
5. När appen har lästs in, gå tillbaka till PerfView och välj **stoppa insamling**.



## <a name="next-steps"></a>Nästa steg

- Granska den [API-referens](status-monitor-v2-overview.md#powershell-api-reference) vill veta mer om parametrar som du kan ha missat.
- Om du stöter på ett problem som inte visas här kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
