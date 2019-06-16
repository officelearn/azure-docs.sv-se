---
title: 'Azure statusövervakaren v2 API-referens: Hämta config | Microsoft Docs'
description: Status Monitor v2 API-referens. Get-ApplicationInsightsMonitoringConfig. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514384"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>Status Monitor v2 API: Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Den här artikeln beskrivs en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Hämtar konfigurationsfilen och skriver ut värden till konsolen.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametrar

Inga parametrar som krävs.

## <a name="output"></a>Resultat


#### <a name="example-output-from-reading-the-config-file"></a>Exempel på utdata från att läsa konfigurationsfilen

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem.
- Använd [analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och för att aktivera spårning av anrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop.
 
 Gör mer med Status Monitor v2:
 - Använd vår guide om hur du [felsöka](status-monitor-v2-troubleshoot.md) statusövervakaren v2.
 - Göra ändringar i konfigurationen med hjälp av den [Set config](status-monitor-v2-api-set-config.md) cmdlet.
