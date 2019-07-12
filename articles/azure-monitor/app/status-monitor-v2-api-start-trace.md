---
title: 'Azure statusövervakaren v2 API-referens: Starta spårning | Microsoft Docs'
description: Status Monitor v2 API-referens. Start-spårning. Samla in ETW-loggar från Status Monitor och Application Insights SDK.
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
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807062"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>Status Monitor v2 API: Start-ApplicationInsightsMonitoringTrace (v0.4.0-alfa)

Den här artikeln beskrivs en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Samlar in [ETW-händelser](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) koppla från den kodlös runtime. Denna cmdlet är ett alternativ till att köra [PerfView](https://github.com/microsoft/perfview).

Insamlade händelser skrivs till konsolen i realtid och sparas i en ETL-fil. ETL utdatafilen kan öppnas av [PerfView](https://github.com/microsoft/perfview) för vidare studier.

Denna cmdlet kommer att köras tills den når tidsgränsen varaktighet (som standard 5 minuter) eller stoppas manuellt (`Ctrl + C`).

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter.

## <a name="examples"></a>Exempel

### <a name="how-to-collect-events"></a>Hur du samlar in händelser

Normalt skulle vi ber du samla in händelser för att undersöka varför ditt program inte att instrumenteras.

Den kodlös bifoga runtime genererar ETW-händelser när IIS startas och när programmet startas.

Att samla in dessa händelser:
1. I en cmd-konsol med administratörsbehörigheter, kör `iisreset /stop` att stänga av IIS och alla web apps.
2. Kör denna cmdlet
3. I en cmd-konsol med administratörsbehörigheter, kör `iisreset /start` att starta IIS.
4. Försök att bläddra till din app.
5. När din app är klar läses in, kan du manuellt stoppa (`Ctrl + C`) eller vänta tills tidsgränsen.

### <a name="what-events-to-collect"></a>Vilka händelser ska samlas in

Vid insamling av händelser har du tre alternativ:
1. Växeln `-CollectSdkEvents` att samla in händelser som sänts ut från Application Insights SDK.
2. Växeln `-CollectRedfieldEvents` att samla in händelser som genereras av Status Monitor och Redfield-Runtime. Dessa loggar är användbara vid diagnostisering av IIS och start av program.
3. Använda båda växlar för att samla in båda händelsetyperna av.
4. Som standard, om någon växel har angetts både händelsetyper kommer att samlas in.


## <a name="parameters"></a>Parametrar

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Valfritt.** Använd den här parametern om du vill ange hur länge skriptet ska samla in händelser. Standardvärdet är 5 minuter.

### <a name="-logdirectory"></a>-LogDirectory
**Valfritt.** Använd den här växeln om du vill ange katalogen för ETL-fil. Den här filen kommer att skapas i katalogen PowerShell-moduler som standard. Den fullständiga sökvägen visas under skriptkörningen.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Valfritt.** Använd den här växeln för att samla in händelser för Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Valfritt.** Använd den här växeln för att samla in händelser från Status Monitor och Redfield runtime.

### <a name="-verbose"></a>-Verbose
**Vanliga parameter.** Använd den här växeln för att mata ut detaljerade loggar.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Exempel på Start-programloggar
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

- Granska ytterligare felsökningsstegen här: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Granska den [API-referens](status-monitor-v2-overview.md#powershell-api-reference) vill veta mer om parametrar som du kan ha missat.
- Om du behöver ytterligare hjälp kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Gör mer med Status Monitor v2:
 - Använd vår guide om hur du [felsöka](status-monitor-v2-troubleshoot.md) statusövervakaren v2.
 - [Hämta konfig](status-monitor-v2-api-get-config.md) att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) att inspektera övervakning.
