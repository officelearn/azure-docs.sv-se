---
title: 'Referens för Azure Application Insights-Agent-API: Aktivera Instrumentation-motor | Microsoft Docs'
description: Application Insights Agent-API-referens. Enable-InstrumentationEngine. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 9958121c38b96dc9cfa4dda2812fa9ce2b18d785
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388292"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights Agent-API: Enable-InstrumentationEngine

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Aktiverar Instrumentation-motorn genom att ange vissa register nycklar.
Starta om IIS för att ändringarna ska börja gälla.

Instrumentation-motorn kan komplettera data som samlas in av .NET SDK: er.
Den samlar in händelser och meddelanden som beskriver körningen av en hanterad process. Dessa händelser och meddelanden innehåller beroende resultat koder, HTTP-verb och SQL- [kommando text](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Aktivera Instrumentation Engine om:
- Du har redan aktiverat övervakning med cmdleten Enable men inte aktiverat Instrumentation-motorn.
- Du har manuellt instrumenterat din app med .NET-SDK: er och vill samla in ytterligare telemetri.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.

> [!NOTE] 
> - Denna cmdlet kräver att du granskar och godkänner vår licens-och sekretess policy.
> - Instrumentation-motorn lägger till ytterligare kostnader och är inaktive rad som standard.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar

### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln för att godkänna licens-och sekretess policyn i konsolbaserade installationer.

### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.

## <a name="output"></a>Resultat


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exempel på utdata från har aktiverat Instrumentation Engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- Använd [Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrument paneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) för att se till att din webbplats hålls Live.
- [Lägg till telemetri för webb klienter](../../azure-monitor/app/javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings-och logg anrop.
 
 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) för att inspektera övervakning.
