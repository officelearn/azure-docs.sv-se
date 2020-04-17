---
title: API-referens för Azure Application Insights-agent
description: Api-referens för Application Insights Agent. Aktivera-ApplicationInsightsMonitoring. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536751"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Api för programinsikter agent: Aktivera-ApplicationInsightsMonitoring

I den här artikeln beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Aktiverar kodlös bifoga övervakning av IIS-appar på en måldator.

Den här cmdleten kommer att ändra IIS applicationHost.config och ange några registernycklar.
Det kommer också att skapa en applicationinsights.ikey.config-fil, som definierar instrumenteringsnyckeln som används av varje app.
IIS kommer att läsa in RedfieldModule vid start, vilket kommer att injicera Application Insights SDK i program när programmen startar.
Starta om IIS för att ändringarna ska börja gälla.

När du har aktiverat övervakning rekommenderar vi att du använder [Live Metrics](live-stream.md) för att snabbt kontrollera om din app skickar telemetri till oss.


> [!NOTE] 
> - För att komma igång behöver du en instrumenteringsnyckel. Mer information finns i [Skapa en resurs](create-new-resource.md#copy-the-instrumentation-key).
> - Denna cmdlet kräver att du granskar och accepterar vår licens och sekretesspolicy.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet och en upphöjd körningsprincip. Mer information finns i [Kör PowerShell som administratör med en förhöjd körningsprincip](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exempel

### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda instrumenteringsnyckel
I det här exemplet tilldelas alla appar på den aktuella datorn en enda instrumenteringsnyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumenteringsnyckelkarta
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


## <a name="parameters"></a>Parametrar

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Krävs.** Använd den här parametern för att ange en enda instrumenteringsnyckel för användning av alla appar på måldatorn.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Krävs.** Använd den här parametern för att ange flera instrumenteringsnycklar och en mappning av instrumenteringsnycklarna som används av varje app.
Du kan skapa ett enda installationsskript `MachineFilter`för flera datorer genom att ange .

> [!IMPORTANT]
> Appar matchar reglerna i den ordning som reglerna tillhandahålls. Så du bör ange de mest specifika reglerna först och de mest allmänna reglerna sist.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** är en obligatorisk C# regex för datorn eller VM-namnet.
    - '.*' matchar alla
    - "ComputerName" matchar bara datorer med det exakta angivna namnet.
- **AppFilter** är en obligatorisk C# regex för IIS-platsnamnet. Du kan få en lista över webbplatser på din server genom att köra kommandot [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' matchar alla
    - "SiteName" matchar endast IIS-webbplatsen med det exakta angivna namnet.
- **InstrumentationKey** krävs för att möjliggöra övervakning av appar som matchar de två föregående filtren.
    - Lämna det här värdet null om du vill definiera regler för att utesluta övervakning.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Valfri.** Använd den här växeln för att aktivera instrumenteringsmotorn för att samla in händelser och meddelanden om vad som händer under körningen av en hanterad process. Dessa händelser och meddelanden inkluderar beroenderesultatkoder, HTTP-verb och SQL-kommandotext.

Instrumenteringsmotorn lägger till omkostnader och är avstängd som standard.

### <a name="-acceptlicense"></a>-AccepteraLicens
**Valfri.** Använd den här växeln för att acceptera licens- och sekretesspolicyn i huvudlösa installationer.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
När du har ett kluster av webbservrar kanske du använder en [delad konfiguration](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule kan inte injiceras i den här delade konfigurationen.
Det här skriptet misslyckas med meddelandet att extra installationssteg krävs.
Använd den här växeln om du vill ignorera den här kontrollen och fortsätta installera förutsättningar. Mer information finns i [känd konflikt med-iis-delad konfiguration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.

### <a name="-whatif"></a>-WhatIf 
**Gemensam parameter.** Använd den här växeln för att testa och validera indataparametrarna utan att aktivera övervakning.

## <a name="output"></a>Resultat


#### <a name="example-output-from-a-successful-enablement"></a>Exempel på utdata från en lyckad aktivering

```
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

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mätvärden](../../azure-monitor/platform/metrics-charts.md) för att övervaka prestanda och användning.
- [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
- [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal.
 
 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att dina inställningar har spelats in korrekt.
 - [Få status för](status-monitor-v2-api-get-status.md) att inspektera övervakningen.
