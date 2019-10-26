---
title: API-referens för Azure Application Insights-agent
description: Application Insights Agent-API-referens. Enable-ApplicationInsightsMonitoring. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dccd7e617174bef4a85cb6293cbcc459542310f9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899712"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights Agent-API: Enable-ApplicationInsightsMonitoring

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Möjliggör kod kopplings övervakning av IIS-appar på en måldator.

Denna cmdlet ändrar IIS applicationHost. config och anger vissa register nycklar.
Dessutom skapas en applicationinsights. iKey. config-fil som definierar Instrumentation-nyckeln som används av varje app.
IIS läser in RedfieldModule vid start, som kommer att mata in Application Insights SDK i program när programmen startas.
Starta om IIS för att ändringarna ska börja gälla.

När du har aktiverat övervakning rekommenderar vi att du använder [Live-mått](live-stream.md) för att snabbt kontrol lera om din app skickar oss telemetri.


> [!NOTE] 
> - Du behöver en Instrumentation-nyckel för att komma igång. Mer information finns i [skapa en resurs](create-new-resource.md#copy-the-instrumentation-key).
> - Denna cmdlet kräver att du granskar och godkänner vår licens-och sekretess policy.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet och en förhöjd princip för körning. Mer information finns i [kör PowerShell som administratör med en förhöjd körnings princip](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exempel

### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda Instrumentation-nyckel
I det här exemplet tilldelas alla appar på den aktuella datorn en enda Instrumentation-nyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumentande nyckel karta
I det här exemplet:
- `MachineFilter` matchar den aktuella datorn genom att använda jokertecknet `'.*'`.
- `AppFilter='WebAppExclude'` har en `null` Instrumentation-nyckel. Den angivna appen instrumenteras inte.
- `AppFilter='WebAppOne'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- `AppFilter='WebAppTwo'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- Slutligen använder `AppFilter` även jokertecknet `'.*'` för att matcha alla webbappar som inte matchar de tidigare reglerna och tilldela en standard Instrumentation-nyckel.
- Blank steg har lagts till för läsbarhet.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametrar

### <a name="-instrumentationkey"></a>– InstrumentationKey
**Kunna.** Använd den här parametern för att ange en enskild Instrumentation-nyckel som kan användas av alla appar på mål datorn.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Kunna.** Använd den här parametern för att ange flera instrument nycklar och en mappning av de Instrumentation-nycklar som används av varje app.
Du kan skapa ett enda installations skript för flera datorer genom att ange `MachineFilter`.

> [!IMPORTANT]
> Apparna kommer att matchas mot regler i den ordning som reglerna anges. Därför bör du ange de mest aktuella reglerna först och de mest allmänna reglerna sist.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** är ett obligatoriskt C# regex för datorn eller namnet på den virtuella datorn.
    - ". *" matchar alla
    - ComputerName matchar bara datorer med det angivna namnet.
- **AppFilter** är ett obligatoriskt C# regex för IIS-webbplatsens namn. Du kan hämta en lista över platser på servern genom att köra kommandot [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - ". *" matchar alla
    - "Webbplats namn" kommer bara att matcha IIS-platsen med det angivna namnet.
- **InstrumentationKey** krävs för att kunna övervaka appar som matchar de föregående två filtren.
    - Lämna värdet null om du vill definiera regler för att undanta övervakning.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Valfritt.** Använd den här växeln för att låta instrument motorn samla in händelser och meddelanden om vad som händer under körningen av en hanterad process. Dessa händelser och meddelanden innehåller beroende resultat koder, HTTP-verb och SQL-kommando text.

Instrumentation-motorn lägger till overhead och är inaktive rad som standard.

### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln för att godkänna licens-och sekretess policyn i konsolbaserade installationer.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
När du har ett kluster av webb servrar kan du använda en [delad konfiguration](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Det går inte att mata in HttpModule i denna delade konfiguration.
Det här skriptet kommer inte att fungera med meddelandet om att extra installations steg krävs.
Använd den här växeln för att ignorera den här kontrollen och fortsätta installera nödvändiga komponenter. Mer information finns i [känd konflikt med-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.

### <a name="-whatif"></a>-WhatIf 
**Gemensam parameter.** Använd den här växeln för att testa och validera dina indataparametrar utan att aktivera övervakning.

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
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrument paneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) för att se till att din webbplats hålls Live.
- [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop.
 
 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) för att inspektera övervakning.
