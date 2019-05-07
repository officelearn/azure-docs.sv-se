---
title: 'Azure-Status Monitor v2 API-referens: Ange config | Microsoft Docs'
description: Status Monitor v2 API-referens Set-ApplicationInsightsMonitoringConfig. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 953edcb98de6ea705721aef0922562d23e18f0f5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148252"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Status Monitor v2 API: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Det här dokumentet beskriver en cmdlet som levereras som en medlem i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Beskrivning

Ställa in konfigurationsfilen utan att behöva upprepa en fullständig ominstallation. Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-Session med administratörsbehörighet.


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


### <a name="-verbose"></a>-Verbose
**Vanliga Parameter.** Använd den här växeln för att mata ut detaljerade loggar.


## <a name="output"></a>Resultat

Inga utdata som standard.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exempel utförliga utdata från inställningen konfigurationsfilen via - InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exempel utförliga utdata från inställningen konfigurationsfilen via - InstrumentationKeyMap

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
