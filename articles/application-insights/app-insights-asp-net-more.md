---
title: Få ut mer av Azure Application Insights | Microsoft Docs
description: Efter att komma igång med Application Insights, är här en sammanfattning av de funktioner som du kan utforska.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: a1244076efe1c920a23f29de9d25ab6845747fe4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245859"
---
# <a name="more-telemetry-from-application-insights"></a>Mer telemetri från Application Insights
När du har [lagt till Application Insights för ASP.NET-koden](app-insights-asp-net.md), det finns några saker som du kan göra för att få ännu mer telemetri. 

| Åtgärd | Vad du får|
|---|---|
|(IIS-servrar) [Installera Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648) på varje server-datorn.<br/>(Azure-webbappar) Öppna Application Insights-bladet i Azure på Kontrollpanelen för webbappen.| [**Prestandaräknare**](app-insights-performance-counters.md)<br/>[**Undantag** ](app-insights-asp-net-exceptions.md) – detaljerad Stacka spårningar<br/>[**Beroenden**](app-insights-asp-net-dependencies.md)|
|[Lägg till JavaScript-kodavsnitt till dina webbsidor](app-insights-javascript.md)|[Sidan prestanda](app-insights-usage-overview.md), webbläsarundantag, prestanda för AJAX. Anpassad telemetri för klientsidan.|
|[Skapa webbtester för tillgänglighet](app-insights-monitor-web-app-availability.md)|Få aviseringar om din webbplats blir otillgänglig|
|[Se till att buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) genereras av MSBuild|[Skapa anteckningar i måttdiagram](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Skriva anpassade händelser och mått](app-insights-api-custom-events-metrics.md)|Antal företag händelser och mått, spåra detaljerad användning och mycket mer.|
|[Profilera live-webbplatsen](https://aka.ms/AIProfilerPreview)|Detaljerad funktionen tidsinställningar från live-webbapp|






