---
title: Azure Application Insights .Net Agent API-referens
description: Api-referens för Application Insights Agent. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733679"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>API-referens för Azure Monitor Application Insights-agent

I den här artikeln beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - För att komma igång behöver du en instrumenteringsnyckel. Mer information finns i [Skapa en resurs](create-new-resource.md#copy-the-instrumentation-key).
> - Denna cmdlet kräver att du granskar och accepterar vår licens och sekretesspolicy.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet och en upphöjd körningsprincip. Mer information finns i [Kör PowerShell som administratör med en förhöjd körningsprincip](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Denna cmdlet kräver att du granskar och accepterar vår licens och sekretesspolicy.
> - Instrumenteringsmotorn lägger till ytterligare omkostnader och är avstängd som standard.


## <a name="enable-instrumentationengine"></a>Aktivera instrumenteringEngine

Aktiverar instrumenteringsmotorn genom att ställa in några registernycklar.
Starta om IIS för att ändringarna ska börja gälla.

Instrumenteringsmotorn kan komplettera data som samlats in av .NET SDK:er.
Den samlar in händelser och meddelanden som beskriver körningen av en hanterad process. Dessa händelser och meddelanden inkluderar beroenderesultatkoder, [HTTP-verb och SQL-kommandotext](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Aktivera instrumenteringsmotorn om
- Du har redan aktiverat övervakning med cmdlet aktivera men inte aktiverat instrumenteringsmotorn.
- Du har instrumenterat appen manuellt med .NET SDK:er och vill samla in ytterligare telemetri.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametrar

#### <a name="-acceptlicense"></a>-AccepteraLicens
**Valfri.** Använd den här växeln för att acceptera licens- och sekretesspolicyn i huvudlösa installationer.

#### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att mata ut detaljerade loggar.

### <a name="output"></a>Resultat


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exempel på utdata från att framgångsrikt aktivera instrumenteringsmotorn

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Aktivera-ApplicationInsightsMonitoring

Aktiverar kodlös bifoga övervakning av IIS-appar på en måldator.

Den här cmdleten kommer att ändra IIS applicationHost.config och ange några registernycklar.
Det kommer också att skapa en applicationinsights.ikey.config-fil, som definierar instrumenteringsnyckeln som används av varje app.
IIS kommer att läsa in RedfieldModule vid start, vilket kommer att injicera Application Insights SDK i program när programmen startar.
Starta om IIS för att ändringarna ska börja gälla.

När du har aktiverat övervakning rekommenderar vi att du använder [Live Metrics](live-stream.md) för att snabbt kontrollera om din app skickar telemetri till oss.

### <a name="examples"></a>Exempel

#### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda instrumenteringsnyckel
I det här exemplet tilldelas alla appar på den aktuella datorn en enda instrumenteringsnyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumenteringsnyckelkarta
I det här exemplet:
- `MachineFilter`matchar den aktuella `'.*'` datorn med jokertecknet.
- `AppFilter='WebAppExclude'`ger `null` en instrumenteringsnyckel. Den angivna appen kommer inte att instrumenteras.
- `AppFilter='WebAppOne'`tilldelar den angivna appen en unik instrumenteringsnyckel.
- `AppFilter='WebAppTwo'`tilldelar den angivna appen en unik instrumenteringsnyckel.
- Slutligen `AppFilter` använder du `'.*'` även jokertecknet för att matcha alla webbappar som inte matchas av de tidigare reglerna och tilldela en standardinstrumenteringsnyckel.
- Blanksteg läggs till för läsbarhet.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Parametrar

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Krävs.** Använd den här parametern för att ange en enda instrumenteringsnyckel för användning av alla appar på måldatorn.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Krävs.** Använd den här parametern för att ange flera instrumenteringsnycklar och en mappning av instrumenteringsnycklarna som används av varje app.
Du kan skapa ett enda installationsskript `MachineFilter`för flera datorer genom att ange .

> [!IMPORTANT]
> Appar matchar reglerna i den ordning som reglerna tillhandahålls. Så du bör ange de mest specifika reglerna först och de mest allmänna reglerna sist.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** är en obligatorisk C# regex för datorn eller VM-namnet.
    - '.*' matchar alla
    - "ComputerName" matchar bara datorer med det exakta angivna namnet.
- **AppFilter** är en obligatorisk C# regex för IIS-platsnamnet. Du kan få en lista över webbplatser på din server genom att köra kommandot [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' matchar alla
    - "SiteName" matchar endast IIS-webbplatsen med det exakta angivna namnet.
- **InstrumentationKey** krävs för att möjliggöra övervakning av appar som matchar de två föregående filtren.
    - Lämna det här värdet null om du vill definiera regler för att utesluta övervakning.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Valfri.** Använd den här växeln för att aktivera instrumenteringsmotorn för att samla in händelser och meddelanden om vad som händer under körningen av en hanterad process. Dessa händelser och meddelanden inkluderar beroenderesultatkoder, HTTP-verb och SQL-kommandotext.

Instrumenteringsmotorn lägger till omkostnader och är avstängd som standard.

#### <a name="-acceptlicense"></a>-AccepteraLicens
**Valfri.** Använd den här växeln för att acceptera licens- och sekretesspolicyn i huvudlösa installationer.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
När du har ett kluster av webbservrar kanske du använder en [delad konfiguration](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule kan inte injiceras i den här delade konfigurationen.
Det här skriptet misslyckas med meddelandet att extra installationssteg krävs.
Använd den här växeln om du vill ignorera den här kontrollen och fortsätta installera förutsättningar. Mer information finns i [känd konflikt med-iis-delad konfiguration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.

#### <a name="-whatif"></a>-WhatIf 
**Gemensam parameter.** Använd den här växeln för att testa och validera indataparametrarna utan att aktivera övervakning.

### <a name="output"></a>Resultat

#### <a name="example-output-from-a-successful-enablement"></a>Exempel på utdata från en lyckad aktivering

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Inaktivera instrumenteringsengine

Inaktiverar instrumenteringsmotorn genom att ta bort några registernycklar.
Starta om IIS för att ändringarna ska börja gälla.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametrar 

#### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att mata ut detaljerade loggar.

### <a name="output"></a>Resultat


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exempel på utdata från att framgångsrikt inaktivera instrumenteringsmotorn

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Inaktivera-ApplicationInsightsMonitoring

Inaktiverar övervakning på måldatorn.
Den här cmdleten tar bort redigeringar av IIS applicationHost.config och tar bort registernycklar.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametrar 

#### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.

### <a name="output"></a>Resultat


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Exempel på utdata från att inaktivera övervakning

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Hämta programInightsMonitoringConfig

Hämtar konfigurationsfilen och skriver ut värdena till konsolen.

### <a name="examples"></a>Exempel

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametrar

Inga parametrar krävs.

### <a name="output"></a>Resultat


##### <a name="example-output-from-reading-the-config-file"></a>Exempel på utdata från att läsa konfigurationsfilen

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Hämta programInightsMonitoringStatus

Den här cmdleten innehåller felsökningsinformation om statusövervakaren.
Använd den här cmdleten för att undersöka övervakningsstatus, version av PowerShell-modulen och för att inspektera den pågående processen.
Den här cmdleten rapporterar versionsinformation och information om nyckelfiler som krävs för övervakning.

### <a name="examples"></a>Exempel

#### <a name="example-application-status"></a>Exempel: Programstatus

Kör kommandot `Get-ApplicationInsightsMonitoringStatus` för att visa övervakningsstatus för webbplatser.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

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
- **Maskinidentifierare** är ett anonymt ID som används för att unikt identifiera din server. Om du skapar en supportbegäran behöver vi det här ID:t för att hitta loggar för servern.
- **Standardwebbplatsen** stoppas i IIS
- **DemoWebApp111** har startats i IIS, men har inte fått några förfrågningar. Den här rapporten visar att det inte finns någon process som körs (ProcessId: hittades inte).
- **DemoWebApp222** körs och övervakas (Instrumenterad: true). Baserat på användarkonfigurationen matchades Instrumentation Key xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 för den här webbplatsen.
- **DemoWebApp333** har instrumenterats manuellt med hjälp av Application Insights SDK. Statusövervakaren upptäckte SDK och övervakar inte den här platsen.


#### <a name="example-powershell-module-information"></a>Exempel: Information om PowerShell-modul

Kör kommandot `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` för att visa information om den aktuella modulen:

```powershell

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

#### <a name="example-runtime-status"></a>Exempel: Körningsstatus

Du kan kontrollera processen på den instrumenterade datorn för att se om alla DLL-filer har lästs in. Om övervakningen fungerar ska minst 12 DLL-filer läsas in.

Kör kommandot: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`


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

### <a name="parameters"></a>Parametrar

#### <a name="no-parameters"></a>(Inga parametrar)

Som standard kommer den här cmdleten att rapportera övervakningsstatus för webbprogram.
Använd det här alternativet om du vill granska om programmet har instrumenterats.
Du kan också granska vilken instrumenteringsnyckel som matchades till din webbplats.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Valfritt**. Använd den här växeln för att rapportera versionsnummer och sökvägar för DLL:er som krävs för övervakning.
Använd det här alternativet om du behöver identifiera versionen av en DLL, inklusive Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspekteraProcess

**Valfritt**. Använd den här växeln för att rapportera om IIS körs.
Det kommer också att hämta externa verktyg för att avgöra om de nödvändiga DLL-filer laddas i IIS-körningen.


Om den här processen misslyckas av någon anledning kan du köra dessa kommandon manuellt:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Valfritt**. Används endast med InspectProcess. Använd den här växeln för att hoppa över användarprompten som visas innan ytterligare verktyg hämtas.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Ställer in config-filen utan att göra en fullständig ominstallation.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet.


### <a name="examples"></a>Exempel

#### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda instrumenteringsnyckel
I det här exemplet tilldelas alla appar på den aktuella datorn en enda instrumenteringsnyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumenteringsnyckelkarta
I det här exemplet:
- `MachineFilter`matchar den aktuella `'.*'` datorn med jokertecknet.
- `AppFilter='WebAppExclude'`ger `null` en instrumenteringsnyckel. Den angivna appen kommer inte att instrumenteras.
- `AppFilter='WebAppOne'`tilldelar den angivna appen en unik instrumenteringsnyckel.
- `AppFilter='WebAppTwo'`tilldelar den angivna appen en unik instrumenteringsnyckel.
- Slutligen `AppFilter` använder du `'.*'` även jokertecknet för att matcha alla webbappar som inte matchas av de tidigare reglerna och tilldela en standardinstrumenteringsnyckel.
- Blanksteg läggs till för läsbarhet.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametrar

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Krävs.** Använd den här parametern för att ange en enda instrumenteringsnyckel för användning av alla appar på måldatorn.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Krävs.** Använd den här parametern för att ange flera instrumenteringsnycklar och en mappning av instrumenteringsnycklarna som används av varje app.
Du kan skapa ett enda installationsskript `MachineFilter`för flera datorer genom att ange .

> [!IMPORTANT]
> Appar matchar reglerna i den ordning som reglerna tillhandahålls. Så du bör ange de mest specifika reglerna först och de mest allmänna reglerna sist.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** är en obligatorisk C# regex för datorn eller VM-namnet.
    - '.*' matchar alla
    - "ComputerName" matchar bara datorer med det angivna namnet.
- **AppFilter** är en obligatorisk C# regex för datorn eller VM-namnet.
    - '.*' matchar alla
    - ApplicationName matchar endast IIS-appar med det angivna namnet.
- **InstrumentationKey** krävs för att möjliggöra övervakning av de appar som matchar de två föregående filtren.
    - Lämna det här värdet null om du vill definiera regler för att utesluta övervakning.


#### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.


### <a name="output"></a>Resultat

Som standard ingen utdata.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exempel på utförliga utdata från inställningen av config-filen via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exempel på utförliga utdata från inställningen av config-filen via -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ProgramInsightsMonitoringTrace

Samlar [in ETW-händelser](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) från den kodlösa bifoga körningen. Den här cmdleten är ett alternativ till att köra [PerfView](https://github.com/microsoft/perfview).

Insamlade händelser skrivs ut till konsolen i realtid och sparas i en ETL-fil. Utdata ETL-filen kan öppnas av [PerfView](https://github.com/microsoft/perfview) för vidare undersökning.

Den här cmdleten körs tills timeout-varaktigheten (standardtid 5`Ctrl + C`minuter) eller stoppas manuellt ( ).

### <a name="examples"></a>Exempel

#### <a name="how-to-collect-events"></a>Så här samlar du in evenemang

Normalt ber vi dig att samla in händelser för att undersöka varför din ansökan inte instrumenteras.

Den codeless bifoga körningen kommer att avge ETW händelser när IIS startar och när ditt program startar.

Så här samlar du in dessa händelser:
1. I en cmd-konsol med `iisreset /stop` administratörsbehörighet kör du Så här inaktiverar du IIS och alla webbappar.
2. Utför den här cmdleten
3. Kör `iisreset /start` Så att du startar IIS i en cmd-konsol med administratörsbehörighet.
4. Försök att bläddra till din app.
5. När appen har lästs in kan du`Ctrl + C`manuellt stoppa den ( ) eller vänta på tidsgränsen.

#### <a name="what-events-to-collect"></a>Vilka händelser att samla in

Du har tre alternativ när du samlar in evenemang:
1. Använd växeln `-CollectSdkEvents` för att samla in händelser som skickas från Application Insights SDK.
2. Använd växeln `-CollectRedfieldEvents` för att samla in händelser som avges av Status Monitor och Redfield Runtime. Dessa loggar är användbara när du diagnostiserar IIS och programstart.
3. Använd båda växlarna för att samla in båda händelsetyperna.
4. Som standard, om ingen växel anges kommer båda händelsetyperna att samlas in.


### <a name="parameters"></a>Parametrar

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Valfri.** Använd den här parametern för att ange hur länge skriptet ska samla in händelser. Standardvärdet är 5 minuter.

#### <a name="-logdirectory"></a>-LogDirectory
**Valfri.** Använd den här växeln för att ställa in utdatakatalogen för ETL-filen. Som standard skapas den här filen i powershellmodulkatalogen. Den fullständiga sökvägen visas under skriptkörningen.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Valfri.** Använd den här växeln för att samla in SDK-händelser för Application Insights.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Valfri.** Använd den här växeln för att samla in händelser från Status Monitor och Redfield-körningen.

#### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att mata ut detaljerade loggar.



### <a name="output"></a>Resultat


#### <a name="example-of-application-startup-logs"></a>Exempel på startloggar för program
```powershell
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

  Visa telemetrin:
 - [Utforska mätvärden](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- Använd [analyser](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
- [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal.
 
 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.






