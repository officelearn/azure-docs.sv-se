---
title: 'Azure statusövervakaren v2 API-referens: Aktivera instrumentation motorn | Microsoft Docs'
description: Status Monitor v2 API-referens. Aktivera InstrumentationEngine. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514369"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Status Monitor v2 API: Aktivera InstrumentationEngine (v0.2.1-alfa)

Den här artikeln beskrivs en cmdlet som ingår i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Beskrivning

Kan instrumentation motorn genom att ange vissa registernycklar.
Starta om IIS för att ändringarna ska börja gälla.

Motorn för instrumentation komplettera data som samlas in av .NET-SDK: er.
Den samlar in händelser och meddelanden som beskriver körningen av en hanterad process. Dessa händelser och meddelanden innehåller beroende resultatkoder, HTTP-verb och SQL-Kommandotext.

Aktivera instrumentation motorn om:
- Du har redan aktiverat övervakning med cmdleten Enable men aktivera inte instrumentation motorn.
- Du har instrumenterat din app med .NET-SDK: er manuellt och vill samla in ytterligare telemetri.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörsbehörigheter.

> [!NOTE] 
> - Denna cmdlet kräver att du granskar och accepterar du våra licens och sekretess-instruktion.
> - Motorn för instrumentation lägger till ytterligare kostnader och är inaktiverat som standard.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar

### <a name="-acceptlicense"></a>-AcceptLicense
**Valfritt.** Använd den här växeln om du vill acceptera instruktionen licens och sekretess i fjärradministrerad installationer.

### <a name="-verbose"></a>-Verbose
**Vanliga parameter.** Använd den här växeln för att mata ut detaljerade loggar.

## <a name="output"></a>Resultat


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exempel på utdata från har aktiverat instrumentation motorn

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Hämta konfig](status-monitor-v2-api-get-config.md) att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) att inspektera övervakning.
