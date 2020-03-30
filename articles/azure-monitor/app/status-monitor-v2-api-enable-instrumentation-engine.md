---
title: API-referens för Azure Application Insights-agent
description: Api-referens för Application Insights Agent. Aktivera instrumenteringSengine. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671366"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Api för programinsikter agent: Aktivera instrumentationEngine

I den här artikeln beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Aktiverar instrumenteringsmotorn genom att ställa in några registernycklar.
Starta om IIS för att ändringarna ska börja gälla.

Instrumenteringsmotorn kan komplettera data som samlats in av .NET SDK:er.
Den samlar in händelser och meddelanden som beskriver körningen av en hanterad process. Dessa händelser och meddelanden inkluderar beroenderesultatkoder, [HTTP-verb och SQL-kommandotext](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Aktivera instrumenteringsmotorn om
- Du har redan aktiverat övervakning med cmdlet aktivera men inte aktiverat instrumenteringsmotorn.
- Du har instrumenterat appen manuellt med .NET SDK:er och vill samla in ytterligare telemetri.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet.

> [!NOTE] 
> - Denna cmdlet kräver att du granskar och accepterar vår licens och sekretesspolicy.
> - Instrumenteringsmotorn lägger till ytterligare omkostnader och är avstängd som standard.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar

### <a name="-acceptlicense"></a>-AccepteraLicens
**Valfri.** Använd den här växeln för att acceptera licens- och sekretesspolicyn i huvudlösa installationer.

### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att mata ut detaljerade loggar.

## <a name="output"></a>Resultat


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exempel på utdata från att framgångsrikt aktivera instrumenteringsmotorn

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Nästa steg

  Visa telemetrin:
 - [Utforska mätvärden](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- Använd [analyser](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).
 
 Lägg till mer telemetri:
 - [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
- [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal.
 
 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att dina inställningar har spelats in korrekt.
 - [Få status för](status-monitor-v2-api-get-status.md) att inspektera övervakningen.
