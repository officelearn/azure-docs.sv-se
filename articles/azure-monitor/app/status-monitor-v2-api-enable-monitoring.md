---
title: 'Azure statusövervakaren v2 API-referens: Aktivera övervakning | Microsoft Docs'
description: Status Monitor v2 API-referens. Enable-ApplicationInsightsMonitoring. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734175"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>Status Monitor v2 API: Aktivera ApplicationInsightsMonitoring (v0.2.1-alfa)

Den här artikeln beskrivs en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Aktiverar kodlös koppla övervakning av IIS-appar på en måldator.

Denna cmdlet ska ändra IIS-applicationHost.config och ange vissa registernycklar.
Dessutom skapas en applicationinsights.ikey.config-fil som definierar instrumenteringsnyckeln som används av varje app.
IIS läser in RedfieldModule vid start, som matar in Application Insights SDK i program när program börjar.
Starta om IIS för att ändringarna ska börja gälla.

När du aktiverar övervakning, rekommenderar vi att du använder [Live Metrics](live-stream.md) att snabbt kontrollera om din app skickar oss telemetri.


> [!NOTE] 
> - För att komma igång behöver du en instrumenteringsnyckel. Mer information finns i [skapa en resurs](create-new-resource.md#copy-the-instrumentation-key).
> - Denna cmdlet kräver att du granskar och accepterar du våra licens och sekretess-instruktion.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter och en princip för upphöjd körning. Mer information finns i [Kör PowerShell som administratör med en upphöjd körningsprincip](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exempel

### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda instrumentationsnyckel
I det här exemplet tilldelas alla appar på den aktuella datorn för en enda instrumentationsnyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en karta för instrumentation
I det här exemplet:
- `MachineFilter` matchar den aktuella datorn med hjälp av den `'.*'` med jokertecken.
- `AppFilter='WebAppExclude'` tillhandahåller en `null` instrumenteringsnyckeln. Den angivna appen kommer inte att instrumenteras.
- `AppFilter='WebAppOne'` tilldelar den angivna appen en unik instrumentationsnyckel.
- `AppFilter='WebAppTwo'` tilldelar den angivna appen en unik instrumentationsnyckel.
- Slutligen `AppFilter` använder också den `'.*'` med jokertecken för att matcha alla webbprogram som inte matchas av tidigare regler och tilldela en standard-instrumentationsnyckel.
- Lagringsutrymmen har lagts till för läsbarhet.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametrar

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Krävs.** Använd den här parametern om du vill definiera en enda instrumenteringsnyckeln för användning av alla appar på måldatorn.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Krävs.** Använd den här parametern om du vill definiera flera instrumenteringsnycklar och en mappning av instrumenteringsnycklar som används av varje app.
Du kan skapa en enda installationsskriptet för flera datorer genom att ange `MachineFilter`.

> [!IMPORTANT]
> Appar kommer att matcha mot regler i den ordning som reglerna som har angetts. Därför bör du ange de mest specifika reglerna först och mest allmänna reglerna för senaste.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** krävs en C# regex på datorn eller namn på virtuell dator.
    - ”. *” matchar alla
    - ”Datornamn” matchar bara datorer med det exakta namnet som angetts.
- **AppFilter** krävs en C# regex på datorn eller namn på virtuell dator.
    - ”. *” matchar alla
    - {ApplicationName} matchar IIS-appar med det exakta namnet som angetts.
- **InstrumentationKey** krävs för att aktivera övervakning av appar som matchar de föregående två filter.
    - Lämna det här värdet tomt om du vill definiera regler för att undanta övervakning.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Valfritt.** Använd den här växeln om du vill aktivera instrumentation motorn att samla in händelser och meddelanden om vad som händer under körningen av en hanterad process. Dessa händelser och meddelanden innehåller beroende resultatkoder, HTTP-verb och SQL-Kommandotext.

Motorn för instrumentation kräver och är inaktiverat som standard.

### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln om du vill acceptera instruktionen licens och sekretess i fjärradministrerad installationer.

### <a name="-verbose"></a>-Verbose
**Vanliga parameter.** Använd den här växeln om du vill visa detaljerade loggar.

### <a name="-whatif"></a>-WhatIf 
**Vanliga parameter.** Använd den här växeln för att testa och validera indataparametrarna utan att faktiskt aktivera övervakning.

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
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem.
- [Använda Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och för att aktivera spårning av anrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop.
 
 Gör mer med Status Monitor v2:
 - Använd vår guide om hur du [felsöka](status-monitor-v2-troubleshoot.md) statusövervakaren v2.
 - [Hämta konfig](status-monitor-v2-api-get-config.md) att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) att inspektera övervakning.
