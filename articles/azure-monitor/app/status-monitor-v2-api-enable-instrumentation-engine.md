---
title: API-referens för Azure Application Insights-agent
description: Application Insights Agent-API-referens. Enable-InstrumentationEngine. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 796c2cc669e238499223d233cf4ddcf740af7c95
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899726"
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
