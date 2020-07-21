---
title: Få ut mer av Azure Application Insights | Microsoft Docs
description: När du har kommit igång med Application Insights här är en sammanfattning av de funktioner som du kan utforska.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540034"
---
# <a name="more-telemetry-from-application-insights"></a>Mer telemetri från Application Insights
När du har [lagt till Application Insights i ASP.net-koden](../../azure-monitor/app/asp-net.md)finns det några saker som du kan göra för att få ännu mer telemetri. 

| Action | Det här får du|
|---|---|
|(IIS-servrar) [Installera statusövervakare](https://go.microsoft.com/fwlink/?LinkId=506648) på varje Server dator.<br/>(Azure-Webbappar) Öppna bladet Application Insights på kontroll panelen i Azure för webb programmet.| [**Prestandaräknare**](../../azure-monitor/app/performance-counters.md)<br/>[**Undantag**](asp-net-exceptions.md) – detaljerade stack spår<br/>[**Beroenden**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Lägga till JavaScript-kodfragmentet på dina webb sidor](../../azure-monitor/app/javascript.md)|[Sid prestanda](../../azure-monitor/app/usage-overview.md), webb läsar undantag, Ajax-prestanda. Anpassad telemetri på klient sidan.|
|[Skapa webb test för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)|Få aviseringar om webbplatsen blir otillgänglig|
|[Se till att buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) genereras av MSBuild|[Bygg anteckningar i mått diagram](./annotations.md)
|[Skriv anpassade händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md)|Räkna affärs händelser och mått, spåra detaljerad användning med mera.|
|[Profilera din Live-webbplats](https://aka.ms/AIProfilerPreview)|Detaljerade funktions tider från din Live Web App|
