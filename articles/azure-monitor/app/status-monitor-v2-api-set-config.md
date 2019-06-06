---
title: 'Azure statusövervakaren v2 API-referens: Ange config | Microsoft Docs'
description: Status Monitor v2 API-referens. Set-ApplicationInsightsMonitoringConfig. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734988"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Status Monitor v2 API: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Det här dokumentet beskriver en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Anger konfigurationsfilen utan att behöva göra en fullständig ominstallation.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter.


## <a name="examples"></a>Exempel

### <a name="example-with-a-single-instrumentation-key"></a>Exempel med en enda instrumentationsnyckel
Alla appar på den aktuella datorn kommer att tilldelas en enda instrumentationsnyckel i det här exemplet.

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
    - ”Datornamn” matchar bara datorer med det angivna namnet.
- **AppFilter** krävs en C# regex på datorn eller namn på virtuell dator.
    - ”. *” matchar alla
    - {ApplicationName} matchar endast IIS appar med det angivna namnet.
- **InstrumentationKey** krävs för att aktivera övervakning av appar som matchar de föregående två filter.
    - Lämna det här värdet tomt om du vill definiera regler för att undanta övervakning.


### <a name="-verbose"></a>-Verbose
**Vanliga parameter.** Använd den här växeln om du vill visa detaljerade loggar.


## <a name="output"></a>Resultat

Som standard inga utdata.

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
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem.
- [Använda Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och för att aktivera spårning av anrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop
 
 Gör mer med Status Monitor v2:
 - Använd vår guide om hur du [felsöka](status-monitor-v2-troubleshoot.md) statusövervakaren v2.
 - [Hämta konfig](status-monitor-v2-api-get-config.md) att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) att inspektera övervakning.
