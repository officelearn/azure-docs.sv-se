---
title: 'Azure-Status Monitor v2 API-referens: Aktivera övervakning | Microsoft Docs'
description: Status Monitor v2 API-referens Enable-ApplicationInsightsMonitoring. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 17798c78b167821f5f30a11996ac90cf67fb0179
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144952"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>Status Monitor v2 API: Aktivera ApplicationInsightsMonitoring (v0.2.1-alfa)

Det här dokumentet beskriver en cmdlet som levereras som en medlem i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Beskrivning

Aktivera kod utan koppla övervakning av IIS-program på en måldator.
Denna cmdlet ska ändra IIS-applicationHost.config och ange vissa registernycklar.
Denna cmdlet skapar också en applicationinsights.ikey.config som definierar vilka instrumenteringsnyckeln används av olika program.
IIS läser in RedfieldModule vid start, som matar in Application Insights SDK i program som program startas.
Starta om IIS för att ändringarna ska börja gälla.

När du har aktiverat övervakning, bör du använda [Live Metrics](live-stream.md) att snabbt se om ditt program skickar oss telemetri.


> [!NOTE] 
> Du måste ha en instrumenteringsnyckel för att komma igång. Mer information finns i [Skapa ny resurs](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-Session med administratörsbehörigheter och med en princip för upphöjd körning. Läs [här](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) för mer information.

> [!NOTE] 
> Denna cmdlet måste du granska och Godkänn vår licens och sekretess-instruktion.


## <a name="examples"></a>Exempel

### <a name="example-with-single-instrumentation-key"></a>Exempel med enkel instrumentationsnyckel
Alla program på den aktuella datorn kommer att tilldelas en enda instrumentationsnyckel i det här exemplet.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Exempel med instrumentation karta
I det här exemplet 
- `MachineFilter` matchar den aktuella datorn med den `'.*'` med jokertecken.
- `AppFilter='WebAppExclude'` tillhandahåller en `null` InstrumentationKey. Den här appen kommer inte att instrumenteras.
- `AppFilter='WebAppOne'` tilldelar den här specifika appen en unik instrumentationsnyckel.
- `AppFilter='WebAppTwo'` tilldelar också den här specifika appen en unik instrumentationsnyckel.
- Till sist `AppFilter` använder också den `'.*'` med jokertecken för att matcha alla andra webbappar inte matchas av tidigare regler och tilldelar en standard-instrumentationsnyckel.
- Lagringsutrymmen har lagts till för läsbarhet endast.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametrar 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Krävs.** Använd den här parametern om du vill definiera en enda iKey för användning av alla program på måldatorn.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Krävs.** Använd den här parametern om du vill definiera flera ikeys och en mappning av vilka appar som ska använda vilka ikey. Du kan skapa en enda installationsskriptet för flera datorer genom att ange MachineFilter. 

> [!IMPORTANT] 
> Program kommer att matcha mot regler i den ordning som de får se. Därför bör du ange de mest specifika reglerna först och de allmänna reglerna för senaste.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** är ett obligatoriskt c#-regex namnet på datorn eller virtuell dator.
    - ”. *” matchar alla
    - ”Datornamn” matchar bara datorer med exakt samma namn.
- **AppFilter** är ett obligatoriskt c#-regex namnet på datorn eller virtuell dator.
    - ”. *” matchar alla
    - {ApplicationName} matchar endast IIS-program med det exakta namnet.
- **InstrumentationKey** krävs för att aktivera övervakning av program som matchar filtren i ovanstående två.
    - Lämna det här värdet tomt om du vill ange regler för att undanta övervakning


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Valfritt.** Använd den här växeln om du vill aktivera Instrumentation motorn att samla in händelser och meddelanden för vad som händer till under körningen av en hanterad process. Inklusive men inte begränsat till beroende resultatkoder, HTTP-verb och SQL-Kommandotext. Motorn för Instrumentation lägger till ytterligare kostnader och är inaktiverat som standard.

### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln om du vill acceptera instruktionen licens och sekretess i fjärradministrerad installationer.

### <a name="-verbose"></a>-Verbose
**Vanliga Parameter.** Använd den här växeln för att mata ut detaljerade loggar.

### <a name="-whatif"></a>-WhatIf 
**Vanliga Parameter.** Använd den här växeln för att testa och validera indataparametrarna utan att faktiskt aktivera övervakning.

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
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem
- [Analys](../../azure-monitor/app/analytics.md) för mer avancerade frågor
- [Skapa instrumentpaneler](../../azure-monitor/app/app-insights-dashboards.md)
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och så att du spårningsanrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop
 
 Gör mer med Status Monitor v2:
 - Använd vår guide om hur du [Felsök](status-monitor-v2-troubleshoot.md) statusövervakaren v2.
 - [Hämta konfig](status-monitor-v2-api-get-config.md) att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) att inspektera övervakning.
