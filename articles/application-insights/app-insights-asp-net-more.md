---
title: Få ut mer av Azure Application Insights | Microsoft Docs
description: Här är en sammanfattning av de funktioner som du kan utforska efter komma igång med Application Insights.
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
ms.openlocfilehash: 2f03083367de4e818bdc953ab76c28ff687f0a48
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294344"
---
# <a name="more-telemetry-from-application-insights"></a>Flera telemetri från Application Insights
När du har [lagt till Application Insights ASP.NET-koden](app-insights-asp-net.md), det finns några saker du kan göra för att få ännu mer telemetri. 

| Åtgärd | Vad du får|
|---|---|
|(IIS-servrar) [Installera statusövervakaren](http://go.microsoft.com/fwlink/?LinkId=506648) på varje server-dator.<br/>(Azure-webbappar) Öppna bladet Application Insights i Azure på Kontrollpanelen för webbprogrammet.| [**Prestandaräknare**](app-insights-performance-counters.md)<br/>[**Undantag** ](app-insights-asp-net-exceptions.md) – detaljerad Stacka spårningar<br/>[**Beroenden**](app-insights-asp-net-dependencies.md)|
|[Lägg till JavaScript-kodfragment på webbsidor](app-insights-javascript.md)|[Sidan prestanda](app-insights-web-track-usage.md), webbläsarundantag, AJAX-prestanda. Anpassad telemetri för klientsidan.|
|[Skapa tillgänglighetstester för webbprogram](app-insights-monitor-web-app-availability.md)|Få aviseringar om webbplatsen inte är tillgänglig|
|[Se till att buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) genereras av MSBuild|[Skapa anteckningar i mått diagram](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Skriva anpassade händelser och mått](app-insights-api-custom-events-metrics.md)|Antal företag händelser och mått, spåra detaljerad användning och mycket mer.|
|[Webbplatsen live-profil](https://aka.ms/AIProfilerPreview)|Detaljerad funktionen tidsinställningar från livewebbappar|






