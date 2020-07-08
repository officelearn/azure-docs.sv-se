---
title: Få ut mer av Azure Application Insights | Microsoft Docs
description: När du har kommit igång med Application Insights här är en sammanfattning av de funktioner som du kan utforska.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77666032"
---
# <a name="more-telemetry-from-application-insights"></a>Mer telemetri från Application Insights
När du har [lagt till Application Insights i ASP.net-koden](../../azure-monitor/app/asp-net.md)finns det några saker som du kan göra för att få ännu mer telemetri. 

| Åtgärd | Det här får du|
|---|---|
|(IIS-servrar) [Installera statusövervakare](https://go.microsoft.com/fwlink/?LinkId=506648) på varje Server dator.<br/>(Azure-Webbappar) Öppna bladet Application Insights på kontroll panelen i Azure för webb programmet.| [**Prestandaräknare**](../../azure-monitor/app/performance-counters.md)<br/>[**Undantag**](asp-net-exceptions.md) – detaljerade stack spår<br/>[**Beroenden**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Lägga till JavaScript-kodfragmentet på dina webb sidor](../../azure-monitor/app/javascript.md)|[Sid prestanda](../../azure-monitor/app/usage-overview.md), webb läsar undantag, Ajax-prestanda. Anpassad telemetri på klient sidan.|
|[Skapa webb test för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)|Få aviseringar om webbplatsen blir otillgänglig|
|[Se till att buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) genereras av MSBuild|[Bygg anteckningar i mått diagram](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Skriv anpassade händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md)|Räkna affärs händelser och mått, spåra detaljerad användning med mera.|
|[Profilera din Live-webbplats](https://aka.ms/AIProfilerPreview)|Detaljerade funktions tider från din Live Web App|






