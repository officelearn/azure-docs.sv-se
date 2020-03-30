---
title: API-referens för Azure Application Insights-agent
description: Api-referens för Application Insights Agent. Start-Trace. Samla in ETW-loggar från Status Monitor och Application Insights SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671230"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Program Insights Agent API: Start-ApplicationInsightsMonitoringTrace

I den här artikeln beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Samlar [in ETW-händelser](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) från den kodlösa bifoga körningen. Den här cmdleten är ett alternativ till att köra [PerfView](https://github.com/microsoft/perfview).

Insamlade händelser skrivs ut till konsolen i realtid och sparas i en ETL-fil. Utdata ETL-filen kan öppnas av [PerfView](https://github.com/microsoft/perfview) för vidare undersökning.

Den här cmdleten körs tills timeout-varaktigheten (standardtid 5`Ctrl + C`minuter) eller stoppas manuellt ( ).

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet.

## <a name="examples"></a>Exempel

### <a name="how-to-collect-events"></a>Så här samlar du in evenemang

Normalt ber vi dig att samla in händelser för att undersöka varför din ansökan inte instrumenteras.

Den codeless bifoga körningen kommer att avge ETW händelser när IIS startar och när ditt program startar.

Så här samlar du in dessa händelser:
1. I en cmd-konsol med `iisreset /stop` administratörsbehörighet kör du Så här inaktiverar du IIS och alla webbappar.
2. Utför den här cmdleten
3. Kör `iisreset /start` Så att du startar IIS i en cmd-konsol med administratörsbehörighet.
4. Försök att bläddra till din app.
5. När appen har lästs in kan du`Ctrl + C`manuellt stoppa den ( ) eller vänta på tidsgränsen.

### <a name="what-events-to-collect"></a>Vilka händelser att samla in

Du har tre alternativ när du samlar in evenemang:
1. Använd växeln `-CollectSdkEvents` för att samla in händelser som skickas från Application Insights SDK.
2. Använd växeln `-CollectRedfieldEvents` för att samla in händelser som avges av Status Monitor och Redfield Runtime. Dessa loggar är användbara när du diagnostiserar IIS och programstart.
3. Använd båda växlarna för att samla in båda händelsetyperna.
4. Som standard, om ingen växel anges kommer båda händelsetyperna att samlas in.


## <a name="parameters"></a>Parametrar

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Valfri.** Använd den här parametern för att ange hur länge skriptet ska samla in händelser. Standardvärdet är 5 minuter.

### <a name="-logdirectory"></a>-LogDirectory
**Valfri.** Använd den här växeln för att ställa in utdatakatalogen för ETL-filen. Som standard skapas den här filen i powershellmodulkatalogen. Den fullständiga sökvägen visas under skriptkörningen.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Valfri.** Använd den här växeln för att samla in SDK-händelser för Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Valfri.** Använd den här växeln för att samla in händelser från Status Monitor och Redfield-körningen.

### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att mata ut detaljerade loggar.



## <a name="output"></a>Resultat


### <a name="example-of-application-startup-logs"></a>Exempel på startloggar för program
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Nästa steg

Ytterligare felsökning:

- Läs ytterligare felsökningssteg här:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Granska [API-referensen](status-monitor-v2-overview.md#powershell-api-reference) om du vill veta mer om parametrar som du kan ha missat.
- Om du behöver ytterligare hjälp kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att dina inställningar har spelats in korrekt.
 - [Få status för](status-monitor-v2-api-get-status.md) att inspektera övervakningen.
