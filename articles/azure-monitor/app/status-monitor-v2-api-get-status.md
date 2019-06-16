---
title: 'Azure statusövervakaren v2 API-referens: Hämta status | Microsoft Docs'
description: Status Monitor v2 API-referens. Get-ApplicationInsightsMonitoringStatus. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 860226320fe1a546798cc462e4e5c06d4b9228cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514311"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v022-alpha"></a>Status Monitor v2 API: Get-ApplicationInsightsMonitoringStatus (v0.2.2-alpha)

Den här artikeln beskrivs en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Möjliggör felsökning av PowerShell-modulen som används.
Denna cmdlet rapporterar versionsinformation och information om viktiga filer som krävs för att övervaka.
Ytterligare parametrar ger extra rapporter om statusen för övervakning.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter.

## <a name="examples"></a>Exempel


### <a name="example-basic-information"></a>Exempel: Grundläggande information

Kör `Get-ApplicationInsightsMonitoringStatus` att visa information om den aktuella modulen:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus

PowerShell Module version:
0.2.2-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content Exists: False
ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\applicationInsights.ikey.config Exists: True
ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True


Machine Identifier:
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF
```

### <a name="example-runtime-status"></a>Exempel: Körningsstatus

Du kan granska processen på instrumenterade datorn för att se om alla DLL: er har lästs in. Om övervakning fungerar, ska minst 12 DLL-filer läsas.

Kör kommandot `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>Parametrar

### <a name="no-parameters"></a>(Inga parametrar)

Som standard rapporterar denna cmdlet versionsnummer och sökvägar för DLL-filer som krävs för att övervaka.

Använd det här alternativet om du vill identifiera alla DLL-filen, inklusive Application Insights SDK-version.


### <a name="-inspectprocess"></a>-InspectProcess

**Valfritt**. Använd den här parametern om du vill rapportera om IIS körs.
Det kommer också hämta externa verktyg för att avgöra om de nödvändiga DLL-filerna läses in i IIS-runtime.


Om den här processen misslyckas av någon anledning, kan du köra dessa kommandon manuellt:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) - p w3wp | findstr /I ”InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I ”InstrumentationEngine AI ApplicationInsights”


### <a name="-force"></a>-Force

**Valfritt**. Används endast med InspectProcess. Använd den här växeln om du vill hoppa över användaruppmaning som visas innan ytterligare verktyg som hämtas.


## <a name="next-steps"></a>Nästa steg

 Gör mer med Status Monitor v2:
 - Använd vår guide om hur du [felsöka](status-monitor-v2-troubleshoot.md) statusövervakaren v2.
