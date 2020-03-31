---
title: Få ut mer av Azure Application Insights | Microsoft-dokument
description: När du har kommit igång med Application Insights följer här en sammanfattning av de funktioner du kan utforska.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666032"
---
# <a name="more-telemetry-from-application-insights"></a>Mer telemetri från Application Insights
När du har [lagt till Application Insights i din ASP.NET-kod](../../azure-monitor/app/asp-net.md)finns det några saker du kan göra för att få ännu mer telemetri. 

| Åtgärd | Vad du får|
|---|---|
|(IIS-servrar) [Installera statusövervakare](https://go.microsoft.com/fwlink/?LinkId=506648) på varje serverdator.<br/>(Azure-webbappar) Öppna bladet Application Insights på Azure-kontrollpanelen för webbappen.| [**Prestandaräknare**](../../azure-monitor/app/performance-counters.md)<br/>[**Undantag**](asp-net-exceptions.md) - detaljerade stackspårningar<br/>[**Beroenden**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Lägga till JavaScript-kodavsnittet på webbsidorna](../../azure-monitor/app/javascript.md)|[Sidprestanda,](../../azure-monitor/app/usage-overview.md)webbläsarundantag, AJAX-prestanda. Anpassad telemetri på klientsidan.|
|[Skapa webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)|Få aviseringar om webbplatsen blir otillgänglig|
|[Se buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) genereras av MSBuild|[Skapa anteckningar i måttdiagram](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Skriv anpassade händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md)|Räkna affärshändelser och mått, spåra detaljerad användning med mera.|
|[Profilera din livewebbplats](https://aka.ms/AIProfilerPreview)|Detaljerade funktionstimmingar från din live-webbapp|






