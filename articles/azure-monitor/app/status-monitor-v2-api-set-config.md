---
title: API-referens för Azure Application Insights-agent
description: Application Insights Agent-API-referens. Set-ApplicationInsightsMonitoringConfig. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 45dcd2374fc5be40f86d403f8daccf4a6f1d6997
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189430"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights Agent-API: set-ApplicationInsightsMonitoringConfig

Det här dokumentet beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Anger konfigurations filen utan att göra en fullständig ominstallation.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.


## <a name="examples"></a>Exempel

### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda Instrumentation-nyckel
I det här exemplet kommer alla appar på den aktuella datorn att tilldelas en enda Instrumentation-nyckel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exempel med en instrumentande nyckel karta
I det här exemplet:
- `MachineFilter` matchar den aktuella datorn med hjälp av `'.*'` jokertecken.
- `AppFilter='WebAppExclude'` tillhandahåller en `null` Instrumentation-nyckel. Den angivna appen instrumenteras inte.
- `AppFilter='WebAppOne'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- `AppFilter='WebAppTwo'` tilldelar den angivna appen en unik Instrumentation-nyckel.
- Slutligen använder `AppFilter` även jokertecken `'.*'` för att matcha alla webbappar som inte matchar de tidigare reglerna och tilldela en standard Instrumentation-nyckel.
- Blank steg har lagts till för läsbarhet.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** är ett obligatoriskt C# regex för datorn eller namnet på den virtuella datorn.
    - ". *" matchar alla
    - ComputerName kommer bara att matcha datorer med det angivna namnet.
- **AppFilter** är ett obligatoriskt C# regex för datorn eller namnet på den virtuella datorn.
    - ". *" matchar alla
    - ApplicationName kommer bara att matcha IIS-appar med det angivna namnet.
- **InstrumentationKey** krävs för att aktivera övervakning av de appar som matchar de föregående två filtren.
    - Lämna värdet null om du vill definiera regler för att undanta övervakning.


### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att visa detaljerade loggar.


## <a name="output"></a>Resultat

Som standard visas inga utdata.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exempel på utförlig utdata från att ställa in konfigurations filen via-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exempel på utförlig utdata från att ställa in konfigurations filen via-InstrumentationKeyMap

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
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrument paneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) för att se till att din webbplats hålls Live.
- [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop
 
 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) för att inspektera övervakning.
