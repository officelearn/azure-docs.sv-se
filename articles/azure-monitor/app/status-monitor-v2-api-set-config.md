---
title: API-referens för Azure Application Insights-agent
description: Api-referens för Application Insights Agent. Set-ApplicationInsightsMonitoringConfig. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671247"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Api för programinsikter agent: Set-ApplicationInsightsMonitoringConfig

I det här dokumentet beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Ställer in config-filen utan att göra en fullständig ominstallation.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet.


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
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** är en obligatorisk C# regex för datorn eller VM-namnet.
    - '.*' matchar alla
    - "ComputerName" matchar bara datorer med det angivna namnet.
- **AppFilter** är en obligatorisk C# regex för datorn eller VM-namnet.
    - '.*' matchar alla
    - ApplicationName matchar endast IIS-appar med det angivna namnet.
- **InstrumentationKey** krävs för att möjliggöra övervakning av de appar som matchar de två föregående filtren.
    - Lämna det här värdet null om du vill definiera regler för att utesluta övervakning.


### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.


## <a name="output"></a>Resultat

Som standard ingen utdata.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exempel på utförliga utdata från inställningen av config-filen via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exempel på utförliga utdata från inställningen av config-filen via -InstrumentationKeyMap

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
 - [Utforska mätvärden](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
- [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal
 
 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att dina inställningar har spelats in korrekt.
 - [Få status för](status-monitor-v2-api-get-status.md) att inspektera övervakningen.
