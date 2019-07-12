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
ms.openlocfilehash: e579db587d5f56aecd60f584ea4805dd4ac1bf98
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718354"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v040-alpha"></a>Status Monitor v2 API: Get-ApplicationInsightsMonitoringStatus (v0.4.0-alpha)

Den här artikeln beskrivs en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Denna cmdlet felsökningsinformation om statusövervakaren.
Använd denna cmdlet ska undersökas övervakningsstatus version av PowerShell-modulen och inspektera processen.
Denna cmdlet rapporterar versionsinformation och information om viktiga filer som krävs för att övervaka.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter.

## <a name="examples"></a>Exempel

### <a name="example-application-status"></a>Exempel: Programstatus

Kör kommandot `Get-ApplicationInsightsMonitoringStatus` ska visas övervakningsstatus för webbplatser.

```
Machine Identifier:
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

I det här exemplet;
- **Datorn identifierare** är en anonym ID som används för att identifiera din server. Om du skapar en supportbegäran om behöver vi detta ID för att hitta loggar för din server.
- **Standardwebbplats** har stoppats i IIS
- **DemoWebApp111** har startats i IIS, men har inte mottagit några förfrågningar. Den här rapporten visar att det finns ingen pågående process (process-ID: Det gick inte att hitta).
- **DemoWebApp222** körs och övervakas (Instrumented: SANT). Utifrån Användarkonfiguration matchades Instrumenteringsnyckeln xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 för den här platsen.
- **DemoWebApp333** har instrumenterats manuellt med hjälp av Application Insights SDK. Statusövervakaren identifierat SDK och övervakas inte den här platsen.


### <a name="example-powershell-module-information"></a>Exempel: PowerShell-modulinformation

Kör kommandot `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` att visa information om den aktuella modulen:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
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

Som standard rapporterar denna cmdlet övervakningsstatus för webbprogram.
Använd det här alternativet för att granska om ditt program har instrumenterade.
Du kan också granska som Instrumenteringsnyckeln matchades din webbplats.


### <a name="-powershellmodule"></a>-PowerShellModule
**Valfritt**. Använd den här växeln om du vill rapportera versionsnummer och sökvägar för DLL-filer som krävs för att övervaka.
Använd det här alternativet om du vill identifiera alla DLL-filen, inklusive Application Insights SDK-version.

### <a name="-inspectprocess"></a>-InspectProcess

**Valfritt**. Använd den här växeln om du vill rapportera om IIS körs.
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
